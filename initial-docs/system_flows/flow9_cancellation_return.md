# Flow9: 全体／一部キャンセル・返品

### 🎯 Mục tiêu

Mô tả quy trình xử lý các tình huống **hủy, trả hàng (toàn phần / một phần)** cho cả đơn bán (Order) và đơn mua (PO), bao gồm:

- Quản lý tồn kho logic & vật lý thông qua **Adjustment_App**
- Cập nhật trạng thái đơn hàng / đơn mua
- Điều chỉnh hoa hồng (commission) thủ công khi đã phát sinh chi trả ở kỳ trước
- Hợp nhất toàn bộ xử lý hoàn – hủy – trả hàng trong một quy trình duy nhất, dễ vận hành và kiểm soát

Mục tiêu là đảm bảo dữ liệu tồn kho, đơn hàng và hoa hồng **luôn khớp** nhau mà không cần logic phức tạp hoặc automation Ruby ở giai đoạn MVP.

---

### 🧩 Thành phần hệ thống liên quan

| Thành phần | Vai trò |
| --- | --- |
| **Orders_App (受注アプリ)** | Quản lý thông tin đơn bán, trạng thái, số lượng, lý do hủy/trả |
| **PO_App (購買アプリ)** | Quản lý đơn mua hàng, số lượng nhập và tình trạng hủy/trả |
| **Adjustment_App (調整アプリ)** | Ghi nhận các nghiệp vụ điều chỉnh kho (返品・廃棄・サンプル・棚卸差異) |
| **Stock_App (在庫アプリ)** | Theo dõi tồn kho logic, cập nhật khi có điều chỉnh |
| **Physical_Stock_app (実在庫DB)** | Quản lý tồn kho vật lý chi tiết theo棚 (kệ) và LOT番号 |
| **Commission_App (コミッションアプリ)** | Quản lý và điều chỉnh hoa hồng thủ công sau khi hoàn/trả |
| **Op_Staff (運用担当者)** | Nhân viên vận hành thực hiện điều chỉnh bằng tay trong các app |
| **EC_Channel / Mekaポータル** | Nơi khách hàng / nhà cung cấp thực hiện mua, hủy hoặc trả hàng |

---

### ⚙️ Quy trình tổng thể

### ① Đơn bán (Sales Order)

| Trường hợp | Hành động | Ảnh hưởng | Ghi chú |
| --- | --- | --- | --- |
| **Hủy toàn phần – Chưa xuất kho** | Cập nhật `Status = キャンセル` trong Orders_App | Không ảnh hưởng kho | Lưu ngày & lý do |
| **Hủy một phần – Chưa xuất kho** | Cập nhật `キャンセル数量`, `Status = 部分キャンセル` | Không ảnh hưởng kho | Giữ nguyên phần còn lại |
| **Hủy toàn phần – Đã xuất kho** | Tạo record trong Adjustment_App `Type=返品`, `Qty=+Số lượng` | Cộng lại tồn kho logic + vật lý | `Reason = 出荷後キャンセル` |
| **Hủy một phần – Đã xuất kho** | Adjustment_App `Type=返品`, `Qty=+phần hủy` | Cộng lại tồn tương ứng | `Reason = 部分返品` |
| **Xuất mẫu / Hủy hàng / Sử dụng nội bộ** | Adjustment_App `Type=廃棄／サンプル出庫` | Giảm tồn kho logic + vật lý | Không liên quan commission |

### ➕ Điều chỉnh hoa hồng (Commission)

Nếu đơn hàng đã được tính hoặc trả commission, khi phát sinh hoàn/trả:

- Nhân viên vận hành mở **Commission_App** và tạo bản ghi điều chỉnh thủ công:
    - `Type = 調整(マイナス)`
    - `金額 = -Hoa hồng đã trả`
    - `Reason = キャンセル補正／返品補正`
    - `次回期適用 = true`
- Nếu có phát sinh cộng thêm (ví dụ đổi hàng hoặc mua bổ sung):
    - `Type = 調整(プラス)`
    - `金額 = +Phần bổ sung`
    - `Reason = 交換対応／追加入庫`
- Tất cả record điều chỉnh sẽ được phản ánh ở kỳ chi trả kế tiếp.

---

### ② Đơn mua (Purchase Order)

| Trường hợp | Hành động | Ảnh hưởng | Ghi chú |
| --- | --- | --- | --- |
| **Hủy toàn phần – Chưa nhập kho** | Cập nhật `Status = キャンセル` | Không ảnh hưởng kho | Lưu ngày & lý do |
| **Hủy một phần – Chưa nhập kho** | Cập nhật `入庫予定数` hoặc `キャンセル数量` | Không ảnh hưởng kho | Giữ nguyên phần còn lại |
| **Trả toàn phần – Sau khi nhập kho** | Tạo record trong Adjustment_App `Type=返品`, `Qty=-Số lượng` | Giảm tồn kho logic + vật lý | `Reason = 不良返品` |
| **Trả một phần – Sau khi nhập kho** | Adjustment_App `Type=返品`, `Qty=-phần trả` | Giảm tương ứng | `Reason = 一部返品` |
| **Sai lệch tồn kho / Kiểm kê** | Adjustment_App `Type=棚卸差異`, `Qty=±X` | Điều chỉnh tồn kho vật lý & logic | `Reason = 棚卸修正` |

### 🔄 Luồng hoạt động tổng quát

```mermaid
sequenceDiagram
    title 🧾 Flow: 注文・購買におけるキャンセル／返品 + コミッション調整（全体・一部）MVP版

    participant EC_Channel as 🛒 ECチャネル / Mekaポータル
    participant Orders_App as 📄 Kintone 受注アプリ（販売）
    participant PO_App as 📝 Kintone 購買アプリ（仕入）
    participant Adjustment_App as ⚖️ Kintone 調整アプリ（入出庫修正）
    participant Stock_App as 📈 Kintone 在庫アプリ（論理）
    participant Physical_DB as 🏭 実在庫DB（棚・LOT単位）
    participant Commission_App as 💰 Kintone コミッションアプリ
    participant Op_Staff as 🧑‍💻 運用担当者

    %% === 第一部：販売注文（ORDER） ===
    note over EC_Channel, Orders_App: 【販売注文】キャンセル／返品フロー

    %% A1 全キャンセル（出荷前）
    EC_Channel->>Orders_App: 1A. 顧客が注文キャンセル（出荷前）
    Orders_App->>Orders_App: 2A. ステータス更新 = キャンセル
    Orders_App->>Orders_App: 3A. キャンセル日／理由を記録
    note right of Orders_App: 出荷していないので在庫・報酬には影響なし

    %% A2 一部キャンセル（出荷前）
    EC_Channel->>Orders_App: 1B. 顧客が一部キャンセル（100→80）
    Orders_App->>Orders_App: 2B. 出荷予定数を減算（キャンセル数=20）
    Orders_App->>Orders_App: 3B. ステータス更新 = 部分キャンセル
    note right of Orders_App: 在庫確保中、報酬未確定のため影響なし

    %% A3 全キャンセル（出荷後）
    EC_Channel->>Orders_App: 1C. 出荷後に顧客キャンセル
    Op_Staff->>Adjustment_App: 2C. 調整レコード作成（Type=返品, Qty=+100）
    Adjustment_App->>Physical_DB: 3C. 実在庫を加算（返品入庫）
    Adjustment_App->>Stock_App: 4C. 論理在庫を加算（販売取消）
    Orders_App->>Orders_App: 5C. ステータス更新 = 返品完了
    Op_Staff->>Commission_App: 6C. コミッション調整レコード作成（Type=マイナス, 金額=-X, 理由=返品補正）
    note right of Commission_App: 次回支払い期にマイナス補正を反映（手動）

    %% A4 一部返品（出荷後）
    EC_Channel->>Orders_App: 1D. 顧客が一部返品（出荷100→返品10）
    Op_Staff->>Adjustment_App: 2D. 調整レコード作成（Type=返品, Qty=+10）
    Adjustment_App->>Physical_DB: 3D. 実在庫を+10（棚／LOT単位）
    Adjustment_App->>Stock_App: 4D. 論理在庫も+10
    Orders_App->>Orders_App: 5D. 返品数量=10, ステータス=部分返品
    Op_Staff->>Commission_App: 6D. コミッション調整レコード作成（Type=マイナス, 金額=-部分分, 理由=部分返品調整）

    %% A5 サンプル・廃棄調整
    Op_Staff->>Adjustment_App: 6A. 調整レコード（Type=廃棄／サンプル出庫）
    Adjustment_App->>Physical_DB: 7A. 実在庫を減算
    Adjustment_App->>Stock_App: 8A. 論理在庫を減算
    note right of Adjustment_App: サンプル出庫や廃棄分も在庫調整として処理

    %% === 第二部：購買注文（PURCHASE ORDER） ===
    note over PO_App, Mekaポータル: 【購買】キャンセル／返品フロー

    %% B1 全キャンセル（入庫前）
    Op_Staff->>PO_App: 1E. ステータス=キャンセル
    PO_App->>PO_App: 2E. 理由・日付を入力
    note right of PO_App: 入庫していないので在庫・支払には影響なし

    %% B2 一部キャンセル（入庫前）
    Mekaポータル->>PO_App: 1F. 分納確定（100→80納品, 20キャンセル）
    PO_App->>PO_App: 2F. キャンセル数=20, ステータス=部分完了

    %% B3 全返品（入庫後）
    Op_Staff->>Adjustment_App: 1G. 調整レコード（Type=返品, Qty=-100）
    Adjustment_App->>Physical_DB: 2G. 実在庫を減算（返品出庫）
    Adjustment_App->>Stock_App: 3G. 論理在庫を減算（仕入取消）
    PO_App->>PO_App: 4G. ステータス=返品完了

    %% B4 一部返品（入庫後）
    Op_Staff->>Adjustment_App: 1H. 調整レコード（Type=返品, Qty=-10）
    Adjustment_App->>Physical_DB: 2H. 実在庫を-10（棚・LOT単位）
    Adjustment_App->>Stock_App: 3H. 論理在庫を-10
    PO_App->>PO_App: 4H. 返品数量=10, ステータス=部分返品

    %% B5 調整（入庫過多・棚卸差異）
    Op_Staff->>Adjustment_App: 5B. 調整レコード（Type=棚卸差異, Qty=±X）
    Adjustment_App->>Physical_DB: 6B. 実在庫を調整
    Adjustment_App->>Stock_App: 7B. 論理在庫も同期
    note right of Adjustment_App: 棚卸時の差異修正・過剰入庫補正をここで管理

```

---