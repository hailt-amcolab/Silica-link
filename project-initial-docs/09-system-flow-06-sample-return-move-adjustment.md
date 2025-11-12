# System Flow 6: サンプル出荷・返品・棚移動・在庫調整

（Quy trình xuất mẫu, hoàn hàng, di chuyển kệ và điều chỉnh tồn kho – dùng ReactJS Barcode Scan）

## Mục tiêu

Thiết lập quy trình vận hành thống nhất cho toàn bộ nghiệp vụ xuất nhập kho nội bộ, bao gồm:

- **返品 (Hoàn hàng)**: nhập hàng hoàn trả sau khi bán.
- **サンプル出荷 (Xuất mẫu)**: xuất kho dùng thử hoặc gửi mẫu.
- **棚移動 (Di chuyển kệ)**: chuyển hàng giữa các vị trí trong cùng kho.
- **在庫調整 (Điều chỉnh kho)**: xử lý sai lệch, chênh lệch kiểm kê hoặc sửa tồn thực tế.

Tất cả các thao tác này được thực hiện qua **ReactJS Warehouse UI** (màn hình quét barcode), và dữ liệu được đồng bộ tức thời giữa:

- **Kintone** (luồng nghiệp vụ và tồn logic)
- **Physical_DB** (tồn vật lý thực tế theo kệ, LOT)

## Thành phần tham gia

| Thành phần | Vai trò | Ghi chú |
| --- | --- | --- |
| 🧑‍💼 Sales_Staff / 管理者 | Người tạo hoặc phê duyệt yêu cầu di chuyển, xuất mẫu, hoàn hàng | Thực hiện trực tiếp trong Kintone |
| 📄 StockMovement_App | App trung tâm lưu record các hoạt động di chuyển/điều chỉnh/hoàn hàng | Trường: SKU, Qty, From/To Shelf, Type, Reason, LOT |
| 🖥️ Ruby_Server | Xử lý logic tồn kho (điều chỉnh logic, đồng bộ Physical_DB) | Trigger bởi sự kiện record save/update |
| 📱 Warehouse_UI (ReactJS) | Màn hình quét barcode tại kho | Nhập mã hàng, kệ, LOT và xác nhận kết quả |
| 🏭 Physical_DB | CSDL quản lý tồn vật lý chi tiết theo từng kệ (Location) và LOT | Dữ liệu thực tế tại kho |
| 📈 Stock_App | App tồn kho logic trên Kintone | Dữ liệu logic cho báo cáo và hệ thống |

## Quy trình tổng thể

### 1️⃣ Tạo yêu cầu di chuyển hoặc hoàn hàng

- Sales_Staff tạo mới một record trong `StockMovement_App`
- Nhập các thông tin:
  - SKU, Số lượng
  - Loại giao dịch (Type):
    - `RETURN` → Nhập hàng hoàn trả
    - `SAMPLE_OUT` → Xuất mẫu
    - `LOCATION_TRANSFER` → Di chuyển kệ
    - `ADJUSTMENT` → Điều chỉnh kho
  - Vị trí kệ From / To (nếu có)
  - LOT và lý do (tùy chọn)
- Sau khi lưu, Kintone trigger Webhook → Ruby_Server

---

### 2️⃣ Gửi task đến màn hình kho (Warehouse_UI)

- Ruby_Server tạo một “task” chứa: SKU, số lượng, vị trí From/To, loại giao dịch
- Task hiển thị trên `Warehouse_UI` (ReactJS Barcode Scan)
- Nhân viên kho sẽ:
  - Quét barcode sản phẩm
  - Nhập hoặc quét mã kệ (Shelf Code)
  - Nhập hoặc quét mã LOT
  - Xác nhận số lượng thực tế

---

### 3️⃣ Thực thi logic tồn kho (Logic + Physical)

| Type | Hành động vật lý | Hành động logic | Cập nhật trạng thái |
| --- | --- | --- | --- |
| 返品 (Return) | Tăng tồn thực tế trong `Physical_DB` theo kệ & LOT | Tăng tồn logic trong `Stock_App` | `返品完了` |
| サンプル出荷 (Sample Out) | Giảm tồn thực tế (xuất khỏi kệ) | Giảm tồn logic (ghi nhận tiêu hao) | `出荷完了` |
| 棚移動 (Location Transfer) | Giảm ở kệ A → Tăng ở kệ B (cùng LOT) | Không đổi tồn logic | `移動完了` |
| 在庫調整 (Adjustment) | Tăng/giảm thực tế theo số nhập | Điều chỉnh tồn logic tương ứng (±Qty) | `調整完了` |

---

### 4️⃣ Lưu lịch sử và thông báo

- Ruby_Server ghi lại vào `StockMovement_App`:
  - Kết quả thực tế (LOT, shelf, số lượng, thời gian)
  - Người thao tác
- Gửi thông báo hoàn tất qua Slack/Email đến Sales_Staff
- Có thể export báo cáo giao dịch để kiểm soát định kỳ

---

## Cấu trúc dữ liệu mẫu (StockMovement_App)

| Trường | Kiểu dữ liệu | Mô tả |
| --- | --- | --- |
| Record ID | Auto | ID của bản ghi |
| Type | Enum | `RETURN / SAMPLE_OUT / LOCATION_TRANSFER / ADJUSTMENT` |
| SKU | Text | Mã sản phẩm |
| Quantity | Number | Số lượng thao tác |
| From_Location | Text | Kệ xuất |
| To_Location | Text | Kệ nhập |
| LOT | Text | LOT sản xuất hoặc nhập hàng |
| Reason | Text | Lý do thao tác |
| Operator | User | Nhân viên kho thực hiện |
| Status | Enum | `完了 / 未完了 / エラー` |
| Executed_At | DateTime | Thời điểm hoàn tất |

---

## Lưu ý vận hành

- `ADJUSTMENT` và `RETURN` dùng cho các tình huống đặc biệt (hủy đơn sau xuất kho, kiểm kê lệch, hàng lỗi)
- `Warehouse_UI` có thể vận hành offline (cache local), sau đó đồng bộ lại khi có mạng
- Định kỳ 1 lần/tháng: báo cáo “棚卸 (Inventory Audit)” đối chiếu Logic vs Physical

---

## Sequence Diagram

```mermaid
sequenceDiagram
    title Flow Y: サンプル出荷・棚移動・在庫調整（ReactJSスキャンUI・単倉庫）

    participant Sales_Staff as 🧑‍💼 営業担当/管理者
    participant StockMovement_App as 📄 在庫移動アプリ(Kintone)
    participant Ruby_Server as 🖥️ Ruby Server
    participant Stock_App as 📈 論理在庫(Kintone)
    participant Warehouse_UI as 📱 倉庫端末(ReactJS)
    participant Physical_DB as 🏭 実在庫DB(棚・LOT)

    note over Sales_Staff, Warehouse_UI: 目的: サンプル出荷／棚移動／在庫調整をBarcodeで実在庫＋論理在庫に反映

    %% ステップ1: 依頼登録
    Sales_Staff->>StockMovement_App: 1. レコード作成 (SKU/Qty/棚/種別/理由/LOT)
    StockMovement_App->>Ruby_Server: 2. [Webhook] 移動ロジック起動
    Ruby_Server->>Warehouse_UI: 3. タスク送信 (SKU/棚/数量/種別)

    %% ステップ2: 倉庫スキャン
    Warehouse_UI->>Warehouse_UI: 4. バーコード/棚コード/LOTをスキャン

    %% ステップ3: 在庫反映
    alt SAMPLE_OUT
        Warehouse_UI->>Physical_DB: 5a. 実在庫を減算(棚・LOT)
        Ruby_Server->>Stock_App: 6a. 論理在庫を減算
        Ruby_Server->>StockMovement_App: 7a. ステータス=出荷完了
    else LOCATION_TRANSFER
        Warehouse_UI->>Physical_DB: 5b. 出庫棚を減算
        Warehouse_UI->>Physical_DB: 6b. 入庫棚を加算 (同LOT)
        Ruby_Server->>Stock_App: 7b. 論理在庫=変化なし
        Ruby_Server->>StockMovement_App: 8b. ステータス=移動完了
    else ADJUSTMENT
        Warehouse_UI->>Physical_DB: 5c. 実在庫±調整
        Ruby_Server->>Stock_App: 6c. 論理在庫±同期
        Ruby_Server->>StockMovement_App: 7c. ステータス=調整完了
    else RETURN
        Warehouse_UI->>Physical_DB: 5d. 実在庫を加算(棚・LOT)
        Ruby_Server->>Stock_App: 6d. 論理在庫を加算
        Ruby_Server->>StockMovement_App: 7d. ステータス=返品完了
    end

    %% ステップ4: 記録・通知
    Ruby_Server->>StockMovement_App: 8. 実績(棚/LOT/数量/時刻)を記録
    StockMovement_App->>Sales_Staff: 9. 完了通知(Slack/Email)

    note over Stock_App, Physical_DB: ✅ 実在庫・論理在庫・履歴が整合
```

## Kết quả cuối cùng

- Quản lý đồng nhất xuất mẫu, hoàn hàng, di chuyển kệ, điều chỉnh tồn kho
- `StockMovement_App` là trung tâm lịch sử; `Ruby_Server` đồng bộ hai chiều Logic/Physical
- Tối ưu thao tác nhờ Barcode Scan trên `Warehouse_UI`


