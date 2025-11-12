# Tài liệu Dự án Silica-link

Thư mục này chứa các tài liệu quan trọng của dự án Silica-link.

## Cấu trúc Tài liệu

### Tài liệu Tổng quan
- [00-business-flows-overview.md](./00-business-flows-overview.md) - Tổng quan luồng nghiệp vụ & phạm vi
- [system-overview.md](./system-overview.md) - System Overview (kiến trúc, tích hợp, guardrails)
- [technical-stack.md](./technical-stack.md) - Technical Stack: vai trò & cách áp dụng công nghệ
- [kintone-apps.md](./kintone-apps.md) - Kintone Apps: danh mục và vai trò
- [estimate-v1.md](./estimate-v1.md) - Ước tính giờ công (Junior Ruby Dev)
- [01-project-why.md](./01-project-why.md) - Lý do và mục tiêu dự án
- [02-project-overview.md](./02-project-overview.md) - Tổng quan về kiến trúc và tích hợp hệ thống
- [03-project-scope.md](./03-project-scope.md) - Phạm vi và các chức năng chính của dự án
- [user-hearing-credie-sales.md](./user-hearing-credie-sales.md) - User Hearing: Hiện trạng kinh doanh & đối tác CREDIE (Sales)
- [99-operations-security-and-out-of-scope.md](./99-operations-security-and-out-of-scope.md) - Vận hành & bảo mật; Ngoài phạm vi

### Business Flows
- [BF-01-yoom-to-kintone-order-allocation.md](./BF-01-yoom-to-kintone-order-allocation.md) - Yoom → Kintone: Nhận đơn + phân bổ tồn kho
- [BF-02-b2b-agent-portal-order-approval.md](./BF-02-b2b-agent-portal-order-approval.md) - Cổng đại lý B2B: nhập đơn + phê duyệt
- [BF-03-kintone-to-shipco-pick-scan-ship.md](./BF-03-kintone-to-shipco-pick-scan-ship.md) - Kintone → Ship&Co: Tự động giao hàng + quét mã
- [BF-04-gmo-payment-reconciliation.md](./BF-04-gmo-payment-reconciliation.md) - Đối soát thu (GMO) + Thực hiện chi
- [BF-05-supply-reorder-auto-po-supplier-portal-edi.md](./BF-05-supply-reorder-auto-po-supplier-portal-edi.md) - Giám sát tồn → Tự động tạo PO + cổng NCC/EDI
- [BF-06-quarterly-rewards-and-dividends.md](./BF-06-quarterly-rewards-and-dividends.md) - Hoa hồng & cổ tức theo quý
- [BF-07-agency-rank-and-discount-management.md](./BF-07-agency-rank-and-discount-management.md) - Xếp hạng đại lý & quản lý chiết khấu
- [BF-08-affiliate-referral-management.md](./BF-08-affiliate-referral-management.md) - Quản lý giới thiệu (取次店) kiểu Affiliate
- [BF-09-sample-outflow.md](./BF-09-sample-outflow.md) - Xuất mẫu dùng thử (Sales → Kho)

### System Flows
- [04-system-flow-01-ec-order-processing.md](./04-system-flow-01-ec-order-processing.md) - Flow 1: EC注文の自動取込・在庫引当と出荷処理 (B2C)
- [05-system-flow-02-b2b-order-processing.md](./05-system-flow-02-b2b-order-processing.md) - Flow 2: B2B 全体プロセス: 代理店/注文 出荷/配送
- [06-system-flow-03-affiliate-commission-processing.md](./06-system-flow-03-affiliate-commission-processing.md) - Flow 3: GoAffPro連携によるアフィリエイト報酬処理
- [07-system-flow-04-product-material-production-stock.md](./07-system-flow-04-product-material-production-stock.md) - Flow 4: 製品・資材・製造・在庫管理フロー
- [08-system-flow-05-procurement-cycle.md](./08-system-flow-05-procurement-cycle.md) - Flow 5: 資材購買サイクル
- [09-system-flow-06-sample-return-move-adjustment.md](./09-system-flow-06-sample-return-move-adjustment.md) - Flow 6: サンプル出荷・返品・棚移動・在庫調整
- [10-system-flow-07-payment-ar-ap.md](./10-system-flow-07-payment-ar-ap.md) - Flow 7: 支払いフロー（AR/AP統合）
- [11-system-flow-08-warehouse-operation.md](./11-system-flow-08-warehouse-operation.md) - Flow 8: 倉庫作業のフロー（出庫・入庫フロー）
- [12-system-flow-09-cancel-return.md](./12-system-flow-09-cancel-return.md) - Flow 9: 全体／一部キャンセル・返品

## Mục đích Dự án

Dự án Silica-link nhằm tối ưu hóa quy trình Đặt hàng – Giao hàng – Nhập hàng – Quản lý hoa hồng/cổ tức, tích hợp các hệ thống rời rạc thành một nền tảng thống nhất.

## Nền tảng Trung tâm

- **Kintone**: Database & Workflow trung tâm
- **AWS Lambda**: Batch xử lý, giám sát và logging

## Hệ thống Tích hợp

- **Yoom**: Nhập đơn từ Shopify, Rakuten, Amazon
- **Ship&Co**: Phát hành vận đơn, tracking
- **WMS** (ロジザードZERO / WMS Lite): Quản lý kho
- **GMO Aozora Bank API**: Đối soát và chuyển khoản
- **Supplier Portal / EDI**: Kết nối nhà cung cấp
- **GoAffPro**: Quản lý affiliate và reward
- **Meka Portal**: Portal nhà cung cấp, xác nhận PO/Invoice/分納
- **RewardsLedger**: Quản lý hoa hồng/đại lý

