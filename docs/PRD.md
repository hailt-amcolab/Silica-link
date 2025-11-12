# Silica-link - Product Requirements Document

**Author:** amcolab
**Date:** 2025-11-12
**Version:** 1.0

---

## Executive Summary

Silica-link là nền tảng tích hợp tự động hóa quy trình Đặt hàng – Giao hàng – Nhập hàng – Quản lý hoa hồng/cổ tức cho CREDIE. Hệ thống kết nối các hệ thống rời rạc (Shopify, Ship&Co, WMS, GMO Bank API) thành một nền tảng thống nhất với Kintone làm trung tâm, giảm 90% thao tác thủ công và tăng độ chính xác lên 99.9%.

### What Makes This Special

**Tích hợp toàn diện**: Kết nối tất cả hệ thống (EC, WMS, Ship&Co, GMO Bank, Supplier) thành một nền tảng thống nhất, loại bỏ Excel và manual data entry.

**Tự động hóa 90%**: Từ nhận đơn EC đến phát hành vận đơn, từ cảnh báo tồn thấp đến tạo PO, từ đối soát thanh toán đến chi trả hoa hồng - tất cả đều tự động.

**Realtime & minh bạch**: Dashboard realtime, tracking, audit trail đầy đủ, giúp quản lý ra quyết định nhanh chóng và chính xác.

**Kiến trúc linh hoạt**: Kintone trung tâm + Ruby on Rails + Redis + Sidekiq, dễ scale và mở rộng khi business tăng trưởng.

---

## Project Classification

**Technical Type:** Enterprise Platform Integration
**Domain:** E-commerce Order Management & Supply Chain
**Complexity:** High (multiple system integrations, financial transactions, real-time processing)

**Project Context:**
- **Field Type:** Greenfield (new integration platform)
- **Track:** BMad Method
- **Project Type:** Software Platform
- **Integration Complexity:** High (Yoom, Ship&Co, WMS, GMO Bank API, Supplier Portal/EDI)

---

## Success Criteria

### Primary Success Metrics

1. **Tự động hóa rate**: ≥90% đơn hàng được xử lý tự động (không cần Excel, manual entry)
2. **Processing time**: Giảm 50% thời gian xử lý đơn hàng so với quy trình thủ công hiện tại
3. **Error rate**: <0.1% lỗi nhập liệu (so với ~2-5% hiện tại)
4. **Uptime**: ≥99.5% system availability (24/7 operation)
5. **Inventory accuracy**: 99.9% accuracy giữa Logic Stock và Physical Stock
6. **Payment reconciliation**: 100% đối soát tự động trong 24h

### Business Metrics

- **Order volume**: Xử lý được X đơn/ngày (tăng Y% so với hiện tại)
- **Commission processing**: Xử lý X commission transactions/tháng tự động
- **Cost savings**: Giảm chi phí nhân công 50% (giảm thời gian xử lý)
- **ROI**: Break-even trong 6-12 tháng (tính theo cost savings)

### User Satisfaction Metrics

- **Sales team**: Giảm thời gian nhập đơn từ X → Y phút (50% reduction)
- **Warehouse**: Giảm thời gian xử lý xuất kho từ X → Y phút (50% reduction)
- **Finance**: Giảm thời gian đối soát từ X → Y giờ (80% reduction)
- **Agencies**: Portal response time <2s, 24/7 availability
- **Suppliers**: Portal response time <2s, real-time PO confirmation

---

## Product Scope

### MVP - Minimum Viable Product

**Phase 1: Foundation (4-6 tuần)**

1. **Tự động nhập đơn EC từ Yoom**
   - Webhook endpoint nhận đơn từ Yoom
   - Batch dự phòng (polling) nếu webhook fail
   - Redis + Sidekiq queue system
   - Idempotency handling (channel_order_id + store_id)
   - Race condition handling cho check tồn kho
   - Tự động tạo record trong Kintone Orders App

2. **Tự động phân bổ tồn kho**
   - Check tồn kho realtime (Logic Stock)
   - Không reserve (chỉ check availability)
   - Race condition handling (optimistic locking)
   - Cập nhật trạng thái đơn hàng (allocated/unallocated)

3. **Basic Scan UI cho Warehouse**
   - ReactJS Scan UI (JP language only)
   - Quét mã vạch xuất kho (Shelf, JAN/EAN, LOT)
   - Cập nhật tồn kho Physical Stock
   - Đồng bộ Logic Stock ↔ Physical Stock
   - Today's Pick/Scan/Ship tasks

4. **Tự động phát hành vận đơn Ship&Co**
   - Ship&Co API integration
   - Retry mechanism (3 attempts với exponential backoff)
   - Error handling và logging
   - Lưu label PDF vào S3
   - Gửi thông báo tracking cho khách hàng

5. **Đối soát GMO tự động (AR)**
   - GMO API integration (/transactions endpoint)
   - Virtual Account management
   - Đối soát tự động (matching VA ID, amount, date)
   - Cập nhật PayStatus trong Orders/Invoices App
   - Exception handling (mismatch, duplicate)

### Growth Features (Post-MVP)

**Phase 2: Automation (6-8 tuần)**

6. **Portal đại lý B2B**
   - Kintone-based Portal (JP language, mobile-friendly)
   - Phân quyền (mỗi agency chỉ thấy đơn của mình)
   - Đặt hàng, theo dõi đơn hàng, xem lịch sử
   - Dashboard hiển thị rank, chiết khấu, hoa hồng

7. **Cảnh báo tồn thấp và tạo PO tự động**
   - Reorder point admin setup (per SKU)
   - Daily/after outbound check
   - Tự động tạo PO PDF
   - Email tracking, 2-level approval, reminder
   - Portal nhà cung cấp (Kintone Guest Space)

8. **Quản lý nhập kho phân lô**
   - Pre-payment cho tất cả B2B (GMO API)
   - Nhập kho theo LOT
   - Đồng bộ Physical Stock
   - Cập nhật Logic Stock

9. **Quản lý AR/AP tích hợp**
   - Invoice_App (AR - thu tiền)
   - Payment_Request_App (AP - chi tiền)
   - 2-level approval (Finance → CEO)
   - GMO batch payment (/payments endpoint)
   - Payouts App quản lý chi trả

**Phase 3: Optimization (8-10 tuần)**

10. **Tự động tính rank đại lý**
    - Batch calculation (monthly)
    - Starter/Silver/Gold/Platinum tiers
    - Cập nhật rank & hệ số chiết khấu tự động
    - Dashboard hiển thị tiến độ đạt rank

11. **Tích hợp GoAffPro**
    - Affiliate commission tracking
    - Link generation (links.credie.jp/p/{token})
    - Cookie tracking (partner_id)
    - Monthly aggregation
    - RewardsLedger integration

12. **Quản lý hoa hồng/cổ tức**
    - RewardsLedger App
    - Quarterly calculation (cổ tức)
    - GMO payout tự động
    - PDF generation (báo cáo)
    - Directors dashboard

13. **Dashboard tổng hợp realtime**
    - Tracking đơn hàng, thanh toán, tồn kho
    - Audit trail đầy đủ
    - Báo cáo tổng hợp (sales, inventory, payments)
    - Analytics (trends, forecasts)

### Vision (Future)

**Phase 4: Advanced Features**

- AI/ML: Demand forecasting, predictive analytics, auto-routing
- Advanced Analytics: Real-time insights, trend analysis, forecasting
- Mobile Apps: Warehouse staff app, management dashboard
- Voice Commands: Scan UI với voice input
- Blockchain Integration: Supply chain transparency

**Moonshots (Long-term)**

- Fully Autonomous Warehouse: Robot picking, AI optimization
- Predictive Supply Chain: AI predicts demand, auto-reorder
- Global Multi-warehouse Network: Real-time inventory across locations
- Customer Self-Service Portal: End-to-end customer experience
- Ecosystem Integration: Connect với tất cả partners qua API

---

## Functional Requirements

### FR-1: EC Order Intake Automation

**FR-1.1: Webhook Integration**
- System MUST receive webhook from Yoom when new order created
- Webhook payload MUST include: channel_order_id, store_id, items, customer info, shipping address
- System MUST validate webhook signature (if provided by Yoom)
- System MUST handle webhook failures gracefully (retry, DLQ)

**FR-1.2: Batch Fallback**
- System MUST poll Yoom API periodically (every 15 minutes) to catch missed webhooks
- Batch process MUST check for new orders since last poll
- Batch MUST use same idempotency logic as webhook

**FR-1.3: Idempotency**
- System MUST use idempotency key: `{channel_order_id}_{store_id}`
- System MUST prevent duplicate order creation
- System MUST log duplicate attempts for audit

**FR-1.4: Order Creation in Kintone**
- System MUST create order record in Kintone Orders App
- Order record MUST include: order_id, channel, items, customer, shipping, status
- System MUST link to Product Master for SKU mapping

### FR-2: Inventory Allocation

**FR-2.1: Stock Check**
- System MUST check Logic Stock availability before allocating
- Check MUST be realtime (no caching delay > 5 seconds)
- System MUST handle concurrent requests (race condition)

**FR-2.2: Allocation Logic**
- System MUST NOT reserve stock (only check availability)
- System MUST update order status: allocated/unallocated
- System MUST log allocation attempts for audit

**FR-2.3: Race Condition Handling**
- System MUST use optimistic locking for stock checks
- System MUST handle concurrent allocation requests gracefully
- System MUST prevent overselling

### FR-3: Warehouse Scan UI

**FR-3.1: Barcode Scanning**
- UI MUST support scanning: Shelf code, JAN/EAN, LOT number
- UI MUST validate scanned codes against Product Master
- UI MUST provide visual feedback (success/error)

**FR-3.2: Pick/Scan/Ship Tasks**
- UI MUST display today's tasks from Kintone TaskApp
- UI MUST allow warehouse staff to scan items for picking
- UI MUST update task status in realtime
- UI MUST sync Physical Stock after scan

**FR-3.3: Stock Synchronization**
- System MUST sync Physical Stock → Logic Stock after scan
- Sync MUST be realtime (< 5 seconds delay)
- System MUST handle sync failures (retry, DLQ)

### FR-4: Ship&Co Integration

**FR-4.1: Shipping Label Generation**
- System MUST call Ship&Co API to create shipping label
- System MUST handle API rate limits (queue, retry)
- System MUST save label PDF to S3
- System MUST link PDF to order in Kintone

**FR-4.2: Tracking Notification**
- System MUST send tracking notification to customer (email/SMS via Ship&Co)
- System MUST update order status: shipped
- System MUST log tracking number in Kintone

**FR-4.3: Error Handling**
- System MUST retry failed API calls (3 attempts, exponential backoff)
- System MUST log errors to CloudWatch
- System MUST send alerts for critical failures

### FR-5: GMO Payment Reconciliation (AR)

**FR-5.1: Transaction Fetching**
- System MUST fetch transactions from GMO API (/transactions)
- System MUST handle API authentication (signature, IP whitelist)
- System MUST support both webhook and polling modes

**FR-5.2: Reconciliation Logic**
- System MUST match transactions to Virtual Accounts
- Matching MUST use: VA ID (exact), Amount (exact), Date (within 24h)
- System MUST update PayStatus in Orders/Invoices App

**FR-5.3: Exception Handling**
- System MUST handle mismatches (name, amount, date)
- System MUST create exception record in Kintone Exceptions App
- System MUST alert Finance team for manual review

### FR-6: B2B Agency Portal

**FR-6.1: Authentication & Authorization**
- Portal MUST use Kintone authentication
- Each agency MUST only see their own orders
- Portal MUST support mobile devices (responsive design)

**FR-6.2: Order Management**
- Agencies MUST be able to create orders via Portal
- Agencies MUST be able to view order history
- Agencies MUST be able to track order status (realtime)

**FR-6.3: Dashboard**
- Portal MUST display agency rank (Starter/Silver/Gold/Platinum)
- Portal MUST display discount rate (based on rank)
- Portal MUST display commission/rewards summary

### FR-7: Procurement Automation

**FR-7.1: Low Stock Alert**
- System MUST check stock levels daily (after outbound processing)
- System MUST compare against reorder point (per SKU)
- System MUST trigger PO creation when stock < reorder point

**FR-7.2: PO Generation**
- System MUST generate PO PDF automatically
- PO MUST include: items, quantities, prices, delivery date
- System MUST send PO to supplier via email

**FR-7.3: Supplier Portal**
- Suppliers MUST be able to confirm PO via Portal (Kintone Guest Space)
- Suppliers MUST be able to update delivery date
- Suppliers MUST be able to upload delivery confirmation

**FR-7.4: Approval Workflow**
- PO MUST require 2-level approval (Purchasing → Finance)
- System MUST send reminders if approval pending > 24h
- System MUST track approval history

### FR-8: AR/AP Management

**FR-8.1: Invoice Management (AR)**
- System MUST create Invoice records in Invoice_App
- Invoice MUST link to Order and Virtual Account
- System MUST track payment status (pending/paid/overdue)

**FR-8.2: Payment Request (AP)**
- System MUST create Payment_Request records in Payment_Request_App
- Payment Request MUST require 2-level approval (Finance → CEO)
- System MUST track approval status and history

**FR-8.3: GMO Payment Execution**
- System MUST execute approved payments via GMO API (/payments)
- System MUST handle batch payments (multiple payouts)
- System MUST update Payouts App with results
- System MUST handle payment failures (retry, alert)

### FR-9: Agency Rank Management

**FR-9.1: Rank Calculation**
- System MUST calculate rank monthly (batch process)
- Rank tiers: Starter (< 1M), Silver (1M-5M), Gold (5M-20M), Platinum (> 20M)
- Calculation MUST be based on sales volume (last 12 months)

**FR-9.2: Discount Application**
- System MUST apply discount rate based on rank
- Discount MUST be applied automatically at order creation
- System MUST log discount history for audit

### FR-10: Affiliate Commission (GoAffPro)

**FR-10.1: Link Generation**
- System MUST generate affiliate links: links.credie.jp/p/{token}
- Link MUST include partner_id in cookie
- Link MUST redirect to EC store (Shopify)

**FR-10.2: Commission Tracking**
- System MUST track clicks and orders with partner_id
- System MUST calculate commission based on rules (SKU, channel, tier)
- System MUST record in RewardsLedger App

**FR-10.3: Monthly Aggregation**
- System MUST aggregate commissions monthly
- System MUST generate Payouts records
- System MUST execute payout via GMO API

### FR-11: Rewards & Dividends

**FR-11.1: RewardsLedger Management**
- System MUST record all commission/reward transactions
- Records MUST include: type, amount, period, status
- System MUST support multiple reward types (commission, dividend)

**FR-11.2: Quarterly Dividend Calculation**
- System MUST calculate dividends quarterly
- Calculation MUST use MWATER/SILICA_H2 unit prices
- System MUST apply rate rules (threshold-based)

**FR-11.3: Payout Execution**
- System MUST generate Payouts records for approved rewards
- System MUST execute payout via GMO API
- System MUST generate PDF statements (S3 storage)
- System MUST send statements to recipients (email/portal)

### FR-12: Dashboard & Reporting

**FR-12.1: Realtime Dashboard**
- Dashboard MUST display: orders, inventory, payments (realtime)
- Dashboard MUST support filtering (date range, channel, status)
- Dashboard MUST be accessible 24/7

**FR-12.2: Audit Trail**
- System MUST log all critical operations (order creation, payment, stock update)
- Logs MUST include: user, timestamp, action, result
- Logs MUST be stored in S3 for long-term retention

**FR-12.3: Reporting**
- System MUST generate reports: sales, inventory, payments, commissions
- Reports MUST be exportable (CSV, PDF)
- Reports MUST support scheduled generation (daily, weekly, monthly)

---

## Non-Functional Requirements

### Performance

**NFR-P1: Response Time**
- Webhook processing: < 5 seconds (p95)
- API endpoints: < 2 seconds (p95)
- Portal page load: < 2 seconds (p95)
- Dashboard refresh: < 3 seconds (p95)

**NFR-P2: Throughput**
- Order processing: ≥ 100 orders/minute
- Stock check: ≥ 1000 checks/minute
- Payment reconciliation: ≥ 500 transactions/hour

**NFR-P3: Scalability**
- System MUST handle 10x current volume without architecture changes
- System MUST support horizontal scaling (multiple workers)
- Database (Kintone) MUST handle concurrent reads/writes

### Security

**NFR-S1: Authentication & Authorization**
- All API endpoints MUST require authentication
- Portal access MUST use Kintone authentication
- External API calls MUST use API keys (stored securely)

**NFR-S2: Data Protection**
- Sensitive data (payment info, customer data) MUST be encrypted at rest
- API communications MUST use HTTPS/TLS
- Virtual Account IDs MUST be stored securely

**NFR-S3: API Security**
- GMO API calls MUST use signature authentication
- GMO API calls MUST use IP whitelist
- Webhook endpoints MUST validate signatures (if provided)

**NFR-S4: Audit & Compliance**
- All financial transactions MUST be logged for audit
- System MUST support audit trail queries
- System MUST comply with data retention policies

### Reliability

**NFR-R1: Availability**
- System uptime: ≥ 99.5% (monthly)
- Planned maintenance: < 4 hours/month
- Unplanned downtime: < 2 hours/month

**NFR-R2: Error Handling**
- All external API calls MUST have retry mechanism (3 attempts, exponential backoff)
- Failed operations MUST be logged to DLQ (Dead Letter Queue)
- Critical failures MUST trigger alerts (CloudWatch, email)

**NFR-R3: Data Consistency**
- Stock data MUST be consistent between Logic Stock and Physical Stock
- Order status MUST be consistent across all systems
- Payment status MUST be accurate (no double-counting)

### Integration

**NFR-I1: API Integration**
- System MUST integrate with: Yoom, Ship&Co, GMO Bank API, WMS
- Integration MUST handle API rate limits gracefully
- Integration MUST support both webhook and polling modes

**NFR-I2: Data Synchronization**
- Stock sync (Logic ↔ Physical): < 5 seconds delay
- Order status sync: < 10 seconds delay
- Payment status sync: < 30 seconds delay

**NFR-I3: Idempotency**
- All operations MUST be idempotent (safe to retry)
- Duplicate requests MUST be handled gracefully
- System MUST prevent duplicate order creation

### Usability

**NFR-U1: User Interface**
- Portal MUST be responsive (mobile-friendly)
- Portal MUST support Japanese language
- UI MUST be intuitive (minimal training required)

**NFR-U2: Documentation**
- System MUST have user documentation (portal, scan UI)
- System MUST have technical documentation (API, integration)
- System MUST have operational runbooks

---

## Implementation Planning

### Epic Breakdown Required

Requirements must be decomposed into epics and bite-sized stories (200k context limit).

**Next Step:** Run `workflow create-epics-and-stories` to create the implementation breakdown.

### Technical Architecture

**Next Step:** Run `workflow create-architecture` to design technical architecture.

---

## References

- Product Brief: docs/product-brief-Silica-link-2025-11-12.md
- Brainstorming Session: docs/bmm-brainstorming-session-2025-11-12.md
- Project Overview: project-initial-docs/02-project-overview.md
- Project Scope: project-initial-docs/03-project-scope.md
- Business Flows: project-initial-docs/00-business-flows-overview.md
- System Overview: project-initial-docs/system-overview.md

---

## Next Steps

1. **Epic & Story Breakdown** - Run: `workflow create-epics-and-stories`
2. **Architecture** - Run: `workflow create-architecture`
3. **UX Design** (if UI) - Run: `workflow create-ux-design` (optional)

---

_This PRD captures the essence of Silica-link - a comprehensive integration platform that automates 90% of manual processes and provides realtime transparency across the entire order-to-cash cycle._

_Created through collaborative discovery between amcolab and AI facilitator._

