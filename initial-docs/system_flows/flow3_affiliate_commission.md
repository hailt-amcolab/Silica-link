# **Flow3: GoAffPro連携によるアフィリエイト報酬処理**

```
sequenceDiagram
    title Flow Affiliate: GoAffPro報酬管理 × Rank同期 × Kintone集計 × 承認 × GMO振込

    participant Sales_Staff as 🧑‍💼 営業担当(Kintone)
    participant Partner as 🙋 アフィリエイト(Partner)
    participant Partner_App as 🗂️ Partner Master (Kintone)
    participant Rule_App as 📏 Commission Rule (Rank別率/Kintone)
    participant GoAffPro as 🔗 GoAffPro (Affiliate管理)
    participant EC_Channel as 🛒 Shopify
    participant Yoom as ⚙️ Yoom(Webhook)
    participant Orders_App as 📄 受注アプリ(Kintone)
    participant Ruby_Server as 🖥️ Ruby Server
    participant Commission_App as 💰 Commission集計App(Kintone)
    participant Payments_App as 💸 支払管理アプリ(Kintone)
    participant CEO as 👑 CEO(最終承認)
    participant GMO_API as 🏦 GMO銀行API

    %% === 0. パートナー登録 & リンク発行 ===
    Sales_Staff->>Partner_App: 1. パートナー登録(口座, Rank初期値, 手数料率)
    Partner_App->>GoAffPro: 2. Partner同期(PartnerID, Email, Rank)
    GoAffPro->>Partner: 3. アフィリエイトURL発行 (アカウント／商品／リスト単位)

    %% === 1. 注文発生 & partner_id連携 ===
    Partner->>Customer: 4. リンク共有
    Customer->>EC_Channel: 5. 購入完了(GoAffPro付与 partner_id)
    EC_Channel->>Yoom: 6. 新規注文Webhook
    Yoom->>Ruby_Server: 7. 注文Payload(partner_id含む)
    Ruby_Server->>Orders_App: 8. 受注レコード作成(partner_id保存)

    %% === 2. GoAffPro 報酬登録 & 返品対応 ===
    EC_Channel->>GoAffPro: 9. [Webhook] 注文連携
    GoAffPro->>GoAffPro: 10. Rewards計上(成果登録)

    par キャンセル/返品
        EC_Channel->>GoAffPro: 11a. Refund/Cancel webhook
        GoAffPro->>GoAffPro: 12a. Rewards自動減額
        Op_Staff->>Orders_App: 13a. [手動] ステータス更新(Cancel/Return)
        note right of Orders_App: Kintone側は手動で管理（同期不要）
    end

    %% === 3. Rank同期処理 (月次実行) ===
    rect rgb(240,250,255)
    loop 毎月初 (Rank同期サイクル)
        Partner_App->>GoAffPro: 14. Rank情報送信 (最新Rankを表示に反映)
        GoAffPro-->>Partner_App: 15. 実績データ返却 (売上/Rewards/活動率)
        Ruby_Server->>Rule_App: 16. 評価ロジック実行（閾値比較）
        Ruby_Server->>Partner_App: 17. Rank更新（昇格/維持/降格）
        note right of Partner_App: Rankが更新されると次月の手数料率にも反映
    end
    end

    %% === 4. 月次報酬集計 ===
    rect rgb(250,250,230)
    loop 月末
        Ruby_Server->>GoAffPro: 18. 月次Rewards取得(Partner, Rank, 金額)
        GoAffPro-->>Ruby_Server: 19. Rewards明細返却
        Ruby_Server->>Rule_App: 20. Rank別Rate取得
        Ruby_Server->>Commission_App: 21. 集計レコード起票(Partner×期間×報酬額)
    end
    end

    %% === 5. 承認 & 支払依頼作成 ===
    Sales_Staff->>Commission_App: 22. 社内承認(確認済)
    Commission_App->>Payments_App: 23. 支払依頼レコード作成(支払期限=翌月末)

    %% === 6. CEO承認 & GMO振込 ===
    Payments_App->>CEO: 24. 最終承認依頼
    CEO->>Payments_App: 25. 承認(Go)
    Payments_App->>Ruby_Server: 26. 振込指示生成
    Ruby_Server->>Partner_App: 27. 口座情報取得
    Ruby_Server->>GMO_API: 28. /payments 実行
    GMO_API-->>Ruby_Server: 29. 結果返却(成功/失敗)
    Ruby_Server->>Payments_App: 30. 支払ステータス更新
    Ruby_Server->>Commission_App: 31. 状態更新(支払済)
    Ruby_Server->>GoAffPro: 32. Payout結果を同期
```

### 🎯 Mục tiêu

Mục tiêu của flow này là quản lý toàn bộ chu trình **Affiliate Commission** thông qua việc tích hợp giữa:

- **GoAffPro** (quản lý rewards thực tế & hiển thị dashboard cho affiliate),
- **Kintone** (quản lý Partner, Rank, Rule, Commission và Payment),
- **Ruby Server** (middleware thực hiện logic xử lý batch),
- **GMO API** (chuyển khoản ngân hàng).

Hệ thống phải đảm bảo:

1. Rewards trên GoAffPro luôn phản ánh đúng Rank hiện tại của affiliate.
2. Commission và Payment được tổng hợp, phê duyệt, và chuyển khoản minh bạch hàng tháng.
3. Trạng thái thanh toán được đồng bộ ngược lại giữa GoAffPro và Kintone.
4. Rank được đánh giá lại tự động mỗi tháng dựa trên doanh số thực tế.

---

### 🧩 Thành phần hệ thống

| Thành phần | Mô tả |
| --- | --- |
| **GoAffPro** | Hệ thống quản lý affiliate, reward và rank thực tế. |
| **Partner_App (Kintone)** | Quản lý thông tin affiliate, tài khoản ngân hàng, Rank hiện tại. |
| **Rule_App (Kintone)** | Lưu bảng quy tắc hoa hồng theo từng Rank (Rank-based Commission Rule). |
| **Orders_App (Kintone)** | Ghi nhận đơn hàng và trạng thái thanh toán, hủy hoặc trả hàng. |
| **Commission_App (Kintone)** | Tổng hợp commission cuối kỳ, phê duyệt nội bộ. |
| **Payments_App (Kintone)** | Quản lý thông tin chuyển khoản (Payment Request / Payment Status). |
| **Ruby_Server** | Middleware thực hiện logic batch, gọi API GoAffPro & GMO Bank. |
| **GMO_API** | API chuyển khoản hàng loạt cho các affiliate. |
| **CEO (承認者)** | Phê duyệt cuối cùng trước khi thực hiện chuyển khoản. |

---

### ⚙️ Quy trình chi tiết

### 1️⃣ Đăng ký và phát hành URL Affiliate

1. Nhân viên sales đăng ký affiliate trong `Partner_App`, bao gồm:
    - Partner ID, Email, Rank khởi tạo (ví dụ: Bronze),
    - Tài khoản ngân hàng
2. `Ruby_Server` đồng bộ dữ liệu sang **GoAffPro** qua API.
3. GoAffPro tự động phát hành **Affiliate URL**:
    - URL theo tài khoản (Affiliate Account),
    - URL cho từng sản phẩm (Product Link),
    - URL cho danh sách sản phẩm (Collection Link).
4. Affiliate (Partner) chia sẻ URL để giới thiệu người mua hàng.

---

### 2️⃣ Đơn hàng và ghi nhận Reward

1. Khi người dùng click vào affiliate URL và mua hàng trên Shopify,
GoAffPro gắn `partner_id` vào đơn hàng.
2. `Yoom` nhận Webhook → gửi sang `Ruby_Server` → tạo record trong `Orders_App`.
3. GoAffPro tự động ghi nhận **Reward (Commission)** tương ứng cho affiliate.
4. Thông tin đơn hàng, số tiền, Partner ID được lưu trên cả hai hệ thống.

---

### 3️⃣ Xử lý Cancel / Return

| Hệ thống | Hành động |
| --- | --- |
| **GoAffPro** | Shopify gửi Webhook về GoAffPro khi có refund hoặc return. GoAffPro tự động trừ reward tương ứng. |
| **Kintone** | Nhân viên vận hành cập nhật **Orders_App.status** sang `キャンセル` hoặc `返品` (thủ công). |
| **Ruby_Server** | Khi tổng hợp tháng, Ruby sẽ đối chiếu reward thực tế đã giảm trên GoAffPro để tránh double-count. |

→ Việc hoàn tiền chỉ ảnh hưởng đến reward thực tế trên GoAffPro,

còn trên Kintone chỉ lưu record trạng thái phục vụ cho audit nội bộ.

---

### 4️⃣ Đồng bộ Rank hàng tháng (毎月初)

Mục tiêu: đảm bảo GoAffPro hiển thị đúng Rank hiện tại và Kintone cập nhật Rank mới theo kết quả kỳ trước.

1. `Partner_App` gửi Rank hiện tại của từng Partner lên GoAffPro để hiển thị.
2. GoAffPro trả về dữ liệu hoạt động tháng trước:
    - Tổng doanh số,
    - Tổng Reward,
    - Số lượng đơn hàng,
    - Tỉ lệ hoàn trả / hiệu suất.
3. `Ruby_Server` thực hiện **logic đánh giá Rank**:
    - So sánh với bảng quy tắc trong `Rule_App` (ví dụ: Silver ≥ ¥500,000).
    - Nếu đạt ngưỡng → tăng Rank.
    - Nếu thấp hơn → hạ hoặc giữ nguyên Rank.
4. Rank mới được cập nhật vào `Partner_App` → đồng bộ lại sang GoAffPro.

Ví dụ:

> Partner A đạt ¥2,300,000 → Rank tháng sau tự động lên Gold,
> 
> 
> và tỉ lệ hoa hồng trên GoAffPro hiển thị tăng từ 7% → 10%.
> 

---

### 5️⃣ Chốt sổ và tổng hợp commission (毎月末)

1. Vào cuối tháng, `Ruby_Server` gọi API GoAffPro để lấy dữ liệu **Rewards tổng** theo Partner.
2. Đối chiếu với Rank trong `Rule_App` để xác định tỷ lệ chính xác.
3. Ghi record vào `Commission_App` gồm:
    - Partner ID,
    - Kỳ tháng,
    - Rank hiện tại,
    - Tổng Reward (¥),
    - Trạng thái: `下書き`.
4. Nhân viên kế toán hoặc sales kiểm tra, xác nhận và chuyển sang `承認済み`.
5. Sau khi approve, hệ thống tự động tạo record tương ứng trong `Payments_App`
với **hạn thanh toán = cuối tháng kế tiếp**.

---

### 6️⃣ Phê duyệt CEO và chuyển khoản GMO

1. `Payments_App` gửi yêu cầu phê duyệt tới CEO.
2. Khi CEO approve (`Go`), `Ruby_Server` gọi **GMO API /payments** để thực hiện chuyển khoản:
    - Sử dụng thông tin tài khoản từ `Partner_App`.
    - Có thể batch nhiều Partner cùng lúc.
3. Nhận kết quả từ GMO (Success / Failed / Fee).
4. Cập nhật:
    - `Payments_App.status` = 支払済み hoặc 失敗,
    - `Commission_App.status` = 支払済み,
    - Đồng bộ ngược kết quả payout sang GoAffPro để hiển thị cho affiliate.

---

### 🧮 Logic tính hoa hồng

| Rank | Doanh số trong kỳ | Tỷ lệ hoa hồng | Ghi chú |
| --- | --- | --- | --- |
| **Bronze** | Dưới ¥500,000 | 5% | Rank khởi tạo |
| **Silver** | ¥500,000–¥2,000,000 | 7% | Tự động tăng khi đạt ngưỡng |
| **Gold** | Trên ¥2,000,000 | 10% | Hạ nếu 2 kỳ liên tiếp không đạt |
- Commission = Sales × Rate (lấy theo Rank tại thời điểm tính).
- Nếu Reward từ GoAffPro lệch với Rate trong Rule_App, ghi chú cảnh báo trong Commission_App.

---

### ⚠️ Ngoại lệ

| Tình huống | Hành động |
| --- | --- |
| **Cancel / Return** | GoAffPro tự động giảm reward; Kintone cập nhật thủ công trạng thái. |
| **Sai lệch Rank / Reward** | Ruby đánh dấu flag “差異あり” trong Commission_App để kế toán kiểm tra. |
| **Lỗi chuyển khoản** | Payments_App = 支払失敗; không cập nhật Commission sang 支払済. |
| **Thay đổi Rank đột xuất** | Có thể update thủ công trong Partner_App, GoAffPro sẽ sync lại trong batch kế tiếp. |

---

### ✅ Kết quả mong đợi

- Affiliate có thể xem **Rank và Reward thực tế** trực tiếp trên GoAffPro Dashboard.
- Kintone quản lý toàn bộ luồng **từ partner → commission → payment**.
- Rank được cập nhật tự động hàng tháng, phản ánh đúng hiệu suất bán hàng.
- CEO có thể duyệt & chuyển khoản qua GMO chỉ trong 1 thao tác.
- Toàn bộ luồng được tự động ghi log, có thể audit chi tiết theo Partner, Rank và kỳ.

---

### 📅 Tần suất xử lý batch

| Loại batch | Mô tả | Lịch chạy |
| --- | --- | --- |
| Rank Sync (GoAffPro ↔ Kintone) | Gửi rank hiện tại & nhận kết quả hiệu suất để đánh giá rank mới | Ngày 1 hàng tháng |
| Rewards Import | Lấy dữ liệu rewards theo kỳ từ GoAffPro | Ngày 28–31 hàng tháng |
| GMO Payment | Thực hiện chuyển khoản sau khi CEO duyệt | Ngày cuối  tháng kế tiếp |

---

### 🔄 Sơ đồ tổng quan luồng

```
GoAffPro ⇄ Ruby_Server ⇄ Kintone (Partner / Rule / Commission / Payments) ⇄ GMO API

```

---