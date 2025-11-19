# Technical Stack — Tác dụng và cách áp dụng trong dự án

## Shopify
- Tác dụng: Kênh bán B2C chính; nơi phát sinh orders, refunds, fulfillment status.
- Shopify có cơ chế **Webhooks native**: khi có đơn hàng mới (event orders/create), Shopify sẽ **push** ngay JSON sang URL bạn config.
- Vì vậy Yoom chỉ cần tạo **Webhook trigger** rồi nhận trực tiếp → realtime, không cần polling.

## Yoom
- Tác dụng: Gateway/Webhook orchestrator; chuẩn hóa payload đa kênh (Shopify, Rakuten, Amazon) trước khi vào hệ thống.

## GMO Bank API
- Tác dụng: Đối soát thu (AR) và chuyển khoản chi (AP).

## Ship&Co
- Tác dụng: Phát hành nhãn vận chuyển, chọn carrier (Yamato/Sagawa), cung cấp tracking.

## GoAffPro (hoặc cơ chế Affiliate nội bộ)
- Tác dụng: Quản lý affiliate/取次店, link tracking, dashboard.

## Kintone
- Tác dụng: Nền tảng nghiệp vụ trung tâm (apps).

## AWS EC2 + S3
- Tác dụng: Hạ tầng tích hợp & vận hành.

## Ruby on Rails (Ruby Server/Middleware)
- Tác dụng: Orchestrator tích hợp, API backend cho Scan UI/Portal, rule engines.
- MySQL database
- Redis

## React js
- Tác dụng: UI cho hệ thống quản lý kho vât lý

## Bảo mật & vận hành chung
- Idempotency key cho mọi webhook/call, chữ ký HMAC, retry backoff, DLQ.
- Audit đầy đủ (ai/khi nào/cái gì/bao nhiêu/ở đâu): lưu trên S3 + `Audit_Log`.
- Templates đa ngôn ngữ theo `client_id`/locale (invoice, delivery note, payout).
