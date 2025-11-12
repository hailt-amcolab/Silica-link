# Business Flow 8: Quản lý giới thiệu (取次店) kiểu Affiliate

## Tác nhân Kích hoạt

- **Đối tác tạo link**: Tạo link giới thiệu để chia sẻ
- **Khách đặt hàng**: Click link và mua hàng
- **Chốt tháng**: Batch tổng hợp và thanh toán hàng tháng

## Hệ thống Chính

- **Link Shortener Service**: `links.credie.jp` (rút gọn URL)
- **EC Platform**: Shopify (webhook orders.create/refunds.create)
- **Kintone**: RewardsLedger App, Payouts App
- **GMO API**: Chuyển khoản thanh toán
- **Partner Portal**: Dashboard cho đối tác

## Quy trình Chi tiết

### Bước 1: Tạo link rút gọn

- Tạo link dạng `links.credie.jp/p/{token}` cho mỗi đối tác
- Cấu hình domain và TTL (thời gian hiệu lực)
- Redirect 302 khi người dùng click link
- Quản lý token và mapping với partner_id

### Bước 2: Cookie và Tracking

- Lần ghé đầu tiên: đặt **1st-party cookie** chứa `partner_id`
- Xử lý **ITP (Intelligent Tracking Prevention)** của Safari
- Có consent mechanism nếu cần
- Duy trì `partner_id` qua các session

### Bước 3: Webhook xử lý đơn hàng

- Nhận webhook từ EC:
  - `orders.create`: Đơn hàng mới
  - `refunds.create`: Hoàn tiền/trả hàng
- **Khôi phục `partner_id`** từ cookie/session
- **Kiểm chứng** tính hợp lệ của partner_id
- **Ghi log** đầy đủ cho audit

### Bước 4: Ghi nhận hoa hồng

- Tạo record trong **RewardsLedger** với:
  - **Đơn giá/Tỷ lệ** (unit price/rate)
  - **SKU/Kênh** (target SKU/channel)
  - **Tiền tệ** (currency)
  - **Trạng thái**: `pending` → `approved` → `paid`
- Với refund: ghi record **âm** (マイナス行) để trừ hoa hồng

### Bước 5: Máy quy tắc gán công (Attribution)

- **Mặc định**: "Last click 30 ngày"
  - Click cuối cùng trong 30 ngày được tính hoa hồng
- **Hỗ trợ ưu tiên/ghi đè**:
  - Rule có thể override theo partner/SKU
  - Priority management cho các rule
- **Chuẩn bị UI** để thay đổi rule trong tương lai

### Bước 6: Tổng hợp cuối tháng

- **Tổng hợp** theo đối tác và kỳ
- Áp dụng **số tiền tối thiểu thanh toán** (minimum payment amount)
- (Tùy chọn) **Khấu trừ** các khoản đã trả trước
- Tạo record trong **Payouts App**

### Bước 7: Chuyển khoản GMO

- Gọi API `/payments` để chuyển khoản
- **Phản ánh kết quả** vào Payouts App:
  - Trạng thái: `完了` hoặc `失敗`
  - **Phí chuyển khoản** (fee management)
- Cập nhật RewardsLedger status = `paid`

### Bước 8: Sinh phiếu chi PDF

- Tạo **PDF phiếu chi** từ template
- Lưu lên **S3** (có tổ chức theo tháng/năm)
- Gửi email hoặc tạo **link portal** để đối tác tải

### Bước 9: Giám sát gian lận

- **Phát hiện click bất thường**:
  - Pattern bất thường, click farm
- **Tự affiliate** (self-affiliate):
  - Ngăn đối tác tự mua để nhận hoa hồng
- **DLQ (Dead Letter Queue)**:
  - Xử lý lỗi và retry
- **Alert** khi phát hiện gian lận

### Bước 10: Portal đối tác

- **Phát hành link**: Tạo và quản lý link giới thiệu
- **Dashboard**:
  - Doanh thu (sales)
  - Hoa hồng (commission)
  - Lịch sử thanh toán (payment history)
- **CSV export**: Xuất dữ liệu để đối tác phân tích

---

## Đầu ra

- ✅ Bản ghi commission (RewardsLedger)
- ✅ Payout record (Payouts App)
- ✅ Phiếu chi PDF (trên S3)
- ✅ Số liệu portal (dashboard metrics)

---

## Q&A: Affiliate / Partner (取次店)

### Mô hình Hoa hồng

**Q: Xác định mô hình: 1 tầng hay multi-level (đã phát sinh yêu cầu 4階層)?**

**A**: Cần xác nhận với business:
- **Phase 1 (MVP)**: Có thể bắt đầu với **1 tầng** (single-tier)
- **Phase 2**: Mở rộng sang **multi-level** (4階層) nếu cần
- Thiết kế database và rule engine cần **linh hoạt** để hỗ trợ cả hai

**Q: Rule: % cố định? khác nhau theo tầng/SKU?**

**A**: Hệ thống hỗ trợ:
- **% cố định**: Áp dụng cho tất cả SKU
- **% theo SKU**: Mỗi SKU có tỷ lệ riêng
- **% theo tầng**: Nếu multi-level, mỗi tầng có tỷ lệ khác nhau
- **% theo kênh**: Khác nhau giữa EC channels

### Attribution Rule

**Q: Cơ chế attribution: last click 30 ngày hay có tuỳ chỉnh?**

**A**: Hệ thống hỗ trợ:
- **Mặc định**: Last click 30 ngày
- **Tùy chỉnh**: Có thể thay đổi theo:
  - Partner (mỗi đối tác có rule riêng)
  - SKU/Category (một số sản phẩm dùng rule khác)
  - Kênh (EC vs B2B có rule khác)
- **UI quản lý**: Chuẩn bị UI để admin thay đổi rule trong tương lai

---

## Hạng mục Công việc (Estimate)

| Hạng mục | Nội dung | Effort (h) |
| --- | --- | --- |
| **A. 紹介リンク/短縮URL・リダイレクト** | `links.credie.jp/p/{token}` generation・302 redirect・Domain settings・Link validity period | 16 |
| **B. Cookie付与・partner_id維持** | 1st-party Cookie grant (TTL/Consent)・ITP consideration・Cross-domain measures | 16 |
| **C. EC Webhook受口 (注文/返品)** | `orders.create/refunds.create` for `partner_id` restoration・verification・audit log | 24 |
| **D. RewardsLedger 設計・起票** | Order/return accounting・unit price/rate・target SKU/channel・currency・status (pending/approved/paid) | 24 |
| **E. 帰属ルールエンジン** | Default "Last click 30 days"・priority/overwrite・future rule switching UI | 12 |
| **F. 月次集計・締め・Payouts生成** | Target period aggregation・minimum payment amount・source (optional)・Payouts issuance | 16 |
| **G. GMO振込連携 (Payouts→/payments)** | Execute Payouts to `/payments`・reflect results in Payouts/Orders・fee management | 20 |
| **H. 支払明細PDF生成/配布** | PDF template・S3 storage・distribution link (email/portal) | 12 |
| **I. 監視・例外処理** | Fraudulent click detection・self-affiliate suppression・duplication・DLQ/alert | 16 |
| **J. 取次店ポータル (閲覧画面)** | Link issuance・sales/fee dashboard・payment history・CSV export | 24 |
| **K. テスト・UAT・運用手順** | E2E (Click→Order→Accounting→Monthly→Transfer→PDF)・UAT support・procedure document | 16 |
| **Tổng cộng** | | **196** |

---

## Luồng Dữ liệu

### Link Creation & Click Flow
```
Partner tạo link 
  → links.credie.jp/p/{token}
  → Customer click
  → Set cookie (partner_id)
  → Redirect to EC
```

### Order & Commission Flow
```
Customer mua hàng
  → EC webhook (orders.create)
  → Restore partner_id
  → RewardsLedger (ghi hoa hồng)
  → Attribution rule engine
```

### Monthly Payout Flow
```
Cuối tháng
  → Tổng hợp commission
  → Tạo Payouts
  → GMO /payments
  → PDF statement
  → Portal update
```

---

## Liên kết Flow Khác

- **Flow 3**: GoAffPro連携 (Có thể tích hợp với GoAffPro nếu cần)
- **Flow 4**: GMO振込連携 (Sử dụng cùng API /payments)
- **Flow 6**: 配当・報酬管理 (Cùng RewardsLedger và Payouts)
- **Flow 7**: 代理店ランク管理 (Có thể kết hợp với rank management)

