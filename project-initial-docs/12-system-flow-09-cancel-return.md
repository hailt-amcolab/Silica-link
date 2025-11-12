# System Flow 9: 全体／一部キャンセル・返品

## Mục tiêu

Mô tả quy trình xử lý các tình huống hủy, trả hàng (toàn phần / một phần) cho cả đơn bán (Order) và đơn mua (PO), bao gồm:

- Quản lý tồn kho logic & vật lý thông qua Adjustment_App
- Cập nhật trạng thái đơn hàng / đơn mua
- Điều chỉnh hoa hồng (commission) thủ công khi đã phát sinh chi trả ở kỳ trước
- Hợp nhất toàn bộ xử lý hoàn – hủy – trả hàng trong một quy trình duy nhất, dễ vận hành và kiểm soát

Mục tiêu là đảm bảo dữ liệu tồn kho, đơn hàng và hoa hồng luôn khớp nhau mà không cần automation Ruby ở giai đoạn MVP.

## Thành phần hệ thống liên quan

| Thành phần | Vai trò |
| --- | --- |
| Orders_App (受注アプリ) | Quản lý thông tin đơn bán, trạng thái, số lượng, lý do hủy/trả |
| PO_App (購買アプリ) | Quản lý đơn mua hàng, số lượng nhập và tình trạng hủy/trả |
| Adjustment_App (調整アプリ) | Ghi nhận các nghiệp vụ điều chỉnh kho (返品・廃棄・サンプル・棚卸差異) |
| Stock_App (在庫アプリ) | Theo dõi tồn kho logic, cập nhật khi có điều chỉnh |
| Physical_DB (実在庫DB) | Quản lý tồn kho vật lý chi tiết theo 棚 (kệ) và LOT番号 |
| Commission_App (コミッションアプリ) | Quản lý và điều chỉnh hoa hồng thủ công sau khi hoàn/trả |
| Op_Staff (運用担当者) | Nhân viên vận hành thực hiện điều chỉnh bằng tay trong các app |
| EC_Channel / Mekaポータル | Nơi khách hàng / nhà cung cấp thực hiện mua, hủy hoặc trả hàng |

## Quy trình tổng thể

### ① Đơn bán (Sales Order)

| Trường hợp | Hành động | Ảnh hưởng | Ghi chú |
| --- | --- | --- | --- |
| Hủy toàn phần – Chưa xuất kho | Cập nhật `Status = キャンセル` trong Orders_App | Không ảnh hưởng kho | Lưu ngày & lý do |
| Hủy một phần – Chưa xuất kho | Cập nhật `キャンセル数量`, `Status = 部分キャンセル` | Không ảnh hưởng kho | Giữ nguyên phần còn lại |
| Hủy toàn phần – Đã xuất kho | Adjustment_App `Type=返品`, `Qty=+Số lượng` | Cộng lại tồn kho logic + vật lý | `Reason = 出荷後キャンセル` |
| Hủy một phần – Đã xuất kho | Adjustment_App `Type=返品`, `Qty=+phần hủy` | Cộng lại tồn tương ứng | `Reason = 部分返品` |
| Xuất mẫu / Hủy hàng / Sử dụng nội bộ | Adjustment_App `Type=廃棄／サンプル出庫` | Giảm tồn kho logic + vật lý | Không liên quan commission |

#### Điều chỉnh hoa hồng (Commission)

Nếu đơn hàng đã được tính hoặc trả commission, khi phát sinh hoàn/trả:

- Tạo bản ghi điều chỉnh thủ công trong `Commission_App`:
  - `Type = 調整(マイナス)`, `金額 = -Hoa hồng đã trả`, `Reason = キャンセル補正／返品補正`, `次回期適用 = true`
- Nếu có phát sinh cộng thêm (đổi hàng/mua bổ sung): `Type = 調整(プラス)`, `金額 = +Phần bổ sung`, `Reason = 交換対応／追加入庫`
- Các record điều chỉnh phản ánh ở kỳ chi trả kế tiếp.

---

### ② Đơn mua (Purchase Order)

| Trường hợp | Hành động | Ảnh hưởng | Ghi chú |
| --- | --- | --- | --- |
| Hủy toàn phần – Chưa nhập kho | Cập nhật `Status = キャンセル` | Không ảnh hưởng kho | Lưu ngày & lý do |
| Hủy một phần – Chưa nhập kho | Cập nhật `入庫予定数` hoặc `キャンセル数量` | Không ảnh hưởng kho | Giữ nguyên phần còn lại |
| Trả toàn phần – Sau khi nhập kho | Adjustment_App `Type=返品`, `Qty=-Số lượng` | Giảm tồn kho logic + vật lý | `Reason = 不良返品` |
| Trả một phần – Sau khi nhập kho | Adjustment_App `Type=返品`, `Qty=-phần trả` | Giảm tương ứng | `Reason = 一部返品` |
| Sai lệch tồn kho / Kiểm kê | Adjustment_App `Type=棚卸差異`, `Qty=±X` | Điều chỉnh tồn kho vật lý & logic | `Reason = 棚卸修正` |

## Sequence Diagram

```mermaid
sequenceDiagram
    title 🧾 Flow: 注文・購買におけるキャンセル／返品 + コミッション調整（全体・一部）MVP版

    participant EC_Channel as 🛒 ECチャネル / Mekaポータル
    participant Orders_App as 📄 受注アプリ（販売）
    participant PO_App as 📝 購買アプリ（仕入）
    participant Adjustment_App as ⚖️ 調整アプリ（入出庫修正）
    participant Stock_App as 📈 在庫アプリ（論理）
    participant Physical_DB as 🏭 実在庫DB（棚・LOT単位）
    participant Commission_App as 💰 コミッションアプリ
    participant Op_Staff as 🧑‍💻 運用担当者

    %% 第一部：販売注文
    note over EC_Channel, Orders_App: 【販売注文】キャンセル／返品フロー

    %% A1 全キャンセル（出荷前）
    EC_Channel->>Orders_App: 1A. 顧客が注文キャンセル（出荷前）
    Orders_App->>Orders_App: 2A. ステータス = キャンセル
    Orders_App->>Orders_App: 3A. キャンセル日／理由を記録

    %% A2 一部キャンセル（出荷前）
    EC_Channel->>Orders_App: 1B. 一部キャンセル（100→80）
    Orders_App->>Orders_App: 2B. 出荷予定数を減算（キャンセル数=20）
    Orders_App->>Orders_App: 3B. ステータス = 部分キャンセル

    %% A3 全キャンセル（出荷後）
    EC_Channel->>Orders_App: 1C. 出荷後キャンセル
    Op_Staff->>Adjustment_App: 2C. Type=返品, Qty=+100
    Adjustment_App->>Physical_DB: 3C. 実在庫 +100（返品入庫）
    Adjustment_App->>Stock_App: 4C. 論理在庫 +100
    Orders_App->>Orders_App: 5C. ステータス = 返品完了
    Op_Staff->>Commission_App: 6C. 調整(マイナス), 理由=返品補正

    %% A4 一部返品（出荷後）
    EC_Channel->>Orders_App: 1D. 一部返品（10）
    Op_Staff->>Adjustment_App: 2D. Type=返品, Qty=+10
    Adjustment_App->>Physical_DB: 3D. 実在庫 +10
    Adjustment_App->>Stock_App: 4D. 論理在庫 +10
    Orders_App->>Orders_App: 5D. 部分返品 = 10
    Op_Staff->>Commission_App: 6D. 調整(マイナス), 理由=部分返品調整

    %% A5 サンプル・廃棄
    Op_Staff->>Adjustment_App: 6A. Type=廃棄／サンプル出庫
    Adjustment_App->>Physical_DB: 7A. 実在庫を減算
    Adjustment_App->>Stock_App: 8A. 論理在庫を減算

    %% 第二部：購買注文
    note over PO_App, EC_Channel: 【購買】キャンセル／返品フロー

    %% B1 全キャンセル（入庫前）
    Op_Staff->>PO_App: 1E. ステータス = キャンセル（入庫前）
    PO_App->>PO_App: 2E. 理由・日付を記録

    %% B2 一部キャンセル（入庫前）
    EC_Channel->>PO_App: 1F. 分納確定（100→80, 20キャンセル）
    PO_App->>PO_App: 2F. キャンセル数=20, ステータス=部分完了

    %% B3 全返品（入庫後）
    Op_Staff->>Adjustment_App: 1G. Type=返品, Qty=-100
    Adjustment_App->>Physical_DB: 2G. 実在庫 -100（返品出庫）
    Adjustment_App->>Stock_App: 3G. 論理在庫 -100
    PO_App->>PO_App: 4G. ステータス = 返品完了

    %% B4 一部返品（入庫後）
    Op_Staff->>Adjustment_App: 1H. Type=返品, Qty=-10
    Adjustment_App->>Physical_DB: 2H. 実在庫 -10
    Adjustment_App->>Stock_App: 3H. 論理在庫 -10
    PO_App->>PO_App: 4H. 返品数量 = 10, ステータス = 部分返品

    %% B5 棚卸差異
    Op_Staff->>Adjustment_App: 5B. Type=棚卸差異, Qty=±X
    Adjustment_App->>Physical_DB: 6B. 実在庫 調整
    Adjustment_App->>Stock_App: 7B. 論理在庫 同期
```

## Kết quả mong đợi

- Một quy trình thống nhất cho hủy/hoàn/điều chỉnh, dễ vận hành
- Tồn kho logic và vật lý luôn khớp thông qua Adjustment_App
- Commission được điều chỉnh minh bạch ở kỳ kế tiếp khi có hoàn/trả
- Giảm phụ thuộc automation, phù hợp giai đoạn MVP

