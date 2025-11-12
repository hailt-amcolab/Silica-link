# System Overview

## Nhóm người dùng mục tiêu
- Nhân viên nội bộ: Kinh doanh, Kế toán, Kho, Sản xuất
- Đối tác kinh doanh: Đại lý (B2B), OEM, FIS (đại lý đặc biệt)
- Đối tác doanh thu: Đại lý giới thiệu/取次店
- Khách hàng B2C (EC)

## Kênh bán hàng & Điểm tiếp xúc
- EC: Shopify (cửa hàng chính), Amazon, Rakuten
- Đối tác: Cửa hàng Shopify của FIS, Cổng thông tin đại lý/OEM
- Tiếp thị: Liên kết đại lý, giới thiệu đối tác (link rút gọn)

## Hệ thống kinh doanh trung tâm
- Quản lý đơn hàng: Hub nhập đơn đa kênh, xử lý trạng thái, tài liệu giao hàng
- Quản lý kho hàng: Tồn kho logic, tồn kho vật lý (Shelf×LOT), đồng bộ kho/Giám sát
- Tài chính & Doanh thu: Invoice/VA đối soát (AR), Payouts (AP), công nợ
- Khách hàng & Đối tác: Tài khoản B2B, chiết khấu/rank đại lý, bảng điều khiển đại lý

## Vận hành & Thực hiện đơn hàng
- Sản xuất: BOM, ghi nhận sản xuất, kiểm soát chất lượng
- Vận hành kho: Kho A (đa sản phẩm), Kho B (chỉ nước), kiểm kê định kỳ
- Thực thi đơn hàng: Pick/Scan/Ship (Ship&Co), thực hiện EC trực tiếp, B2B

## Dịch vụ bên ngoài & Tích hợp
- Thanh toán & Ngân hàng: GMO Aozora (VA / /transactions / /payments)
- Vận chuyển: Ship&Co, Yamato/Sagawa (qua Ship&Co)
- Nền tảng EC: Shopify, Amazon MWS/SP-API, Rakuten
- Chuỗi cung ứng: Cổng NCC/EDI, nhập vật tư, thử nghiệm kiểm tra chất lượng

## Nền tảng kỹ thuật
- Kintone: Database & Workflow trung tâm, Apps: Orders, Stock, Task, PO, Invoice, Payouts, RewardsLedger, Partner/Agency, Rules
- Ruby Server (Middleware): API intake, batch, idempotency, retry/DLQ, điều phối tích hợp
- Scan UI (ReactJS): Barcode (Shelf, JAN/EAN, LOT), Today’s Pick/Scan/Ship, Putaway/X-IN/X-OUT
- AWS: Lambda/Cron, S3 (PDF/CSV/label logs), CloudWatch (metrics/alerts), IAM/Networking

## Các luồng nghiệp vụ chính (liên kết)
- Business Flows: BF-01…BF-09 (EC intake, B2B portal, Pick/Ship, AR/AP, Auto-PO, Rewards/Dividend, Rank, Affiliate, Sample Out)
- System Flows: 01…09 (EC B2C, B2B, GoAffPro, Sản xuất & tồn kho, Procurement, Movement/Return/Adjust, Payment AR/AP, Warehouse Ops, Cancel/Return)

## Bảo mật & Vận hành (guardrails)
- Ký số & whitelist IP với Bank/EC/Webhook; Idempotency key; DLQ + retry
- CloudWatch metrics/alerts; S3 audit log; PDF/CSV trên S3, link vào Kintone
- Đa ngôn ngữ chứng từ theo client_id/ngôn ngữ

## Ngoài phạm vi (MVP)
- Thêm marketplace ngoài Shopify/Amazon/Rakuten; CRM/MA; ERP; BI/data lake; WMS tối ưu nâng cao (wave); app native; FX/đa tiền tệ; EDI tùy NCC riêng

