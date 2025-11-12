# Technical Stack — Tác dụng và cách áp dụng trong dự án

## Shopify
- Tác dụng: Kênh bán B2C chính; nơi phát sinh orders, refunds, fulfillment status.
- Áp dụng:
  - Webhooks `orders/create`, `orders/updated`, `refunds/create`, `fulfillments/update` gửi tới Ruby Server (qua Yoom nếu cần chuẩn hóa).
  - Checkout thu phí ship cho end-user; không backorder.
  - Đồng bộ tracking từ Ship&Co về Shopify sau khi phát hành nhãn.

## Yoom
- Tác dụng: Gateway/Webhook orchestrator; chuẩn hóa payload đa kênh (Shopify, Rakuten, Amazon) trước khi vào hệ thống.
- Áp dụng:
  - Làm điểm tiếp nhận an toàn (retry, signing) → chuyển tiếp đến Ruby Server.
  - Có thể chạy batch lấy orders lịch sử/đồng bộ lại khi webhook mất.

## GMO Bank API
- Tác dụng: Đối soát thu (AR) và chuyển khoản chi (AP).
- Áp dụng:
  - `/transactions`: nạp giao dịch, map theo VA ID → cập nhật `Invoice_App.PayStatus` = PAID.
  - `/payments`: thực hiện batch payouts sau khi CEO phê duyệt trên `Payment_Request_App`; phản ánh phí/kết quả.
  - Bảo mật: chữ ký request, IP whitelist, idempotency key, DLQ + retry.

## Ship&Co
- Tác dụng: Phát hành nhãn vận chuyển, chọn carrier (Yamato/Sagawa), cung cấp tracking.
- Áp dụng:
  - Profiles theo kho: `Warehouse A (Amagasaki)` và (tương lai) `Warehouse B (Gifu)`.
  - Từ `Task_App` (Pick/Scan/Ship) → gọi API tạo label → lưu PDF vào S3 → ghi `tracking_no` vào `Orders_App` → đồng bộ về Shopify.
  - Dùng cho cả B2C (hiện hành) và mở rộng B2B (định hướng số hóa).

## GoAffPro (hoặc cơ chế Affiliate nội bộ)
- Tác dụng: Quản lý affiliate/取次店, link tracking, dashboard.
- Áp dụng:
  - Phát hành link rút gọn; set 1st‑party cookie `partner_id`; webhook orders/refunds.
  - Ghi `RewardsLedger` (pending/approved/paid), quy tắc attribution “last click 30 ngày” (mặc định) + rule tùy biến.
  - Payouts tháng qua GMO `/payments`, xuất PDF, portal partner.

## Kintone
- Tác dụng: Nền tảng nghiệp vụ trung tâm (DB + Workflow).
- Áp dụng (các Apps chính):
  - `Orders_App`, `Stock_App` (論理在庫), `Task_App` (Pick/Putaway/Movement), `PO_App`, `Invoice_App`, `Payment_Request_App`/`Payouts`, `RewardsLedger`, `Rules` (route_rule/affiliate/rank), `Partner/Agency`, `Audit_Log`.
  - Guest Spaces: Portal đại lý/NCC, upload chứng từ (FAX, invoice), xác nhận ngày giao.
  - Webhooks Kintone → Ruby Server: sau phê duyệt/lock, kích hoạt tích hợp ngoài (GMO/Ship&Co/EDI).

## AWS
- Tác dụng: Hạ tầng tích hợp & vận hành.
- Áp dụng:
  - Lambda/Cron: batch tháng/quý (AR aggregation, payouts, rank), nightly reconciliation Logic vs Physical.
  - S3: lưu PDF/CSV/labels/audit logs; presigned URLs nhúng vào Kintone.
  - CloudWatch: metrics/alerts; DLQ (SQS) và retry policy; Parameter Store/Secrets.
  - Networking: IP allowlist tới GMO/Shopify/Ship&Co.

## Ruby on Rails (Ruby Server/Middleware)
- Tác dụng: Orchestrator tích hợp, API backend cho Scan UI/Portal, rule engines.
- Áp dụng:
  - Endpoints tiếp nhận webhooks từ Yoom/Shopify/GoAffPro/GMO; ký số và idempotent.
  - Rule engines: `route_rule` chọn kho A/B; attribution affiliate; rank & discount-rate.
  - Adapter tới Kintone (REST), GMO, Ship&Co, S3; sinh PDF (payout/invoice) nếu cần.
  - AuthN/AuthZ cho portal nhẹ (nếu dùng ngoài Guest).

## Liên kết với các Flow
- BF-01 (EC intake), BF-02 (B2B Portal): Shopify/Yoom → Rails → Kintone.
- BF-03 (Pick/Scan/Ship): Scan UI → Rails → Ship&Co/S3 → Shopify/Kintone.
- BF-04 (AR/AP): Rails ↔ GMO; cập nhật `Invoice_App`/`Payouts`.
- BF-05 (Auto-PO/Portal NCC/EDI): Rails ↔ Kintone ↔ Supplier (REST/EDI).
- BF-06 (Rewards/Dividend): Rails batch + Kintone `RewardsLedger/Rules/Payouts`.
- BF-07 (Rank/Discount): Rails batch tính rank + `Pricing_Rules_App`/Portal API.
- BF-08 (Affiliate): GoAffPro/Link Shortener ↔ Rails ↔ RewardsLedger/Payouts.
- System Flow 8 (Warehouse Ops): TaskApp + Scan UI (React) ↔ Rails ↔ Stock/DB/Ship&Co.

## Bảo mật & vận hành chung
- Idempotency key cho mọi webhook/call, chữ ký HMAC, retry backoff, DLQ.
- Audit đầy đủ (ai/khi nào/cái gì/bao nhiêu/ở đâu): lưu trên S3 + `Audit_Log`.
- Templates đa ngôn ngữ theo `client_id`/locale (invoice, delivery note, payout).

