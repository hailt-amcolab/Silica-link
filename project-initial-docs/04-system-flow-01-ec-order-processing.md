# System Flow 1: EC注文の自動取込・在庫引当と出荷処理

## Mục tiêu

Luồng này mô tả quá trình từ khi đơn hàng được tạo trên **Shopify** đến khi hàng được giao đi, bao gồm:

- Đồng bộ đơn hàng từ Shopify sang Kintone (Orders App)
- Giữ hàng bằng **tồn kho logic (論理在庫)** khi đơn hàng được xác nhận
- Tạo **task xuất kho (出荷タスク)** cho nhân viên kho xử lý thực tế
- Trừ **tồn kho vật lý (実在庫)** khi hàng được quét và xuất kho
- Phát hành vận đơn qua **Ship&Co** và đồng bộ trạng thái trở lại Shopify
- Xử lý ngoại lệ: **Cancel / Return** (chi tiết flow 9)

## Thành phần Hệ thống

| Thành phần | Vai trò |
| --- | --- |
| 🛒 **Shopify (EC_Channel)** | Nguồn đơn hàng BtoC, nơi phát sinh order gốc |
| 🔗 **Yoom** | Công cụ đồng bộ (Webhook/Batch) truyền dữ liệu từ Shopify về server |
| 🖥️ **Ruby_Server (Middleware)** | Server trung gian xử lý logic, tạo record, quản lý API và điều phối hệ thống |
| 📄 **Orders_App** | App Kintone lưu đơn hàng, trạng thái, tracking |
| 📈 **Logic_Stock** | App Kintone quản lý tồn kho logic (luồng hệ thống) |
| 🗃️ **Physical_DB** | CSDL quản lý tồn kho vật lý (hàng thực tế tại kho) |
| 🧾 **Task_App** | App Kintone quản lý các task xuất hàng / nhập hàng |
| 📱 **Warehouse_UI** | Giao diện ReactJS để nhân viên kho quét barcode, xác nhận xuất / nhập |
| 🚚 **Ship&Co** | API phát hành vận đơn, in nhãn và gửi tracking |
| ☁️ **S3_Storage** | Lưu trữ file nhãn vận chuyển (PDF) |
| 🧑‍💻 **Op_Staff** | Nhân viên vận hành: xử lý Cancel / Return thủ công |

## Quy trình Chi tiết

### Giai đoạn 1 – Nhận đơn và giữ hàng (論理在庫引当)

1. Khi có đơn mới trên **Shopify**, hệ thống **Yoom** gọi Webhook gửi dữ liệu sang **Ruby_Server**

2. Ruby_Server chuẩn hóa dữ liệu và tạo **record đơn hàng** trong `Orders_App`

3. Ruby_Server gọi `Logic_Stock` để kiểm tra tồn kho logic theo SKU

4. Nếu **thiếu hàng** → cập nhật status: `要在庫確認 (Cần kiểm tra tồn kho)`

5. Nếu **đủ hàng** → cập nhật status: `受注確定 (Đơn đã xác nhận)` và **trừ tồn kho logic** tương ứng

> **Mục đích**: Đảm bảo hệ thống giữ lại lượng hàng dành cho đơn này, tránh oversell

---

### Giai đoạn 2 – Tạo task xuất kho và thao tác thực tế tại kho

1. Sau khi đơn được xác nhận và giữ hàng logic xong, Ruby_Server tạo **出荷タスク (Shipping Task)** trong `Task_App`
   - Thông tin gồm: OrderID, SKU, số lượng, vị trí kệ (Location), ghi chú đặc biệt nếu có

2. Task hiển thị trên giao diện `Warehouse_UI` cho nhân viên kho

3. Nhân viên kho quét **barcode sản phẩm** và **mã kệ (棚コード)** → xác nhận xuất kho

4. Khi xác nhận hoàn tất, hệ thống trừ **tồn kho vật lý (実在庫)** trong `Physical_DB` theo SKU, Location, LOT

5. `Task_App` nhận kết quả từ Physical_DB và cập nhật trạng thái task = `完了済み (Hoàn tất)`

---

### Giai đoạn 3 – Phát hành nhãn vận chuyển & đồng bộ

1. Khi Task hoàn tất, `Task_App` gửi webhook đến `Ruby_Server`

2. Ruby_Server gọi API `Ship&Co` để phát hành vận đơn

3. Ship&Co trả về **tracking number** và **file PDF nhãn**

4. Ruby_Server lưu file nhãn lên **S3 Storage**

5. Cập nhật thông tin tracking và link nhãn về `Orders_App`

---

### Giai đoạn 4 – Đồng bộ với Shopify

1. Ship&Co gửi thông tin vận chuyển (Fulfilled / Tracking) trực tiếp lên Shopify

2. Shopify gửi webhook ngược về để cập nhật trạng thái `Delivered` trong `Orders_App`

---

### Giai đoạn 5 – Xử lý Cancel / Return

| Trường hợp | Mô tả xử lý |
| --- | --- |
| **Cancel (Huỷ đơn)** | Tham khảo Flow 9 |
| **Return (Trả hàng)** | Tham khảo Flow 9 |

---

## Logic Tồn kho

| Loại tồn kho | Cập nhật khi | Mục đích |
| --- | --- | --- |
| **論理在庫 (Logic Stock)** | Khi xác nhận đơn / huỷ đơn / nhập lại hàng | Giữ hàng trong hệ thống, ngăn oversell |
| **実在庫 (Physical Stock)** | Khi nhân viên kho xuất hoặc nhập hàng thực tế | Quản lý tồn kho vật lý (棚・LOT単位) |

## Sequence Diagram

```mermaid
sequenceDiagram
    title Flow 1: Shopify注文取込〜出荷タスク処理 (ロジック在庫／物理在庫分離)

    participant EC_Channel as 🛒 Shopify (ECチャネル)
    participant Yoom as 🔗 Yoom (Webhook/Batch)
    participant Ruby_Server as 🖥️ システムサーバー (Middleware)
    participant Orders_App as 📄 Kintone 受注アプリ
    participant Logic_Stock as 📈 Kintone 論理在庫アプリ
    participant Physical_DB as 🗃️ 実在庫DB (物理)
    participant Task_App as 🧾 Kintone タスクアプリ（出荷指示）
    participant Warehouse_UI as 📱 倉庫担当者端末
    participant ShipCo as 🚚 Ship&Co
    participant S3_Storage as ☁️ S3 (配送ラベル)
    participant Op_Staff as 🧑‍💻 運用担当者

    EC_Channel->>Yoom: 新規注文発生 (Webhook／Batch)
    Yoom->>Ruby_Server: 1. 受注データペイロード送信
    Ruby_Server->>Orders_App: 2. 受注アプリにレコード作成
    Ruby_Server->>Logic_Stock: 3. SKU別在庫引当ロジック実行
    
    alt 在庫不足
        Ruby_Server->>Orders_App: ステータス "要在庫確認"
    else 在庫十分
        Ruby_Server->>Orders_App: ステータス "受注確定"
        Ruby_Server->>Logic_Stock: 論理在庫を減算
    end

    Ruby_Server->>Task_App: 出荷タスクを作成
    Task_App->>Warehouse_UI: 担当者端末でタスク表示
    Warehouse_UI->>Physical_DB: 実在庫を減算 (物理出庫)
    Physical_DB-->>Task_App: 出庫完了 (status=完了済み)

    Task_App->>Ruby_Server: 完了イベント送信
    Ruby_Server->>ShipCo: Ship&Co API呼出
    ShipCo-->>Ruby_Server: 追跡番号 & PDFラベル返却
    Ruby_Server->>S3_Storage: ラベルPDF保存
    Ruby_Server->>Orders_App: 追跡番号添付
    ShipCo->>EC_Channel: Shopifyへ配送情報同期
    EC_Channel->>Orders_App: ステータス更新 (Delivered)
```

## Kết quả Mong đợi

- ✅ Đơn hàng được xử lý theo đúng chu trình: Shopify → Kintone → Kho → Ship&Co → Shopify
- ✅ Hệ thống tách biệt rõ **tồn kho logic** và **tồn kho vật lý**, đảm bảo dữ liệu khớp
- ✅ Nhân viên kho thao tác qua **Warehouse_UI** để ghi nhận chính xác xuất/nhập hàng
- ✅ Có thể xử lý được các trường hợp đặc biệt: **Cancel / Return** một cách tự động hoặc bán tự động
- ✅ Tất cả dữ liệu (order, stock, task, tracking) được đồng bộ hai chiều, giảm sai lệch thủ công

## Trạng thái Đơn hàng

| Trạng thái | Mô tả | Ghi chú |
| --- | --- | --- |
| `要在庫確認` | Cần kiểm tra tồn kho | Khi thiếu hàng trong Logic_Stock |
| `受注確定` | Đơn đã xác nhận | Đã giữ hàng logic, chờ tạo task xuất kho |
| `出荷タスク作成済み` | Đã tạo task xuất kho | Task đang chờ nhân viên kho xử lý |
| `出庫完了` | Hoàn tất xuất kho | Đã trừ tồn kho vật lý |
| `配送ラベル発行済み` | Đã phát hành nhãn vận chuyển | Có tracking number |
| `Delivered` | Đã giao hàng | Cập nhật từ Shopify webhook |

## Xử lý Lỗi và Ngoại lệ

### Khi thiếu tồn kho logic
- Đơn hàng được đánh dấu `要在庫確認`
- Gửi thông báo cho nhân viên vận hành
- Nhân viên kiểm tra và quyết định: hủy đơn hoặc nhập thêm hàng

### Khi lỗi phát hành vận đơn
- Retry logic: thử lại 3 lần với exponential backoff
- Nếu vẫn lỗi: gửi cảnh báo cho Op_Staff xử lý thủ công

### Khi đồng bộ Shopify thất bại
- Lưu log lỗi vào hệ thống
- Có thể đồng bộ lại thủ công qua admin panel

## Liên kết Flow Khác

- **Flow 9**: Xử lý Cancel / Return đơn hàng (chi tiết quy trình hủy đơn và trả hàng)

