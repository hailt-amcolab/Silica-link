# Silica-link - Product Requirements Document

**Author:** Potato
**Date:** 2025-11-19
**Version:** 1.0

---

## Executive Summary

Silica-link là nền tảng tích hợp tự động hóa toàn bộ chuỗi giá trị **Đặt hàng → Giao hàng → Nhập hàng → Quản lý hoa hồng/cổ tức** cho CREDIE. Hệ thống thay thế các quy trình thủ công rời rạc bằng automation realtime, tích hợp seamless với hệ thống hiện có (Kintone, Shopify, Ship&Co, GMO Bank) để đảm bảo zero duplicate orders, tăng năng suất operations team, và enable scalability cho business growth.

**Vision Alignment:**
Mục tiêu cốt lõi của Silica-link là **tự động hóa 100% quy trình thủ công** hiện tại, từ việc nhập order từ email/FAX, check tồn kho manual, tính rebate bằng Excel, đến việc tạo shipping labels và sync tracking. Hệ thống sẽ giúp operations team tiết kiệm 80% thời gian xử lý (từ 2-3 giờ/ngày xuống <30 phút), đồng thời đảm bảo accuracy 100% và realtime visibility cho tất cả stakeholders.

### What Makes This Special

**Điểm khác biệt cốt lõi: Tích hợp Brownfield với Kintone**

Silica-link không phải là hệ thống thay thế hoàn toàn, mà là **integration layer thông minh** kết nối các hệ thống hiện có của CREDIE. Thay vì yêu cầu migration dữ liệu phức tạp hoặc thay đổi quy trình làm việc hiện tại, Silica-link tích hợp seamless với:

- **Kintone**: Business apps hiện có (Orders, Inventory, Audit_Log) được enhance với automation, không cần rebuild
- **Shopify**: Webhook pipeline realtime đảm bảo zero duplicate với idempotency-first design
- **Ship&Co**: Auto generate shipping labels thay vì manual CSV upload
- **GMO Bank**: Payment reconciliation tự động thay vì manual Excel

Điều này tạo ra **giá trị độc đáo**: Operations team có thể tiếp tục sử dụng Kintone như quen thuộc, nhưng với sức mạnh automation đằng sau. Không cần training lại, không cần thay đổi workflow, chỉ cần enjoy productivity boost.

---

## Project Classification

**Technical Type:** saas_b2b
**Domain:** general
**Complexity:** medium

**Project Classification Details:**

Silica-link được phân loại là **SaaS B2B Platform** vì:

- **Multi-tenant architecture**: Hỗ trợ nhiều kênh bán hàng (Shopify CREDIE shop, Shopify FIS shop, tương lai Rakuten/Amazon)
- **Enterprise features**: Permission model cho đại lý, approval workflows, audit trail đầy đủ
- **Integration-first**: Tích hợp với nhiều hệ thống bên ngoài (Kintone, Shopify, Ship&Co, GMO Bank)
- **Portal-based**: BtoB Portal riêng cho đại lý để đặt hàng, track orders, xem lịch sử

**Domain Complexity: Medium**

Mặc dù không thuộc domain phức tạp như healthcare/fintech, Silica-link có **medium complexity** do:

- **Multiple integrations**: 5+ hệ thống cần tích hợp (Shopify, Yoom, Kintone, Ship&Co, GMO Bank)
- **Complex business logic**: Multi-channel order processing với logic khác nhau cho BtoC (no backorder) vs BtoB (partial shipment + prepayment)
- **Realtime requirements**: Webhook processing, inventory sync, dashboard updates - tất cả phải realtime
- **Idempotency critical**: Zero duplicate orders là requirement không thể thỏa hiệp
- **Audit compliance**: Full audit trail (ai/khi nào/cái gì/bao nhiêu/ở đâu) cho mọi event

**Key Technical Challenges:**
- Webhook reliability và idempotency (đã có research: Rails + Sidekiq + Redis)
- Realtime inventory sync giữa Shopify và Kintone
- Multi-channel order routing với business rules phức tạp
- Payment reconciliation automation với GMO Bank API

---

## Success Criteria

**Thành công sau 3 tháng triển khai:**

1. **100% BtoC orders tự động**
   - Tất cả orders từ Shopify được xử lý tự động, zero manual entry
   - Webhook processing success rate: 99.9%+
   - Zero duplicate orders trong production

2. **Operations team hài lòng**
   - Nhân viên đơn hàng tiết kiệm 80% thời gian (từ 2-3 giờ/ngày xuống <30 phút)
   - Nhân viên kho có picking tasks tự động, scan barcode để confirm
   - Nhân viên kế toán có rebate calculation tự động, payment reconciliation realtime
   - User satisfaction survey: 90%+ positive feedback

3. **Business Impact**
   - Order-to-ship time giảm 50% (từ 2 ngày xuống 1 ngày)
   - Payment reconciliation time giảm 90% (từ 1 tuần xuống <1 ngày)
   - Zero manual data entry errors
   - 100% orders có audit trail

4. **Technical Excellence**
   - Webhook processing latency: < 2 seconds (p95)
   - System uptime: 99.9%+
   - All integrations stable và reliable
   - Dashboard realtime với < 1 second refresh

**Success không chỉ là metrics, mà là operations team thực sự yêu thích sử dụng hệ thống mới và cảm thấy productivity được cải thiện đáng kể.**

---

## Product Scope

### MVP - Minimum Viable Product

**Phase 1: Order Processing Automation (MVP Core)**
1. Webhook pipeline Shopify → Yoom → Rails → Kintone với idempotency
2. Realtime inventory check trước khi confirm order
3. Auto create order trong Kintone Orders App
4. Support BtoC orders (no backorder - hard stop khi hết hàng)
5. Audit log tất cả events vào S3

**Phase 2: Fulfillment Automation**
6. Auto create picking tasks trong Kintone khi order confirmed
7. Ship&Co API integration - auto generate shipping labels
8. Auto sync tracking status về Shopify và notify customers
9. Dashboard realtime cho operations team

**Phase 2.5: Warehouse Management (Core)**
10. Nhập kho nguyên vật liệu từ supplier với scan barcode vị trí kệ
11. Quản lý tồn kho tới cấp vị trí kệ (棚) và kho
12. Scan barcode để track vị trí tồn kho và confirm picking
13. Quản lý LOT và ngày sản xuất
14. Xuất kho không gắn đơn hàng với lý do xuất kho
15. Stock Movement tracking để truy vết di chuyển hàng hóa

**Phase 3: BtoB Portal**
16. BtoB Portal với authentication (cho đại lý)
17. Order creation, review, approval workflow
18. Partial shipment + backorder support (BtoB specific)
19. Payment reconciliation với GMO Bank API
20. Proforma Invoice generation

**Phase 4: Financial Automation**
21. Auto calculate rebate từ sales data (MWATER: 2円/本, SILICA: 5円→3円 khi cumulative ≥ 5,000万)
22. Quarterly locking và dashboard realtime
23. Affiliate tracking và commission calculation

**Phase 5: Advanced Warehouse Management (Post-MVP)**
24. Kiểm kê tồn kho (inventory count) với so sánh tồn kho thực tế vs hệ thống
25. Multi-warehouse routing (kho A 尼崎, kho B 岐阜) với route rules tự động
26. Inventory balancing giữa các kho
27. Quản lý tồn kho tách biệt cho OEM

**MVP Success Criteria:**
- 100% BtoC orders từ Shopify được xử lý tự động (zero manual entry)
- 99.9% webhook processing success rate
- Zero duplicate orders trong production
- All orders có audit trail trong S3
- Operations team có thể sử dụng hệ thống mà không cần training nhiều

### Growth Features (Post-MVP)

**Multi-Channel Expansion:**
- Rakuten integration
- Amazon integration
- Support thêm kênh bán hàng mới dễ dàng

**Multi-Warehouse Intelligence:**
- Multi-warehouse routing (kho A 尼崎, kho B 岐阜)
- Route rules tự động dựa trên SKU và địa chỉ giao hàng
- Inventory balancing giữa các kho

**Advanced Analytics:**
- Demand forecasting
- Inventory optimization recommendations
- Sales performance analytics
- Cost analysis và profitability tracking

**ERP-Style Production Management:**
- BOM (Bill of Materials) management
- WIP (Work In Progress) tracking
- Production planning và scheduling

### Vision (Future)

**AI-Powered Optimization:**
- AI-powered inventory optimization
- Predictive demand forecasting
- Auto production planning dựa trên sales trends

**Open API Platform:**
- Public API cho partners (FIS, OEM, Affiliate)
- Webhook subscriptions cho third-party integrations
- Marketplace cho extensions và plugins

**Digital Twin Simulation:**
- Simulate inventory scenarios
- Test "what-if" scenarios cho production planning
- Optimize warehouse routing và fulfillment costs

---

## saas_b2b Specific Requirements

**Multi-Tenancy Architecture:**
- Mỗi kênh bán hàng (Shopify CREDIE shop, Shopify FIS shop) là một tenant riêng
- Data isolation đảm bảo orders từ shop này không lẫn với shop khác
- Shared infrastructure nhưng logic riêng cho từng tenant

**Permission Model:**
- **Operations Team**: Full access để quản lý orders, inventory, fulfillment
- **Đại lý (BtoB Portal)**: Chỉ xem và tạo orders của chính họ, không thấy orders của đại lý khác
- **FIS**: Dashboard riêng để xem orders và fulfillment status của shop FIS
- **Affiliate**: Dashboard để track link performance và commission

**Subscription Tiers:**
- Hiện tại: Single tenant (CREDIE)
- Tương lai: Có thể mở rộng multi-tenant cho nhiều công ty khác

**Integration Requirements:**
- **Shopify**: Webhook API cho order events (orders/create, orders/updated, orders/paid, orders/cancelled, orders/fulfilled)
- **Yoom**: Gateway/orchestrator cho webhook pipeline
- **Kintone**: REST API cho Orders App, Inventory App, Audit_Log App
- **Ship&Co**: API cho shipping label generation
- **GMO Bank**: API cho payment reconciliation
- **AWS S3**: Audit log storage

**Compliance Requirements:**
- Full audit trail cho mọi event (ai/khi nào/cái gì/bao nhiêu/ở đâu)
- Data retention policies
- PII handling compliance (encrypt sensitive data trong audit logs)
- HMAC signature verification cho webhooks

---

## User Experience Principles

**Simplicity First:**
- Operations team đã quen với Kintone → UI phải intuitive, không cần training nhiều
- BtoB Portal cho đại lý phải đơn giản, mobile-friendly
- Dashboard realtime phải trực quan, dễ đọc

**Realtime Everything:**
- Inventory updates realtime
- Order status updates realtime
- Dashboard refresh realtime (< 1 second)
- Notifications realtime cho important events

**Error Prevention:**
- Idempotency đảm bảo zero duplicate orders
- Validation trước khi confirm order
- Clear error messages khi có vấn đề
- Manual review interface cho failed events

### Key Interactions

**Nhân viên Đơn hàng:**
- Dashboard realtime hiển thị orders mới từ Shopify
- Click vào order để xem chi tiết và status
- Manual review interface cho orders cần attention

**Đại lý (BtoB Portal):**
- Login → Xem lịch sử orders
- Tạo order mới: Chọn SKU → Review → Submit
- Track shipment realtime với tracking number
- Reorder từ lịch sử

**Nhân viên Kho:**
- Picking tasks tự động xuất hiện trong Kintone
- Scan barcode để confirm picking
- Mobile-friendly interface cho scan operations

---

## Root Cause Analysis & User Journey Insights

### Root Cause Analysis (Five Whys)

**Problem:** Operations team tốn 2-3 giờ/ngày cho manual data entry và reconciliation

**Why Analysis:**
1. **Why 1:** Phải nhập liệu thủ công vì orders từ Shopify không tự động sync vào Kintone
2. **Why 2:** Không có auto sync vì thiếu webhook pipeline và idempotency mechanism
3. **Why 3:** Không có webhook pipeline vì hệ thống được thiết kế cho manual process
4. **Why 4:** Thiết kế cho manual process vì khi bắt đầu quy mô nhỏ, manual đủ dùng
5. **Why 5 (Root Cause):** Không upgrade khi business scale vì thiếu integration layer thông minh

**Root Cause Insights:**
- **Thiếu integration layer** giữa các hệ thống rời rạc (Shopify, Kintone, Ship&Co, GMO Bank)
- **Thiếu event-driven architecture** để handle realtime sync
- **Thiếu idempotency mechanism** để đảm bảo data integrity khi automate

**Architectural Requirements Derived:**
- Hệ thống phải có webhook pipeline Shopify → Yoom → Rails → Kintone với idempotency
- Hệ thống phải có integration layer abstract Kintone API để dễ maintain và extend
- Hệ thống phải có event-driven architecture để support realtime sync

### User Journey Mapping

#### Journey 1: Nhân viên Đơn hàng (受注担当)

**Stages & Pain Points:**

1. **Nhận Order**
   - Current: Email/FAX từ đại lý hoặc CSV từ Shopify
   - Pain: Phải check nhiều nơi, dễ miss orders, không biết order nào đã xử lý
   - Opportunity: Auto sync orders từ Shopify → Kintone (zero manual entry)

2. **Check Tồn Kho**
   - Current: Manual search SKU trong Kintone Inventory App
   - Pain: Tồn kho có thể đã thay đổi sau khi check, dễ oversell
   - Opportunity: Realtime inventory check trước khi confirm order

3. **Tạo Order trong Kintone**
   - Current: Nhập thông tin order thủ công (customer, items, quantities)
   - Pain: Phải nhập lại thông tin đã có trong CSV, tốn thời gian, dễ nhầm lẫn
   - Opportunity: Auto create order trong Kintone từ webhook data

4. **Confirm Order**
   - Current: Review và confirm order manual
   - Pain: Không có validation tự động, phải tự check
   - Opportunity: Auto validation để prevent errors, dashboard realtime để track

**Key Requirements:**
- Dashboard realtime hiển thị orders mới từ Shopify
- Auto validation trước khi confirm
- Manual review interface cho orders cần attention

#### Journey 2: Đại lý (BtoB Portal User)

**Stages & Pain Points:**

1. **Cần Đặt Hàng**
   - Current: Gửi email/FAX với danh sách SKU và số lượng
   - Pain: Không biết giá, không biết tồn kho, phải chờ confirmation
   - Opportunity: BtoB Portal để đặt hàng trực tiếp với realtime inventory visibility

2. **Chờ Confirmation**
   - Current: Chờ email/phone từ CREDIE
   - Pain: Không có visibility, phải gọi để check status
   - Opportunity: Self-service portal để track order status realtime

3. **Thanh Toán**
   - Current: Bank transfer, gửi proof of payment
   - Pain: Phải manual track payment, không có confirmation tự động
   - Opportunity: Auto generate Proforma Invoice, payment tracking tự động với GMO Bank API

4. **Chờ Giao Hàng**
   - Current: Gọi CREDIE để hỏi tracking number
   - Pain: Không có self-service tracking, phải depend on CREDIE
   - Opportunity: Self-service tracking với realtime updates

**Key Requirements:**
- BtoB Portal với authentication cho đại lý
- Realtime inventory visibility khi đặt hàng
- Auto generate Proforma Invoice khi order được approve
- Payment tracking tự động và confirmation
- Self-service tracking với realtime shipment updates

---

## Functional Requirements

**Organization:** Functional Requirements được tổ chức theo capability areas, không theo technology layers. Mỗi FR là một testable capability statement ở đúng altitude (WHAT, không phải HOW).

### Order Processing & Automation

**FR1:** Hệ thống phải có webhook pipeline Shopify → Yoom → Rails → Kintone với idempotency mechanism để đảm bảo zero duplicate orders

**FR2:** Hệ thống phải tự động sync orders từ Shopify vào Kintone Orders App (zero manual entry cho nhân viên đơn hàng)

**FR3:** Hệ thống phải check inventory realtime từ Kintone Inventory App trước khi confirm order

**FR4:** Hệ thống phải tự động tạo order record trong Kintone Orders App khi nhận webhook từ Shopify

**FR5:** Hệ thống phải validate order data (customer, line items, shipping address) trước khi process

**FR6:** Hệ thống phải support BtoC orders với hard stop khi hết hàng (no backorder)

**FR7:** Hệ thống phải support BtoB orders với partial shipment và backorder capability

**FR8:** Hệ thống phải có integration layer abstract Kintone API để dễ maintain và extend

**FR9:** Hệ thống phải có event-driven architecture để support realtime sync giữa các hệ thống

**FR10:** Hệ thống phải log tất cả webhook events (raw payload) vào S3 với metadata đầy đủ

### Fulfillment Automation

**FR11:** Hệ thống phải tự động tạo picking tasks trong Kintone khi order được confirmed

**FR12:** Hệ thống phải tự động generate shipping labels qua Ship&Co API khi picking completed

**FR13:** Hệ thống phải tự động sync tracking status về Shopify và notify customers

**FR14:** Hệ thống phải support scan barcode để confirm picking (mobile-friendly interface)

**FR15:** Hệ thống phải tự động update order status trong Kintone khi shipment status thay đổi

### Warehouse Management

**FR56:** Nhân viên kho có thể nhập kho nguyên vật liệu từ supplier: Scan barcode vị trí kệ (棚) và mã hàng để ghi nhận chính xác vị trí tồn

**FR57:** Nhân viên kho có thể kiểm đếm và nhập kho thành phẩm sau khi sản xuất

**FR58:** Hệ thống phải quản lý tồn kho tới cấp vị trí kệ (棚) và kho (kho chính 尼崎, kho Marusho 丸正運送, kho B 岐阜)

**FR59:** Hệ thống phải support scan barcode để track vị trí tồn kho (vị trí kệ + mã hàng)

**FR60:** Hệ thống phải quản lý LOT và ngày sản xuất cho nguyên liệu và thành phẩm

**FR61:** Hệ thống phải support xuất kho không gắn với đơn hàng (hỏng hóc, lỗi, dùng nội bộ, xuất mẫu) với bắt buộc chọn và ghi lý do xuất kho

**FR62:** Hệ thống phải có danh sách lý do xuất kho: lỗi hỏng, thất lạc, xuất mẫu, kiểm định, dùng nội bộ, khác

**FR63:** Hệ thống phải quản lý việc xuất mẫu (営業サンプル) và điều chỉnh tồn kho

**FR64:** Hệ thống phải ghi nhận Stock Movement để truy vết di chuyển hàng hóa (ai/khi nào/từ đâu/đến đâu/bao nhiêu)

**FR65:** Hệ thống phải support kiểm kê tồn kho (inventory count) với khả năng so sánh tồn kho thực tế vs tồn kho hệ thống

**FR66:** Hệ thống phải support điều chỉnh tồn kho sau khi kiểm kê với audit trail đầy đủ

**FR67:** Hệ thống phải quản lý multi-warehouse: Kho chính (尼崎), Kho Marusho (丸正運送), Kho B (岐阜 - tương lai)

**FR68:** Hệ thống phải support route rules để tự động quyết định ship từ kho nào dựa trên SKU và địa chỉ giao hàng

**FR69:** Hệ thống phải support inventory balancing giữa các kho (chuyển hàng giữa kho chính và kho Marusho)

**FR70:** Hệ thống phải quản lý tồn kho cho cả nguyên liệu (資材, 原料) và thành phẩm (sản phẩm hoàn thiện)

**FR71:** Nhân viên kho có thể xem picking list từ đơn hàng với thông tin vị trí kệ (棚) và LOT của sản phẩm cần lấy

**FR72:** Hệ thống phải tự động cập nhật tồn kho khi có xuất nhập kho (realtime)

**FR73:** Hệ thống phải support quản lý tồn kho cho OEM (nguyên liệu dành riêng cho OEM có thể quản lý tách biệt theo từng OEM)

### BtoB Portal & Self-Service

**FR74:** Đại lý có thể đăng nhập BtoB Portal với authentication riêng

**FR75:** Đại lý có thể xem lịch sử orders của chính họ (không thấy orders của đại lý khác)

**FR76:** Đại lý có thể tạo order mới qua BtoB Portal: Chọn SKU → Review → Submit

**FR77:** Đại lý có thể xem realtime inventory availability khi đặt hàng

**FR78:** Đại lý có thể track order status và shipment realtime với tracking number

**FR79:** Đại lý có thể reorder từ lịch sử orders trước đó

**FR80:** Hệ thống phải tự động generate Proforma Invoice khi BtoB order được approve

**FR81:** Hệ thống phải support approval workflow cho BtoB orders (manager review trước khi confirm)

**FR82:** Hệ thống phải support partial shipment: Giao phần có hàng trước, backorder phần còn lại

**FR83:** Hệ thống phải tự động notify đại lý khi backorder items có hàng lại

### Payment & Financial Automation

**FR84:** Hệ thống phải tự động reconcile payments với GMO Bank API

**FR85:** Hệ thống phải tự động confirm order khi payment được verified từ GMO Bank

**FR86:** Hệ thống phải tự động calculate rebate từ sales data theo rules:
   - MWATER: 2円/本 (cố định)
   - SILICA: 5円/本 → 3円/本 khi cumulative ≥ 5,000万

**FR87:** Hệ thống phải support quarterly locking cho rebate data

**FR88:** Hệ thống phải tự động track affiliate commissions từ sales data

**FR89:** Hệ thống phải có dashboard realtime để xem rebate và commission status

### Dashboard & Visibility

**FR90:** Operations team có dashboard realtime hiển thị orders mới từ Shopify (< 1 second refresh)

**FR91:** Operations team có thể click vào order để xem chi tiết và status

**FR92:** Operations team có manual review interface cho orders cần attention (failed events, exceptions)

**FR93:** FIS có dashboard riêng để xem orders và fulfillment status của shop FIS

**FR94:** Affiliate có dashboard để track link performance và commission

**FR95:** Hệ thống phải hiển thị realtime inventory updates trên dashboard

**FR96:** Hệ thống phải hiển thị realtime order status updates trên dashboard

### Audit & Compliance

**FR97:** Hệ thống phải có full audit trail cho mọi event (ai/khi nào/cái gì/bao nhiêu/ở đâu)

**FR98:** Hệ thống phải encrypt sensitive data (PII) trong audit logs

**FR99:** Hệ thống phải support data retention policies

**FR100:** Hệ thống phải verify HMAC signature cho tất cả webhooks từ Shopify

### Error Handling & Reliability

**FR101:** Hệ thống phải có retry mechanism với exponential backoff cho failed webhooks

**FR102:** Hệ thống phải có Dead Letter Queue (DLQ) cho failed events cần manual review

**FR103:** Hệ thống phải có alerting khi retry threshold exceeded

**FR104:** Hệ thống phải có health check endpoints để monitor system status

**FR105:** Hệ thống phải có clear error messages và logging cho troubleshooting

**FR106:** Hệ thống phải có manual review interface cho failed events (DLQ management)

**FR107:** Hệ thống phải có rollback mechanism để quay lại manual process nếu cần (emergency fallback)

**FR108:** Hệ thống phải optimize value chain linkages để tăng efficiency (realtime validation, auto tracking sync, auto confirm khi payment verified)

**FR109:** Hệ thống phải support realtime value chain visibility (dashboard hiển thị toàn bộ order-to-cash flow)

**FR110:** Hệ thống phải measure và report value chain metrics (order-to-cash time, fulfillment efficiency, payment reconciliation time)

**FR111:** Hệ thống phải có stakeholder-specific dashboards (Operations Team, Đại lý, FIS, Affiliate) với permissions phù hợp

**FR112:** Hệ thống phải support feedback collection từ stakeholders (surveys, feedback forms)

**FR113:** Hệ thống phải có reporting capabilities cho management (ROI metrics, business impact, cost savings)

---

## Risk Analysis & Mitigation (Pre-mortem)

### Failure Scenario Analysis

**Hypothetical Failure:** Dự án thất bại sau 6 tháng - Operations team vẫn phải xử lý manual, duplicate orders vẫn xảy ra, hệ thống không được sử dụng

### Contributing Factors & Preventive Measures

#### 1. Webhook Pipeline Failure

**Contributing Factors:**
- Shopify webhooks không reliable, nhiều orders bị miss
- Idempotency mechanism không hoạt động đúng → duplicate orders
- Redis failure → mất idempotency keys → duplicate processing

**Warning Signs:**
- Webhook processing success rate < 90%
- Nhiều duplicate orders trong production
- High error rate từ webhook processing

**Preventive Measures:**
- **Idempotency Redundancy:** Redis cluster với replication, fallback to database idempotency check
- **Comprehensive Monitoring:** Real-time monitoring webhook success rate, duplicate detection alerts
- **Load Testing:** Test với 1000+ orders/hour để validate scalability
- **Retry Strategy:** Exponential backoff với DLQ cho failed events

#### 2. Integration Complexity

**Contributing Factors:**
- Kintone API rate limits không được handle đúng → orders bị drop
- Ship&Co API changes → shipping labels không generate được
- GMO Bank API không stable → payment reconciliation sai

**Warning Signs:**
- High error rate từ integrations
- Operations team phải manual fix integration issues
- Frequent API timeout errors

**Preventive Measures:**
- **Circuit Breakers:** Circuit breakers cho tất cả external integrations
- **Rate Limiting:** Rate limiting và queue throttling cho Kintone API
- **API Versioning:** Version pinning và abstraction layer cho API changes
- **Integration Health Checks:** Automated health checks cho tất cả integrations

#### 3. User Adoption Failure

**Contributing Factors:**
- Operations team không trust hệ thống → vẫn làm manual song song
- BtoB Portal quá phức tạp → đại lý không sử dụng
- Dashboard không realtime → operations team không thấy value

**Warning Signs:**
- Low usage metrics
- Operations team vẫn prefer manual process
- User satisfaction survey < 70%

**Preventive Measures:**
- **Phased Rollout:** Start với 1 shop, validate, rồi mở rộng
- **Training & Support:** Comprehensive training cho operations team, support hotline
- **UI/UX Focus:** BtoB Portal phải đơn giản, mobile-friendly, intuitive
- **Success Metrics:** Track usage metrics, user satisfaction surveys

#### 4. Data Integrity Issues

**Contributing Factors:**
- Inventory sync không realtime → overselling
- Audit logs không đầy đủ → không thể troubleshoot
- Payment reconciliation sai → financial discrepancies

**Warning Signs:**
- Inventory accuracy < 95%
- Payment mismatches
- Missing audit logs

**Preventive Measures:**
- **Realtime Sync:** Event-driven architecture với immediate inventory updates
- **Audit Trail:** Comprehensive logging với S3 storage, searchable audit logs
- **Validation:** Multiple validation layers trước khi confirm orders
- **Reconciliation:** Automated daily reconciliation với GMO Bank

#### 5. Scalability Problems

**Contributing Factors:**
- System không handle peak load (sales events) → orders bị delay
- Queue backlog → webhooks bị timeout
- Database performance issues → slow dashboard

**Warning Signs:**
- High latency during peak hours
- Queue depth warnings
- Dashboard slow response times

**Preventive Measures:**
- **Horizontal Scaling:** Multiple Rails workers, Redis cluster
- **Queue Management:** Priority queues, auto-scaling based on queue depth
- **Database Optimization:** Indexing, connection pooling, query optimization
- **Caching:** Redis caching cho inventory data, dashboard data

---

## Non-Functional Requirements

### Performance

**NFR1:** Webhook processing latency: < 2 seconds (p95)

**NFR2:** Dashboard refresh time: < 1 second

**NFR3:** System phải support 1000+ orders/hour (peak load)

**NFR4:** Order-to-ship time: Giảm 50% (từ 2 ngày xuống 1 ngày)

**NFR5:** Inventory sync latency: < 500ms (realtime)

### Reliability

**NFR6:** Webhook processing success rate: 99.9%+

**NFR7:** System uptime: 99.9%+

**NFR8:** Zero duplicate orders trong production

**NFR9:** Zero data loss (mọi webhook phải được lưu trước khi process)

**NFR10:** Idempotency mechanism phải có redundancy (Redis cluster + database fallback)

### Scalability

**NFR11:** System phải support horizontal scaling (multiple Rails workers)

**NFR12:** Redis cluster với replication để handle scale

**NFR13:** Queue-based architecture để handle spikes

**NFR14:** Database connection pooling và query optimization

**NFR15:** Redis caching cho inventory data và dashboard data

### Security

**NFR16:** HMAC signature verification mandatory cho tất cả webhooks

**NFR17:** Encrypt sensitive data (PII) trong audit logs

**NFR18:** Rate limiting để prevent abuse

**NFR19:** IP whitelist cho Shopify webhook IPs (nếu có thể)

**NFR20:** Secure authentication cho BtoB Portal

### Maintainability

**NFR21:** Comprehensive monitoring và alerting cho tất cả critical paths

**NFR22:** Health check endpoints để monitor system status

**NFR23:** Clear error messages và logging cho troubleshooting

**NFR24:** Documentation đầy đủ cho operations team

**NFR25:** Circuit breakers cho tất cả external integrations

### Integration Resilience

**NFR26:** Rate limiting và queue throttling cho Kintone API

**NFR27:** API versioning và abstraction layer để handle API changes

**NFR28:** Integration health checks automated cho tất cả integrations

**NFR29:** Retry mechanism với exponential backoff cho failed integrations

**NFR30:** Dead Letter Queue (DLQ) cho failed events cần manual review

### User Experience

**NFR31:** BtoB Portal phải mobile-friendly và intuitive

**NFR32:** Dashboard phải realtime với < 1 second refresh

**NFR33:** Error messages phải clear và actionable

**NFR34:** System phải support phased rollout (start với 1 shop, validate, mở rộng)

### Data Integrity

**NFR35:** Inventory accuracy: 99.5%+

**NFR36:** Full audit trail cho mọi event (ai/khi nào/cái gì/bao nhiêu/ở đâu)

**NFR37:** Automated daily reconciliation với GMO Bank

**NFR38:** Multiple validation layers trước khi confirm orders

**NFR39:** Searchable audit logs trong S3

---

## Value Chain Analysis

### Order-to-Cash Value Chain

**Primary Activities (Hoạt động chính tạo giá trị):**

#### 1. Order Intake
- **Current State:** Manual (email/FAX/CSV) → Nhân viên đơn hàng nhập vào Kintone
- **Value Created:** Order được capture và recorded
- **Current Cost:** 2-3 giờ/ngày manual labor, high error rate
- **Target State:** Auto sync từ Shopify → Zero manual entry, zero errors
- **Value Improvement:** High (từ Low → High value)

#### 2. Inventory Check
- **Current State:** Manual check trong Kintone Inventory App
- **Value Created:** Đảm bảo có hàng trước khi confirm
- **Current Cost:** Time delay, risk of overselling (stale data)
- **Target State:** Realtime inventory check → Instant validation, prevent overselling
- **Value Improvement:** High (từ Medium → High value)

#### 3. Order Confirmation
- **Current State:** Manual review và confirm trong Kintone
- **Value Created:** Order được approved và ready for fulfillment
- **Current Cost:** Manual review time, human error
- **Target State:** Auto validation → Instant confirmation cho valid orders
- **Value Improvement:** High (từ Low → High value)

#### 4. Fulfillment
- **Current State:** Manual picking tasks, manual CSV upload cho Ship&Co
- **Value Created:** Hàng được đóng gói và ship
- **Current Cost:** Manual labor, delay trong picking và shipping
- **Target State:** Auto picking tasks, auto shipping labels → Faster fulfillment
- **Value Improvement:** High (từ Medium → High value)

#### 5. Shipping & Tracking
- **Current State:** Manual sync tracking về Shopify
- **Value Created:** Customer biết khi nào nhận hàng
- **Current Cost:** Manual tracking update, customer inquiries
- **Target State:** Auto sync tracking → Better customer experience
- **Value Improvement:** High (từ Low → High value)

#### 6. Payment Processing
- **Current State:** Manual Excel để reconcile với GMO Bank
- **Value Created:** Payment được confirmed và recorded
- **Current Cost:** 1 tuần delay, manual errors
- **Target State:** Auto reconciliation → Instant payment confirmation
- **Value Improvement:** High (từ Low → High value)

#### 7. Rebate Calculation
- **Current State:** Manual Excel calculation
- **Value Created:** Đại lý nhận rebate đúng
- **Current Cost:** Manual calculation, errors, quarterly delay
- **Target State:** Auto calculation → Realtime rebate tracking
- **Value Improvement:** High (từ Low → High value)

### Support Activities (Hoạt động hỗ trợ)

#### 1. Integration Layer
- **Value:** Kết nối các hệ thống rời rạc (Shopify, Kintone, Ship&Co, GMO Bank)
- **Cost:** Development và maintenance
- **Opportunity:** Reusable integration layer → Easy to extend cho future integrations

#### 2. Audit & Compliance
- **Value:** Full traceability, compliance với audit requirements
- **Cost:** Storage và processing
- **Opportunity:** Automated audit trail → Zero manual effort, searchable logs

#### 3. Monitoring & Alerting
- **Value:** Early detection của issues, proactive resolution
- **Cost:** Infrastructure và monitoring tools
- **Opportunity:** Prevent failures trước khi impact operations

### Value Chain Linkages & Optimization

**Critical Linkages:**

1. **Order Intake ↔ Inventory Check**
   - Current: Sequential, manual
   - Optimized: Realtime validation ngay khi order được sync
   - Benefit: Instant feedback, prevent overselling

2. **Fulfillment ↔ Shipping**
   - Current: Manual tracking update
   - Optimized: Auto tracking sync khi shipment status thay đổi
   - Benefit: Better customer experience, reduce inquiries

3. **Payment ↔ Order Confirmation**
   - Current: Manual verification
   - Optimized: Auto confirm order khi payment verified từ GMO Bank
   - Benefit: Faster order processing, reduce manual work

4. **Sales Data ↔ Rebate Calculation**
   - Current: Manual quarterly calculation
   - Optimized: Auto calculation realtime từ sales data
   - Benefit: Accurate rebate, realtime visibility

### Competitive Advantages

**Speed:**
- Order-to-ship time giảm 50% (từ 2 ngày xuống 1 ngày)
- Payment reconciliation time giảm 90% (từ 1 tuần xuống <1 ngày)
- Realtime processing thay vì batch processing

**Accuracy:**
- Zero manual data entry errors
- Zero duplicate orders
- 99.5%+ inventory accuracy

**Scalability:**
- Handle 1000+ orders/hour (peak load)
- Horizontal scaling capability
- Multi-channel support (Shopify, Rakuten, Amazon)

**Visibility:**
- Realtime dashboard cho tất cả stakeholders
- Full audit trail cho mọi event
- Value chain metrics tracking

**Cost Efficiency:**
- Giảm 80% operational costs (từ 2-3 giờ/ngày xuống <30 phút)
- Zero manual labor cho data entry
- Automated processes reduce human errors

### Value Chain Metrics

**Key Metrics to Track:**
- **Order-to-Cash Time:** Tổng thời gian từ order đến payment confirmation
- **Fulfillment Efficiency:** Tỷ lệ orders được fulfill đúng hạn
- **Payment Reconciliation Time:** Thời gian từ payment đến confirmation
- **Inventory Accuracy:** Độ chính xác của inventory data
- **Error Rate:** Tỷ lệ errors trong toàn bộ value chain

**Target Metrics:**
- Order-to-Cash Time: < 1 ngày (giảm 50%)
- Fulfillment Efficiency: 99%+
- Payment Reconciliation Time: < 1 ngày (giảm 90%)
- Inventory Accuracy: 99.5%+
- Error Rate: < 0.1%

---

## Stakeholder Mapping & Engagement

### Stakeholder Identification

**1. Operations Team (Nhân viên Đơn hàng, Kho, Kế toán)**
- **Interest:** High (trực tiếp sử dụng hệ thống hàng ngày)
- **Influence:** Medium (có thể feedback và request changes)
- **Engagement Strategy:** Training, support, involve trong design process
- **Key Requirements:**
  - UI/UX phải intuitive, không cần training nhiều
  - Dashboard realtime để track orders
  - Manual review interface cho exceptions
  - Support và documentation đầy đủ

**2. Đại lý (BtoB Portal Users)**
- **Interest:** High (self-service portal, track orders)
- **Influence:** Low (end users, không có direct control)
- **Engagement Strategy:** Onboarding, incentives, feedback surveys
- **Key Requirements:**
  - BtoB Portal đơn giản, mobile-friendly
  - Realtime inventory visibility
  - Self-service tracking
  - Clear pricing và order history

**3. FIS (Fulfillment Partner)**
- **Interest:** Medium (dashboard để track orders)
- **Influence:** Low (external partner)
- **Engagement Strategy:** Dashboard riêng, support khi cần
- **Key Requirements:**
  - Dashboard riêng để xem orders và fulfillment status
  - Realtime updates cho fulfillment status

**4. Affiliate (取次店)**
- **Interest:** Medium (track commission)
- **Influence:** Low (external partner)
- **Engagement Strategy:** Dashboard riêng, commission tracking
- **Key Requirements:**
  - Dashboard để track link performance và commission
  - Realtime commission calculation

**5. CREDIE Management**
- **Interest:** High (business owner, ROI)
- **Influence:** High (decision maker, budget approval)
- **Engagement Strategy:** Regular updates, ROI reports, strategic alignment
- **Key Requirements:**
  - ROI metrics và reports
  - Business impact tracking
  - Cost savings visibility
  - Strategic alignment với business goals

**6. MediaBirth (Project Sponsor)**
- **Interest:** High (project success)
- **Influence:** High (intermediary, coordination)
- **Engagement Strategy:** Regular communication, status updates
- **Key Requirements:**
  - Regular status updates
  - Project milestone tracking
  - Risk and issue reporting

**7. Amcolab (Development Team)**
- **Interest:** High (technical implementation)
- **Influence:** High (technical decisions, architecture)
- **Engagement Strategy:** Technical collaboration, architecture reviews
- **Key Requirements:**
  - Technical architecture documentation
  - Integration patterns và best practices
  - Scalability và maintainability requirements
  - Code quality và testing standards

### Stakeholder Analysis Matrix

**High Interest + High Influence (Manage Closely):**
- **CREDIE Management:** Regular updates, ROI reports, strategic alignment
- **MediaBirth:** Regular communication, status updates
- **Amcolab:** Technical collaboration, architecture reviews

**High Interest + Low Influence (Keep Satisfied):**
- **Operations Team:** Training, support, involve trong design
- **Đại lý:** Onboarding, incentives, feedback surveys

**Low Interest + Low Influence (Monitor):**
- **FIS:** Dashboard riêng, support khi cần
- **Affiliate:** Dashboard riêng, commission tracking

### Engagement Strategy

**Phase 1: Discovery (Current)**
- Interview operations team để hiểu pain points
- Review existing processes và systems
- Identify requirements và priorities

**Phase 2: Design & Development**
- Regular updates cho CREDIE Management và MediaBirth
- Technical reviews với Amcolab
- UI/UX feedback từ Operations Team
- Prototype testing với Đại lý

**Phase 3: Rollout**
- Training cho Operations Team
- Onboarding cho Đại lý
- Support và documentation
- Monitoring và feedback collection

**Phase 4: Optimization**
- Continuous feedback từ all stakeholders
- Regular reviews và improvements
- Success metrics tracking

### Stakeholder-Specific Success Criteria

**Operations Team:**
- User satisfaction survey: 90%+ positive feedback
- Adoption rate: 100% (tất cả nhân viên sử dụng hệ thống mới)
- Time savings: 80% reduction (từ 2-3 giờ/ngày xuống <30 phút)

**Đại lý:**
- Portal adoption rate: 80%+ đại lý sử dụng BtoB Portal
- Order volume qua portal: 70%+ orders từ portal
- Satisfaction survey: 85%+ positive feedback

**CREDIE Management:**
- ROI visibility: Monthly reports với metrics
- Cost savings: 80% operational cost reduction
- Business impact: Order-to-ship time giảm 50%

**Amcolab:**
- Technical quality: Code coverage 80%+, architecture documentation đầy đủ
- Integration stability: 99.9%+ success rate
- Scalability: Support 1000+ orders/hour

---

_This PRD captures the essence of Silica-link - tự động hóa quy trình thủ công với tích hợp brownfield seamless._

_Created through collaborative discovery between Potato and AI facilitator._

