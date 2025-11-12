# Silica-link - Epic Breakdown với Estimate Chi tiết

**Author:** amcolab
**Date:** 2025-11-12
**Project Level:** Level 4 (Enterprise Platform)
**Target Scale:** 10-50+ stories

---

## Overview

Tài liệu này phân rã PRD thành các epics và stories có thể implement, kèm theo estimate thời gian chi tiết cho từng hạng mục công việc. Estimate dựa trên Junior Ruby Developer (tương tự estimate-v1.md).

**Tổng quan Epics:**
- Epic 1: Foundation & Infrastructure (Setup, core services)
- Epic 2: EC Order Intake Automation
- Epic 3: Inventory Management & Allocation
- Epic 4: Warehouse Scan UI
- Epic 5: Shipping Integration (Ship&Co)
- Epic 6: Payment Reconciliation (GMO AR)
- Epic 7: B2B Agency Portal
- Epic 8: Procurement Automation
- Epic 9: AR/AP Management
- Epic 10: Agency Rank Management
- Epic 11: Affiliate Commission (GoAffPro)
- Epic 12: Rewards & Dividends
- Epic 13: Dashboard & Reporting

---

## Epic 1: Foundation & Infrastructure

**Goal:** Thiết lập nền tảng cơ bản, core services, và infrastructure cần thiết cho tất cả các epics khác.

### Story 1.1: Project Setup & Configuration

As a **Developer**,
I want **project structure và configuration được setup đầy đủ**,
So that **tôi có thể bắt đầu development ngay**.

**Acceptance Criteria:**
- **Given** project repository trống
- **When** setup project
- **Then** có Rails app structure, config files, dependencies
- **And** có ReactJS app structure cho Scan UI
- **And** có AWS infrastructure templates (CloudFormation/CDK)

**Prerequisites:** None

**Technical Notes:**
- Rails 7.x setup với PostgreSQL, Redis, Sidekiq
- ReactJS 18.x setup với barcode scanning libraries
- AWS CDK/CloudFormation templates
- Environment configuration (dev, staging, prod)
- CI/CD pipeline setup (GitHub Actions)

**Estimated Effort:** 16 hours

---

### Story 1.2: Kintone Integration Service

As a **Developer**,
I want **KintoneService để tương tác với Kintone API**,
So that **tất cả các epics khác có thể sử dụng chung**.

**Acceptance Criteria:**
- **Given** Kintone API credentials
- **When** gọi KintoneService methods
- **Then** có thể create/read/update/delete records
- **And** handle rate limiting (10 req/sec)
- **And** có error handling và retry logic

**Prerequisites:** Story 1.1

**Technical Notes:**
- Kintone REST API client
- Rate limiting wrapper
- Error handling (API errors, network errors)
- Retry mechanism với exponential backoff
- Connection pooling

**Estimated Effort:** 12 hours

---

### Story 1.3: Common Services & Utilities

As a **Developer**,
I want **common services và utilities**,
So that **code được tái sử dụng và nhất quán**.

**Acceptance Criteria:**
- **Given** common patterns
- **When** implement services
- **Then** có IdempotencyService, LoggingService, AlertService
- **And** có utilities cho date/time, formatting, validation

**Prerequisites:** Story 1.1

**Technical Notes:**
- IdempotencyService: Generate và check idempotency keys
- LoggingService: Structured logging (JSON format)
- AlertService: Send alerts (email, Slack, CloudWatch)
- Utility classes: Date/time helpers, formatters, validators

**Estimated Effort:** 8 hours

---

### Story 1.4: AWS Infrastructure Setup

As a **DevOps Engineer**,
I want **AWS infrastructure được setup**,
So that **hệ thống có thể deploy và chạy**.

**Acceptance Criteria:**
- **Given** AWS account
- **When** deploy infrastructure
- **Then** có S3 buckets (labels, pdfs, logs)
- **And** có Lambda functions (batch processing)
- **And** có CloudWatch alarms và dashboards
- **And** có IAM roles và policies

**Prerequisites:** Story 1.1

**Technical Notes:**
- S3 buckets với lifecycle policies
- Lambda functions với scheduled triggers
- CloudWatch alarms cho critical metrics
- IAM roles với least privilege
- Secrets Manager cho API keys

**Estimated Effort:** 12 hours

---

**Epic 1 Total:** 48 hours (6 ngày)

---

## Epic 2: EC Order Intake Automation

**Goal:** Tự động nhập đơn EC từ Yoom (Shopify, Rakuten, Amazon) vào Kintone Orders App.

### Story 2.1: Yoom Webhook Endpoint

As a **System**,
I want **webhook endpoint nhận đơn từ Yoom**,
So that **đơn hàng được nhận realtime**.

**Acceptance Criteria:**
- **Given** Yoom webhook request
- **When** POST /webhooks/yoom/orders
- **Then** validate webhook signature (nếu có)
- **And** enqueue OrderIntakeJob
- **And** return 200 OK immediately

**Prerequisites:** Story 1.2, Story 1.3

**Technical Notes:**
- Rails controller cho webhook endpoint
- Webhook signature validation
- Enqueue Sidekiq job (async processing)
- Idempotency check (prevent duplicates)

**Estimated Effort:** 8 hours

---

### Story 2.2: Yoom Batch Polling (Fallback)

As a **System**,
I want **batch polling Yoom API**,
So that **không bỏ sót đơn nếu webhook fail**.

**Acceptance Criteria:**
- **Given** scheduled job (every 15 minutes)
- **When** poll Yoom API
- **Then** fetch orders since last poll
- **And** enqueue OrderIntakeJob cho mỗi order mới
- **And** skip orders đã xử lý (idempotency)

**Prerequisites:** Story 2.1

**Technical Notes:**
- Sidekiq scheduled job (cron: every 15 min)
- Yoom API client
- Track last poll timestamp
- Idempotency check

**Estimated Effort:** 8 hours

---

### Story 2.3: Order Intake Job (Process Order)

As a **System**,
I want **process order từ Yoom**,
So that **order được tạo trong Kintone Orders App**.

**Acceptance Criteria:**
- **Given** order data từ Yoom
- **When** process order
- **Then** validate order data
- **And** map SKU (Yoom → Kintone Product Master)
- **And** create record trong Kintone Orders App
- **And** handle errors (retry, DLQ)

**Prerequisites:** Story 2.1

**Technical Notes:**
- Sidekiq job: OrderIntakeJob
- Data transformation (Yoom format → Kintone format)
- SKU mapping service
- KintoneService.create_record
- Error handling (retry 3x, DLQ)

**Estimated Effort:** 16 hours

---

### Story 2.4: Idempotency & Duplicate Prevention

As a **System**,
I want **idempotency handling**,
So that **không tạo duplicate orders**.

**Acceptance Criteria:**
- **Given** order với idempotency key
- **When** process order
- **Then** check existing order (idempotency key)
- **And** return existing nếu duplicate
- **And** log duplicate attempts

**Prerequisites:** Story 1.3

**Technical Notes:**
- Idempotency key: `{channel_order_id}_{store_id}`
- Check trong Kintone Orders App
- Return existing record nếu duplicate
- Log duplicate attempts (audit)

**Estimated Effort:** 6 hours

---

**Epic 2 Total:** 38 hours (4.75 ngày)

---

## Epic 3: Inventory Management & Allocation

**Goal:** Quản lý tồn kho logic và tự động phân bổ tồn kho cho đơn hàng.

### Story 3.1: Stock Check Service

As a **System**,
I want **check tồn kho logic**,
So that **biết có đủ hàng để allocate không**.

**Acceptance Criteria:**
- **Given** SKU và quantity
- **When** check stock
- **Then** query Kintone Stock App (Logic Stock)
- **And** return available quantity
- **And** handle race conditions (optimistic locking)

**Prerequisites:** Story 1.2

**Technical Notes:**
- StockCheckService
- Query Kintone Stock App
- Optimistic locking (prevent race conditions)
- Cache stock levels (Redis, 5 min TTL)

**Estimated Effort:** 12 hours

---

### Story 3.2: Stock Allocation Logic

As a **System**,
I want **allocate tồn kho cho đơn hàng**,
So that **đơn hàng được xác nhận và giữ hàng**.

**Acceptance Criteria:**
- **Given** order với items
- **When** allocate stock
- **Then** check stock cho từng item
- **And** update order status (allocated/unallocated)
- **And** update Logic Stock (reserved quantity)
- **And** handle partial allocation

**Prerequisites:** Story 3.1

**Technical Notes:**
- StockAllocationService
- Check stock cho từng item
- Update order status trong Kintone
- Update Logic Stock (reserved quantity)
- Handle partial allocation (một số items đủ, một số thiếu)

**Estimated Effort:** 16 hours

---

### Story 3.3: Race Condition Handling

As a **System**,
I want **handle race conditions**,
So that **không oversell khi có nhiều requests đồng thời**.

**Acceptance Criteria:**
- **Given** concurrent stock check requests
- **When** process requests
- **Then** use optimistic locking
- **And** prevent overselling
- **And** handle conflicts gracefully

**Prerequisites:** Story 3.1

**Technical Notes:**
- Optimistic locking (version field)
- Retry on conflict
- Queue concurrent requests nếu cần
- Log conflicts

**Estimated Effort:** 10 hours

---

**Epic 3 Total:** 38 hours (4.75 ngày)

---

## Epic 4: Warehouse Scan UI

**Goal:** ReactJS UI cho warehouse staff quét barcode và cập nhật tồn kho.

### Story 4.1: ReactJS App Setup & Barcode Scanner

As a **Warehouse Staff**,
I want **quét barcode sản phẩm**,
So that **xuất kho nhanh chóng**.

**Acceptance Criteria:**
- **Given** ReactJS app
- **When** scan barcode
- **Then** detect barcode (JAN/EAN, Shelf code, LOT)
- **And** validate barcode format
- **And** display scan result

**Prerequisites:** Story 1.1

**Technical Notes:**
- ReactJS app setup
- react-barcode-reader library
- Barcode validation (format, checksum)
- UI feedback (success/error)

**Estimated Effort:** 16 hours

---

### Story 4.2: Task List Component

As a **Warehouse Staff**,
I want **xem danh sách tasks hôm nay**,
So that **biết cần làm gì**.

**Acceptance Criteria:**
- **Given** warehouse staff login
- **When** load app
- **Then** fetch today's tasks từ Kintone Task App
- **And** display task list (Pick/Scan/Ship)
- **And** filter by status (pending/completed)

**Prerequisites:** Story 4.1, Story 1.2

**Technical Notes:**
- TaskList component
- API endpoint: GET /api/v1/tasks/today
- Fetch từ Kintone Task App
- Display tasks với status, priority

**Estimated Effort:** 12 hours

---

### Story 4.3: Scan & Update Stock

As a **Warehouse Staff**,
I want **scan và cập nhật tồn kho**,
So that **Physical Stock được cập nhật realtime**.

**Acceptance Criteria:**
- **Given** task và scan result
- **When** submit scan
- **Then** validate scan data
- **And** update Physical Stock DB
- **And** sync Physical → Logic Stock (Kintone)
- **And** update task status

**Prerequisites:** Story 4.1, Story 4.2

**Technical Notes:**
- Scan submission API: POST /api/v1/scans
- Update Physical Stock DB
- Sync Physical → Logic Stock (KintoneService)
- Update Kintone Task App status
- Error handling (retry, rollback)

**Estimated Effort:** 20 hours

---

### Story 4.4: Stock Sync Service (Physical → Logic)

As a **System**,
I want **sync Physical Stock → Logic Stock**,
So that **tồn kho được đồng bộ**.

**Acceptance Criteria:**
- **Given** Physical Stock update
- **When** sync to Logic Stock
- **Then** update Kintone Stock App
- **And** sync within 5 seconds
- **And** handle sync failures (retry, DLQ)

**Prerequisites:** Story 1.2, Story 4.3

**Technical Notes:**
- StockSyncService
- Physical Stock DB → Kintone Stock App
- Realtime sync (on scan)
- Nightly batch validation
- Error handling (retry, DLQ)

**Estimated Effort:** 12 hours

---

**Epic 4 Total:** 60 hours (7.5 ngày)

---

## Epic 5: Shipping Integration (Ship&Co)

**Goal:** Tích hợp Ship&Co API để tự động phát hành vận đơn và tracking.

### Story 5.1: Ship&Co API Client

As a **Developer**,
I want **Ship&Co API client**,
So that **có thể gọi Ship&Co API**.

**Acceptance Criteria:**
- **Given** Ship&Co API credentials
- **When** call Ship&Co API
- **Then** authenticate (API key)
- **And** handle rate limiting
- **And** handle errors (retry, DLQ)

**Prerequisites:** Story 1.2, Story 1.3

**Technical Notes:**
- ShipCoService
- REST API client
- Rate limiting (queue jobs nếu cần)
- Retry mechanism (3x, exponential backoff)
- Error handling

**Estimated Effort:** 12 hours

---

### Story 5.2: Create Shipping Label Job

As a **System**,
I want **tạo shipping label tự động**,
So that **vận đơn được phát hành khi task hoàn tất**.

**Acceptance Criteria:**
- **Given** completed task
- **When** create shipping label
- **Then** call Ship&Co API
- **And** save label PDF to S3
- **And** update order với tracking number
- **And** handle errors (retry, DLQ)

**Prerequisites:** Story 5.1, Story 4.3

**Technical Notes:**
- ShippingJob (Sidekiq)
- Trigger: Kintone webhook (task completed)
- Ship&Co API: Create label
- Save PDF to S3
- Update Kintone Orders App (tracking_no, label_url)
- Error handling (retry 3x, DLQ)

**Estimated Effort:** 20 hours

---

### Story 5.3: Tracking Notification

As a **Customer**,
I want **nhận thông báo tracking**,
So that **biết đơn hàng đang ở đâu**.

**Acceptance Criteria:**
- **Given** shipping label created
- **When** Ship&Co sends tracking update
- **Then** receive webhook từ Ship&Co
- **And** update order status trong Kintone
- **And** sync tracking về Shopify (nếu cần)

**Prerequisites:** Story 5.2

**Technical Notes:**
- Ship&Co webhook endpoint
- Update Kintone Orders App (tracking status)
- Sync to Shopify (nếu cần)
- Email/SMS notification (via Ship&Co)

**Estimated Effort:** 8 hours

---

**Epic 5 Total:** 40 hours (5 ngày)

---

## Epic 6: Payment Reconciliation (GMO AR)

**Goal:** Tự động đối soát thanh toán từ GMO Bank API và cập nhật PayStatus.

### Story 6.1: GMO API Client

As a **Developer**,
I want **GMO API client**,
So that **có thể gọi GMO Bank API**.

**Acceptance Criteria:**
- **Given** GMO API credentials
- **When** call GMO API
- **Then** authenticate (signature HMAC, IP whitelist)
- **And** handle rate limiting
- **And** handle errors (retry, DLQ)

**Prerequisites:** Story 1.2, Story 1.3

**Technical Notes:**
- GmoService
- REST API client với signature authentication
- IP whitelist configuration
- Rate limiting (queue jobs nếu cần)
- Retry mechanism (3x, exponential backoff)
- Secure credential storage (AWS Secrets Manager)

**Estimated Effort:** 16 hours

---

### Story 6.2: Fetch Transactions (AR)

As a **System**,
I want **fetch transactions từ GMO**,
So that **đối soát thanh toán tự động**.

**Acceptance Criteria:**
- **Given** scheduled job (daily)
- **When** fetch transactions
- **Then** call GMO API (/transactions)
- **And** process transactions
- **And** enqueue ReconciliationJob

**Prerequisites:** Story 6.1

**Technical Notes:**
- Scheduled job (daily, hoặc webhook nếu GMO support)
- GMO API: GET /transactions
- Process transactions (parse, validate)
- Enqueue ReconciliationJob cho mỗi transaction

**Estimated Effort:** 12 hours

---

### Story 6.3: Reconciliation Logic

As a **System**,
I want **đối soát transactions với Virtual Accounts**,
So that **PayStatus được cập nhật tự động**.

**Acceptance Criteria:**
- **Given** transaction từ GMO
- **When** reconcile
- **Then** match với Virtual Account (VA ID, amount, date)
- **And** update PayStatus trong Kintone Invoices App
- **And** handle mismatches (exceptions)

**Prerequisites:** Story 6.2

**Technical Notes:**
- ReconciliationService
- Match transaction với VA (exact match: VA ID, amount, date within 24h)
- Update Kintone Invoices App (PayStatus = PAID)
- Handle mismatches (name, amount, date) → Exceptions App
- Log reconciliation results

**Estimated Effort:** 20 hours

---

### Story 6.4: Exception Handling (Mismatches)

As a **Finance Team**,
I want **xử lý exceptions thủ công**,
So that **các giao dịch không match được xử lý**.

**Acceptance Criteria:**
- **Given** transaction không match
- **When** create exception
- **Then** create record trong Kintone Exceptions App
- **And** alert Finance team
- **And** support manual review và approval

**Prerequisites:** Story 6.3

**Technical Notes:**
- Exceptions App trong Kintone
- Create exception record (transaction details, mismatch reason)
- Alert Finance team (email, Slack)
- Manual review UI (Kintone form)
- Approval workflow (approve/reject)

**Estimated Effort:** 12 hours

---

**Epic 6 Total:** 60 hours (7.5 ngày)

---

## Tổng kết Estimate (Epic 1-6: MVP Phase)

| Epic | Stories | Total Hours | Days (8h/day) |
| ---- | ------- | ----------- | ------------- |
| Epic 1: Foundation | 4 | 48 | 6 |
| Epic 2: EC Order Intake | 4 | 38 | 4.75 |
| Epic 3: Inventory | 3 | 38 | 4.75 |
| Epic 4: Warehouse Scan UI | 4 | 60 | 7.5 |
| Epic 5: Shipping | 3 | 40 | 5 |
| Epic 6: Payment AR | 4 | 60 | 7.5 |
| **MVP Total** | **22** | **284** | **35.5** |

**MVP Timeline:** ~5-6 tuần (1 developer) hoặc ~3 tuần (2 developers)

---

## Epic 7-13: Growth Features (Estimate Summary)

Do thời gian, các epics còn lại được estimate tổng quan:

| Epic | Description | Estimated Hours | Days |
| ---- | ----------- | --------------- | ---- |
| **Epic 7: B2B Portal** | Kintone Guest Space, Agency portal, order management | 80 | 10 |
| **Epic 8: Procurement** | Low stock alert, auto PO, supplier portal | 72 | 9 |
| **Epic 9: AR/AP Management** | Invoice App, Payment Request, GMO batch payment | 64 | 8 |
| **Epic 10: Agency Rank** | Rank calculation, discount application | 40 | 5 |
| **Epic 11: Affiliate** | GoAffPro integration, link generation, commission | 96 | 12 |
| **Epic 12: Rewards & Dividends** | RewardsLedger, quarterly calculation, payout | 88 | 11 |
| **Epic 13: Dashboard** | Realtime dashboard, reporting, analytics | 56 | 7 |
| **Growth Total** | | **496** | **62** |

**Growth Timeline:** ~8-10 tuần (1 developer) hoặc ~4-5 tuần (2 developers)

---

## Tổng kết Estimate Toàn bộ Dự án

| Phase | Epics | Stories | Hours | Days | Weeks (1 dev) | Weeks (2 devs) |
| ----- | ----- | ------- | ----- | ---- | ------------ | -------------- |
| **MVP (Epic 1-6)** | 6 | 22 | 284 | 35.5 | 5-6 | 3 |
| **Growth (Epic 7-13)** | 7 | ~35 | 496 | 62 | 8-10 | 4-5 |
| **TOTAL** | **13** | **~57** | **780** | **97.5** | **13-16** | **7-8** |

**Lưu ý:**
- Estimate dựa trên Junior Ruby Developer
- Không bao gồm: Testing (QA), Documentation, Training, Deployment
- Có thể giảm 20-30% với Senior Developer + AI assistance
- Buffer 20% cho unexpected issues

---

_For implementation: Use the `create-story` workflow to generate individual story implementation plans from this epic breakdown._

