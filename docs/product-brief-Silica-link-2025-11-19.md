# Product Brief: Silica-link

**Date:** 2025-11-19
**Author:** Potato
**Context:** Enterprise Brownfield Project

---

## Executive Summary

Silica-link là hệ thống quản lý tích hợp cho CREDIE, tự động hóa toàn bộ chuỗi giá trị từ **Đặt hàng → Giao hàng → Nhập hàng → Quản lý hoa hồng/cổ tức**. Hệ thống thay thế các quy trình thủ công rời rạc bằng nền tảng thống nhất, tích hợp Shopify, Kintone, Ship&Co, GMO Bank API và Yoom để đảm bảo minh bạch realtime, giảm lỗi và tăng năng suất.

**Core Value Proposition:** Tự động hóa 100% quy trình order-to-cash với realtime visibility, đảm bảo zero duplicate orders, và hỗ trợ đa kênh (BtoC, BtoB, FIS fulfillment, Affiliate tracking).

---

## Core Vision

### Problem Statement

CREDIE đang vận hành quy trình **Đặt hàng – Giao hàng – Nhập hàng – Quản lý hoa hồng/cổ tức** trên nhiều hệ thống rời rạc, dẫn đến:

- **Nhiều thao tác thủ công**: Nhập liệu từ CSV, email, FAX vào Kintone; cập nhật tracking thủ công; tính rebate bằng Excel
- **Thiếu minh bạch realtime**: Không có dashboard tổng hợp; thông tin tồn kho, đơn hàng, thanh toán không đồng bộ
- **Dễ sai sót**: Duplicate orders, nhầm lẫn tồn kho, tính sai rebate do xử lý thủ công
- **Khó mở rộng**: Không thể scale khi thêm kênh bán hàng (Rakuten, Amazon) hoặc kho mới (岐阜)

### Problem Impact

**Operational Costs:**
- Nhân viên tốn 2-3 giờ/ngày cho data entry và reconciliation
- Lỗi do nhập liệu thủ công gây delay giao hàng, ảnh hưởng customer satisfaction
- Không có audit trail đầy đủ, khó troubleshoot khi có vấn đề

**Business Risks:**
- Duplicate orders → overselling → stockout → lost revenue
- Tính sai rebate → financial discrepancies → trust issues với đại lý
- Thiếu visibility → không thể optimize inventory và fulfillment costs

**Scalability Constraints:**
- Không thể handle peak load (sales events, seasonal spikes)
- Khó tích hợp thêm kênh bán hàng hoặc đối tác mới
- Manual processes không scale với growth

### Why Existing Solutions Fall Short

**Current State:**
- Shopify: Chỉ quản lý BtoC orders, không tích hợp với Kintone
- Kintone: Business apps rời rạc, không có realtime sync với Shopify
- Ship&Co: Chỉ tạo shipping labels, không auto-sync tracking
- Manual Excel: Tính rebate thủ công, dễ sai, không có version control

**Gaps:**
- Không có single source of truth cho orders, inventory, payments
- Thiếu automation giữa các hệ thống
- Không có idempotency → duplicate orders
- Không có realtime dashboard cho operations team

### Proposed Solution

**Silica-link Platform** - Nền tảng tích hợp thống nhất với 4 core capabilities:

1. **Realtime Order Processing**
   - Webhook pipeline: Shopify → Yoom → Rails → Kintone với idempotency
   - Auto inventory check trước khi confirm order
   - Support đa kênh: BtoC (no backorder), BtoB (partial shipment), FIS fulfillment, Affiliate

2. **Intelligent Inventory Management**
   - Realtime sync giữa Kintone và Shopify
   - Multi-warehouse routing (kho A 尼崎, kho B 岐阜)
   - Stock reservation và backorder management cho BtoB

3. **Automated Fulfillment**
   - Auto create picking tasks trong Kintone
   - Auto generate shipping labels via Ship&Co API
   - Auto sync tracking status về Shopify và notify customers

4. **Financial Automation**
   - Auto calculate rebate từ sales data (MWATER: 2円/本, SILICA: 5円→3円)
   - Payment reconciliation với GMO Bank API
   - Quarterly locking và dashboard realtime

### Key Differentiators

- **Brownfield Integration**: Tích hợp với hệ thống hiện có (Kintone, Ship&Co, GMO) thay vì thay thế
- **Idempotency-First**: Đảm bảo zero duplicate orders với Rails + Sidekiq + Redis pattern
- **Multi-Channel Native**: Một platform xử lý BtoC, BtoB, FIS, Affiliate với logic riêng cho từng kênh
- **Realtime Everything**: Dashboard, inventory sync, order status - tất cả realtime
- **Audit Trail Complete**: Mọi event được log vào S3 với metadata đầy đủ (ai/khi nào/cái gì/bao nhiêu/ở đâu)

---

## Target Users

### Primary Users

**1. Nhân viên Đơn hàng (受注担当)**
- **Current Pain**: Phải nhập order từ email/FAX thủ công, check tồn kho manual, dễ nhầm lẫn
- **Value**: Order tự động sync từ Shopify, realtime inventory check, zero duplicate
- **Technical Comfort**: Intermediate - quen với Kintone, cần UI đơn giản

**2. Nhân viên Kho (倉庫担当)**
- **Current Pain**: Không biết order nào cần ship, phải check nhiều nơi, dễ miss
- **Value**: Auto picking tasks trong Kintone, scan barcode để confirm, realtime tracking
- **Technical Comfort**: Basic - cần mobile-friendly scan UI

**3. Nhân viên Kế toán (経理担当)**
- **Current Pain**: Tính rebate thủ công bằng Excel, dễ sai, không có audit trail
- **Value**: Auto calculate rebate, payment reconciliation với GMO Bank, dashboard realtime
- **Technical Comfort**: Intermediate - quen với Excel, cần dashboard trực quan

**4. Đại lý (代理店) - BtoB Portal Users**
- **Current Pain**: Phải gửi order qua email, không biết trạng thái, phải gọi để check
- **Value**: Portal riêng để đặt hàng, xem lịch sử, reorder, track shipment realtime
- **Technical Comfort**: Basic - cần UI đơn giản, mobile-friendly

### Secondary Users

**5. FIS (Fulfillment Partner)**
- **Value**: Dashboard riêng để xem orders, fulfillment status, fees
- **Technical Comfort**: Basic

**6. Affiliate (取次店)**
- **Value**: Dashboard để track link performance, commission, conversion
- **Technical Comfort**: Basic

### User Journey

**BtoC Order Flow:**
1. Customer đặt hàng trên Shopify → Webhook realtime → Rails validate + check inventory
2. Nếu có hàng → Tạo order trong Kintone → Auto create picking task
3. Kho scan barcode → Confirm picking → Auto generate Ship&Co label
4. Ship hàng → Auto sync tracking về Shopify → Customer nhận notification

**BtoB Order Flow:**
1. Đại lý đăng nhập BtoB Portal → Chọn SKU → Review order → Submit
2. Order cần approval → Manager duyệt → Generate Proforma Invoice
3. Đại lý thanh toán → GMO Bank API confirm → Check inventory
4. Nếu đủ hàng → Ship ngay; Nếu thiếu → Partial shipment + backorder
5. Khi có hàng lại → Auto notify đại lý → Thanh toán phần còn lại → Ship tiếp

---

## Success Metrics

### Business Objectives

1. **Operational Efficiency**
   - Giảm 80% thời gian xử lý order (từ 2-3 giờ/ngày xuống <30 phút)
   - Zero duplicate orders
   - 99.9% webhook processing success rate

2. **Accuracy & Quality**
   - Zero manual data entry errors
   - 100% orders có audit trail
   - Rebate calculation accuracy: 100%

3. **Scalability**
   - Support 1000+ orders/hour (peak load)
   - Dễ dàng thêm kênh bán hàng mới (Rakuten, Amazon)
   - Multi-warehouse routing tự động

### Key Performance Indicators

**Order Processing:**
- Webhook processing latency: < 2 seconds (p95)
- Order-to-ship time: Giảm 50% (từ 2 ngày xuống 1 ngày)
- Inventory accuracy: 99.5%+

**Financial:**
- Payment reconciliation time: Giảm 90% (từ 1 tuần xuống <1 ngày)
- Rebate calculation time: Giảm 100% (từ manual → auto realtime)

**User Satisfaction:**
- Đại lý satisfaction: 90%+ (survey)
- Operations team adoption: 100% (tất cả nhân viên sử dụng hệ thống mới)

---

## MVP Scope

### Core Features

**Phase 1: Order Processing (MVP)**
1. Webhook pipeline Shopify → Yoom → Rails → Kintone với idempotency
2. Realtime inventory check trước khi confirm order
3. Auto create order trong Kintone Orders App
4. Support BtoC orders (no backorder)

**Phase 2: Fulfillment Automation**
5. Auto create picking tasks trong Kintone
6. Ship&Co API integration - auto generate shipping labels
7. Auto sync tracking status về Shopify

**Phase 3: BtoB Portal**
8. BtoB Portal với authentication
9. Order creation, review, approval workflow
10. Partial shipment + backorder support
11. Payment reconciliation với GMO Bank API

**Phase 4: Financial Automation**
12. Auto calculate rebate từ sales data
13. Quarterly locking và dashboard
14. Affiliate tracking và commission calculation

### Out of Scope for MVP

- Rakuten/Amazon integration (Phase 2)
- Multi-warehouse routing (Phase 2 - khi kho B 岐阜 đi vào hoạt động)
- ERP-style BOM + WIP tracking (Phase 2)
- Demand forecasting (Future)
- Digital twin simulation (Moonshot)

### MVP Success Criteria

- 100% BtoC orders từ Shopify được xử lý tự động (zero manual entry)
- 99.9% webhook processing success rate
- Zero duplicate orders trong production
- All orders có audit trail trong S3
- Operations team có thể sử dụng hệ thống mà không cần training nhiều

### Future Vision

**Phase 2 Enhancements:**
- Multi-channel integration (Rakuten, Amazon)
- Multi-warehouse intelligent routing
- ERP-style production management (BOM, WIP)
- Advanced analytics và reporting

**Long-term Vision:**
- Demand forecasting và auto production planning
- Open API platform cho partners (FIS, OEM, Affiliate)
- AI-powered inventory optimization

---

## Technical Preferences

**Architecture Pattern:**
- Event-driven architecture với webhook pipeline
- Rails + Sidekiq + Redis cho reliable processing
- Idempotency-first design để đảm bảo data integrity

**Integration Stack:**
- Shopify: Webhook API
- Yoom: Gateway/orchestrator
- Kintone: REST API cho business apps
- Ship&Co: API cho shipping labels
- GMO Bank: API cho payment reconciliation
- AWS S3: Audit log storage

**Security & Compliance:**
- HMAC signature verification cho webhooks
- Encrypt PII trong audit logs
- Full audit trail (ai/khi nào/cái gì/bao nhiêu/ở đâu)
- Data retention policies

**Scalability:**
- Horizontal scaling với multiple Rails workers
- Redis cluster cho idempotency tracking
- Queue-based architecture để handle spikes

---

## Organizational Context

**Stakeholders:**
- **CREDIE**: End user, business owner
- **MediaBirth**: Project sponsor, intermediary
- **Amcolab**: Development team

**Strategic Alignment:**
- Phù hợp với mục tiêu digital transformation của CREDIE
- Giảm operational costs và tăng efficiency
- Enable scalability cho business growth

**Change Management:**
- Training cho operations team (nhân viên đơn hàng, kho, kế toán)
- Phased rollout để minimize disruption
- Support và documentation đầy đủ

**Compliance:**
- Audit requirements (full trail)
- Data retention policies
- PII handling compliance

---

## Risks and Assumptions

### Key Risks

1. **Technical Risks:**
   - Redis failure → Mitigation: Redis cluster với replication
   - Kintone API rate limits → Mitigation: Rate limiting và queue throttling
   - Webhook reliability → Mitigation: Retry mechanism với DLQ

2. **User Adoption Risks:**
   - Nhân viên không quen với hệ thống mới → Mitigation: Training, UI đơn giản, support
   - Đại lý không sử dụng BtoB Portal → Mitigation: Onboarding, incentives

3. **Integration Risks:**
   - Shopify webhook changes → Mitigation: Version pinning, monitoring
   - Kintone API changes → Mitigation: Abstraction layer, versioning

### Critical Assumptions

1. Shopify webhooks reliable và có retry mechanism
2. Kintone API stable và có rate limits documented
3. Operations team sẵn sàng adopt hệ thống mới
4. Đại lý có thể sử dụng BtoB Portal (basic tech skills)

### Open Questions

1. Cần research thêm về Kintone API rate limits và best practices
2. Cần validate với CREDIE về UI/UX preferences cho BtoB Portal
3. Cần confirm về data retention policies và compliance requirements

---

## Supporting Materials

**Input Documents Used:**
- Brainstorming Session Results (27 ideas, key themes, immediate opportunities)
- Technical Research Report (Webhook reliability patterns với Rails + Sidekiq)
- Project Overview (Vấn đề, mục tiêu, kết quả mong đợi)

**Key Insights Incorporated:**
- BtoC và BtoB cần hai cơ chế tồn kho khác nhau
- Prepayment + approval là xương sống để bảo vệ dòng tiền
- Idempotency là critical requirement cho webhook processing
- Rails + Sidekiq + Redis là recommended technical approach

---

_This Product Brief captures the vision and requirements for Silica-link._

_It was created through collaborative discovery and reflects the unique needs of this Enterprise Brownfield project._

_Next: PRD workflow will transform this brief into detailed product requirements._

