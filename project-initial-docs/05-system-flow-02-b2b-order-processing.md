# System Flow 2: B2B 全体プロセス: 代理店/注文 出荷/配送

## Mục tiêu

Quy trình xử lý đơn hàng BtoB có thể giao nhiều lần (分割出荷), đảm bảo hệ thống có thể:

- Giữ hàng (trừ tồn kho logic) ngay khi xác nhận đơn
- Theo dõi và trừ kho vật lý chi tiết theo từng **kệ (Location)** và **LOT sản xuất (LOT番号)**
- Cho phép vận hành viên tạo các lần giao hàng thủ công (手動再出荷)
- Đồng bộ hóa trạng thái giao hàng, invoice và tồn kho giữa các hệ thống

## Thành phần Hệ thống

| Thành phần | Vai trò |
| --- | --- |
| 🧑‍💼 **Sales_Staff** | Nhân viên phụ trách đăng ký đại lý và phê duyệt đơn hàng |
| 🗂️ **Partner_Master** | App quản lý thông tin đại lý (loại đại lý, chiết khấu, tài khoản) |
| 📄 **Orders_App** | App quản lý đơn hàng |
| 🙋‍♂️ **Agent** | Đại lý (người đặt hàng) |
| 🌐 **Portal** | Giao diện web dành cho đại lý để đặt và theo dõi đơn hàng |
| 🖥️ **Ruby_Server** | Server xử lý logic, tạo VA ID, điều phối các app |
| 🏦 **GMO_API** | API ngân hàng để tạo tài khoản ảo (VA) và xác nhận thanh toán |
| 🧾 **Invoice_App** | App quản lý hóa đơn và đối soát thanh toán |
| 📈 **Stock_App** | App quản lý **tồn kho logic (論理在庫)** |
| 📋 **Task_App** | App quản lý các task xuất nhập kho |
| 📱 **Warehouse_UI** | Màn hình xuất nhập kho thực tế (ReactJS) để nhân viên kho quét mã |
| 🏭 **Physical_Warehouse** | Hệ thống quản lý **tồn kho vật lý (実在庫)**, theo từng kệ và LOT |
| 🚚 **Ship&Co** | Hệ thống tạo vận đơn và tracking |
| 🧑‍💻 **Op_Staff** | Nhân viên vận hành, thực hiện thao tác thủ công khi cần |

## Quy trình Tổng thể

### 1️⃣ Đăng ký đại lý và tạo đơn hàng

1. Sales_Staff đăng ký đại lý trong **Partner_Master** (bao gồm loại đại lý, chiết khấu, tài khoản ngân hàng, v.v.)

2. Hệ thống tự động phát hành tài khoản Portal cho đại lý

3. Đại lý đăng nhập Portal, chọn sản phẩm, xác nhận giá (có áp chiết khấu tự động)

4. Đại lý gửi đơn hàng → hệ thống tạo bản ghi trong **Orders_App**

5. Sales_Staff phê duyệt nội bộ → trạng thái cập nhật thành `承認済み (Đã duyệt)`

---

### 2️⃣ Phát hành hóa đơn & xác nhận thanh toán

1. Khi đơn hàng được duyệt, hệ thống tạo **VA ID (Tài khoản ảo)** qua **GMO API**

2. Lưu VA ID vào **Invoice_App**, phát hành và gửi hóa đơn cho đại lý

3. Đại lý thanh toán qua ngân hàng → GMO API gửi webhook kết quả về hệ thống

4. **Ruby_Server** xác minh giao dịch:
   - Nếu **thành công** → cập nhật trạng thái đơn sang `支払い済み (Đã thanh toán)`
   - Nếu **thất bại** → cập nhật sang `入金エラー (Lỗi đối soát)`

---

### 3️⃣ Giữ hàng (trừ tồn kho logic)

1. Khi thanh toán thành công, hệ thống kích hoạt quá trình **引当 (Allocation)**:
   - **Stock_App** kiểm tra tồn kho logic
   - Nếu đủ → trừ tồn kho logic tương ứng và cập nhật trạng thái `引当済み`
   - Nếu thiếu → tách đơn thành hai phần:
     - **A**: Số lượng có thể giao ngay
     - **B**: Số lượng chờ sản xuất hoặc nhập thêm
     - A được trừ tồn logic, B giữ nguyên để chờ nhập kho bổ sung

---

### 4️⃣ Xuất kho phần đầu tiên (A分)

1. **Ruby_Server** tạo **Task xuất kho** trong **Task_App** cho phần A

2. Nhân viên kho mở **Warehouse_UI**, thấy danh sách hàng cần xuất

3. Với từng sản phẩm, nhân viên:
   - Quét **barcode sản phẩm**
   - Quét **mã kệ (Location)**
   - Nhập hoặc quét **LOT番号**

4. Khi xác nhận:
   - Hệ thống ghi lại chi tiết `{SKU, Location_ID, LOT_No, Quantity}`
   - Trừ tồn trong **Physical_Warehouse** đúng dòng (棚・LOT単位)
   - Gửi kết quả hoàn thành về **Task_App** và **Orders_App**

5. **Ruby_Server** gọi **Ship&Co API** để tạo vận đơn, nhận tracking number

6. Gửi thông tin giao hàng (tracking, ngày xuất, kệ, LOT) về Portal cho đại lý

---

### 5️⃣ Xuất kho phần còn lại (B分)

1. Khi có hàng bổ sung, nhân viên vận hành nhấn `出荷指示 (Chỉ thị xuất hàng)` trong Orders_App

2. Hệ thống tạo **Task mới** cho phần B trong Task_App

3. Nhân viên kho lại thực hiện quét **barcode – kệ – LOT** trên Warehouse_UI

4. Hệ thống trừ tồn vật lý đúng dòng `{SKU, Location_ID, LOT_No}`

5. Sau khi hoàn thành, Task_App cập nhật trạng thái về Orders_App và thông báo đến Portal của đại lý

---

### 6️⃣ Hoàn tất đơn hàng

1. Khi tất cả các phần xuất kho hoàn tất → Orders_App cập nhật trạng thái `出荷完了`

2. Portal hiển thị đầy đủ lịch sử giao hàng, tracking, và file nhãn vận chuyển PDF (từ Ship&Co)

---

## Logic Tồn kho

| Loại tồn kho | Thời điểm cập nhật | Nguồn dữ liệu | Mục đích |
| --- | --- | --- | --- |
| **論理在庫 (Logic Stock)** | Khi xác nhận đơn / thanh toán xong | Stock_App | Giữ hàng, tránh overbooking |
| **実在庫 (Physical Stock)** | Khi nhân viên kho xác nhận xuất trên Warehouse_UI | Physical_Warehouse | Quản lý hàng thực tế, theo **Kệ & LOT** |

### Cấu trúc Dữ liệu cho Physical_Warehouse

```json
{
  "SKU": "RS-500ML",
  "Product_Name": "Rice Silica 500mL",
  "Location_ID": "A-03",
  "LOT_No": "240101",
  "Quantity": 48,
  "Last_Updated": "2025-11-07T15:40:00Z",
  "Task_ID": "TASK-2025-1107-001"
}
```

## Sequence Diagram

```mermaid
sequenceDiagram
    title Flow BtoB: 分割出荷 + 手動再出荷 + 論理在庫確保 + 実在庫反映（棚・LOT単位）

    participant Sales_Staff as 🧑‍💼 営業担当
    participant Partner_Master as 🗂️ 代理店マスタ
    participant Orders_App as 📄 受注アプリ
    participant Agent as 🙋‍♂️ 代理店
    participant Portal as 🌐 代理店ポータル
    participant Ruby_Server as 🖥️ ロジックサーバー
    participant GMO_API as 🏦 GMO銀行 API
    participant Invoice_App as 🧾 請求アプリ
    participant Stock_App as 📈 在庫アプリ（論理在庫）
    participant Task_App as 📋 タスクアプリ（出荷指示）
    participant Warehouse_UI as 📱 倉庫端末（ReactJS）
    participant Physical_Warehouse as 🏭 実在庫倉庫（棚・LOT別在庫）
    participant ShipCo as 🚚 Ship&Co
    participant Op_Staff as 🧑‍💻 運用担当者

    %% === 第1段階: 代理店登録と受注作成 ===
    Sales_Staff->>Partner_Master: 1. 代理店登録（掛け率・支払条件）
    Partner_Master->>Ruby_Server: [Trigger] ポータルアカウント発行
    Ruby_Server->>Agent: 2. ログイン情報送信

    Agent->>Portal: 3. 商品選択・発注依頼
    Portal->>Ruby_Server: 4. 掛け率を取得し価格計算
    Ruby_Server-->>Portal: 5. 割引後価格表示
    Agent->>Portal: 6. 注文確定（Submit）
    Portal->>Orders_App: 7. 受注レコード作成（合計数量）

    Orders_App->>Sales_Staff: 8. 社内承認フロー開始
    Sales_Staff->>Orders_App: 9. 承認完了（Status=承認済み）

    %% === 第2段階: 請求発行と入金処理 ===
    Orders_App->>Ruby_Server: [Trigger] 請求書発行
    Ruby_Server->>GMO_API: 10. VA口座発行
    GMO_API-->>Ruby_Server: 11. VA ID受信
    Ruby_Server->>Invoice_App: 12. 請求レコード作成（VA ID含む）
    Invoice_App->>Agent: 13. 請求書送付（Portal + Email）

    Agent->>GMO_API: 14. 銀行振込（VA ID宛）
    loop Webhook / Polling
        GMO_API->>Ruby_Server: 15. 入金明細送信
    end
    Ruby_Server->>Invoice_App: 16. 入金照合処理
    alt 入金成功
        Invoice_App->>Orders_App: 17a. ステータス=支払い済み
    else 入金失敗
        Invoice_App->>Orders_App: 17b. ステータス=入金エラー
    end

    %% === 第3段階: 論理在庫確保（引当） ===
    Orders_App->>Ruby_Server: [Trigger] 在庫引当処理
    Ruby_Server->>Stock_App: 18. 論理在庫確認
    alt 在庫不足
        Ruby_Server->>Orders_App: 19a. 受注分割（A: 出荷可, B: 残）
        Ruby_Server->>Stock_App: 20a. A分の数量を**論理在庫から減算**（確保）
    else 在庫十分
        Ruby_Server->>Stock_App: 19b. 全数量を**論理在庫から減算**（確保）
        Ruby_Server->>Orders_App: 20b. ステータス=引当済み/出荷準備完了
    end

    %% === 第4段階: 出荷タスク作成と実在庫処理 ===
    alt 部分出荷（A分）
        Ruby_Server->>Task_App: 21a. 出荷タスク作成（A分）
        Task_App->>Warehouse_UI: 22a. 倉庫端末に表示（出荷リスト）
        Warehouse_UI->>Physical_Warehouse: 23a. 商品バーコード・棚（Location）・LOTスキャン
        note right of Physical_Warehouse: 24a. スキャンした棚・LOT単位で出庫数量を確定
        Physical_Warehouse->>Physical_Warehouse: 25a. **実在庫を減算（棚・LOT単位）**
        Warehouse_UI->>Task_App: 26a. タスク完了報告（棚・LOT情報付き）
        Task_App->>Ruby_Server: 27a. [Webhook] 出荷完了
        Ruby_Server->>ShipCo: 28a. 配送ラベル発行API
        ShipCo-->>Ruby_Server: 29a. 追跡番号受信
        Ruby_Server->>Orders_App: 30a. 出荷情報更新（A分・LOT/棚情報含む）
        Ruby_Server->>Agent: 31a. Portalに配送ステータス反映
    end

    %% === 第5段階: 残分の手動出荷 ===
    note over Stock_App, Op_Staff: B分入庫後に手動出荷指示を行う
    Op_Staff->>Orders_App: 32. 「残分出荷指示」ボタン押下
    Orders_App->>Task_App: 33. 出荷タスク作成（B分）
    Task_App->>Warehouse_UI: 34. 倉庫端末でB分表示
    Warehouse_UI->>Physical_Warehouse: 35. 棚・バーコード・LOTスキャン
    note right of Physical_Warehouse: 36. スキャンした棚・LOT別に実在庫更新
    Physical_Warehouse->>Physical_Warehouse: 37. **実在庫を減算（棚・LOT単位）**
    Warehouse_UI->>Task_App: 38. タスク完了報告
    Task_App->>Ruby_Server: 39. [Webhook] 出荷完了（B分）
    Ruby_Server->>ShipCo: 40. 配送ラベル発行
    ShipCo-->>Ruby_Server: 41. 追跡番号返却
    Ruby_Server->>Orders_App: 42. 出荷情報更新（B分完了）
    Ruby_Server->>Agent: 43. Portal更新（全出荷完了）

    %% === 第6段階: 完了 ===
    ShipCo->>Agent: 44. 出荷完了通知（追跡番号含む）
    Agent->>Portal: 45. 出荷履歴とラベルPDFを確認
```

## Xử lý Ngoại lệ

| Trường hợp | Hành động |
| --- | --- |
| **Đơn hàng bị huỷ (キャンセル)** | Nếu **chưa xuất kho** → huỷ task và hoàn lại tồn logic.<br>Nếu **đã xuất kho** → tạo task nhập kho (Return Task) và tăng lại tồn vật lý theo đúng kệ, LOT |
| **Sai lệch kho** | Thực hiện kiểm kê định kỳ theo kệ (Location Audit) |
| **Lỗi thanh toán** | Ghi log trong Payment Error App và xử lý thủ công |

## Trạng thái Đơn hàng B2B

| Trạng thái | Mô tả | Ghi chú |
| --- | --- | --- |
| `承認待ち` | Chờ phê duyệt | Đơn hàng mới được tạo, chờ Sales_Staff duyệt |
| `承認済み` | Đã duyệt | Đã được Sales_Staff phê duyệt, chờ phát hành hóa đơn |
| `請求書発行済み` | Đã phát hành hóa đơn | Đã tạo VA ID và gửi hóa đơn cho đại lý |
| `支払い済み` | Đã thanh toán | Đã nhận thanh toán qua GMO API |
| `入金エラー` | Lỗi đối soát | Thanh toán thất bại hoặc không khớp |
| `引当済み` | Đã giữ hàng | Đã trừ tồn kho logic |
| `部分引当済み` | Giữ hàng một phần | Chỉ giữ được một phần (A分), còn lại chờ nhập kho |
| `出荷タスク作成済み` | Đã tạo task xuất kho | Task đang chờ nhân viên kho xử lý |
| `部分出荷完了` | Hoàn tất xuất kho một phần | Đã xuất A分, còn B分 chờ |
| `出荷完了` | Hoàn tất xuất kho | Tất cả các phần đã xuất kho xong |

## Tính năng Đặc biệt

### 分割出荷 (Giao hàng nhiều lần)
- Hệ thống tự động tách đơn khi thiếu tồn kho
- Mỗi lần giao hàng có tracking number riêng
- Đại lý có thể theo dõi từng lần giao hàng trên Portal

### 手動再出荷 (Xuất kho thủ công)
- Nhân viên vận hành có thể tạo task xuất kho cho phần còn lại (B分)
- Hệ thống lưu lịch sử tất cả các lần xuất kho
- Mỗi lần xuất kho đều ghi nhận Location và LOT

### Truy xuất nguồn gốc (Traceability)
- Mỗi sản phẩm xuất kho đều ghi nhận:
  - Location (vị trí kệ)
  - LOT番号 (số lô sản xuất)
  - Ngày xuất kho
  - Task ID
- Cho phép truy xuất ngược từ sản phẩm đến lô sản xuất

## Kết quả Mong đợi

- ✅ Hệ thống có thể theo dõi chính xác **tồn kho logic và vật lý tách biệt**
- ✅ Có khả năng truy xuất nguồn gốc sản phẩm đến **LOT & vị trí lưu kho**
- ✅ Các task xuất hàng, nhập hàng, hoàn hàng được ghi nhận và có thể audit
- ✅ Đại lý có thể xem trạng thái giao hàng và tracking theo thời gian thực trên Portal
- ✅ Hỗ trợ giao hàng nhiều lần khi thiếu tồn kho
- ✅ Quản lý thanh toán qua VA (Virtual Account) tự động

## So sánh Flow 1 (B2C) và Flow 2 (B2B)

| Đặc điểm | Flow 1 (B2C) | Flow 2 (B2B) |
| --- | --- | --- |
| **Nguồn đơn hàng** | Shopify (qua Yoom) | Portal đại lý |
| **Phê duyệt** | Tự động | Cần Sales_Staff phê duyệt |
| **Thanh toán** | Tự động qua Shopify | Qua VA (GMO API) |
| **Giao hàng nhiều lần** | Không | Có (分割出荷) |
| **Chiết khấu** | Không | Có (theo loại đại lý) |
| **Truy xuất LOT** | Có | Có (chi tiết hơn) |

## Liên kết Flow Khác

- **Flow 1**: EC注文の自動取込・在庫引当と出荷処理 (Quy trình B2C)
- **Flow 9**: Xử lý Cancel / Return đơn hàng

