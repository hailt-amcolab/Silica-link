# Silica-link - Epic Breakdown với Estimate Chi tiết

**Author:** amcolab
**Date:** 2025-11-12
**Project Level:** Level 4 (Enterprise Platform)
**Target Scale:** 10-50+ stories

---

## Overview

Tài liệu này phân rã PRD thành các epics và stories có thể implement, kèm theo estimate thời gian chi tiết cho từng hạng mục công việc. Estimate dựa trên Entry-level / Beginner Ruby Developer với technology stack mới (cần nhiều thời gian research và learning).

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

**Goal:** Thiết lập project structure và configuration cơ bản để bắt đầu development.

**Note:** Các core services (Kintone, Common Services, AWS Infrastructure) sẽ được implement trong các epics sau khi cần thiết, không làm trong Epic 1.

### Story 1.1: Project Setup & Configuration

As a **Developer**,
I want **project structure và configuration được setup đầy đủ**,
So that **tôi có thể bắt đầu development ngay**.

**Acceptance Criteria:**
- **Given** project repository trống
- **When** setup project
- **Then** có Rails app structure, config files, dependencies
- **And** có ReactJS app structure cho Scan UI
- **And** có AWS infrastructure templates (CloudFormation/CDK) - structure only
- **And** có environment configuration (dev, staging, prod)
- **And** có CI/CD pipeline setup (GitHub Actions)

**Prerequisites:** None

**Technical Notes:**
- Rails 7.x setup với PostgreSQL, Redis, Sidekiq
- ReactJS 18.x setup với barcode scanning libraries
- AWS CDK/CloudFormation templates (structure, chưa deploy)
- Environment configuration files (.env.example)
- CI/CD pipeline setup (GitHub Actions - basic structure)
- Git repository setup, .gitignore, README

**Estimated Effort:** 16 hours (base)
**Adjusted:** Setup task - có ReactJS + AWS (mới) → +40%
- Research: 15% = 2.4h
- Buffer: 25% = 4h
- **Total: 16 + 2.4 + 4 = ~22 hours**

**Estimated Effort:** 22 hours (2.75 ngày)

---

**Epic 1 Total:** 22 hours (2.75 ngày)

**Note về Core Services (sẽ implement trong các epics sau):**

- **KintoneService (Story 1.2):** Sẽ implement trong Epic 2 khi cần tạo orders trong Kintone
- **Common Services (Story 1.3):** 
  - IdempotencyService: Sẽ implement trong Epic 2 (Story 2.4)
  - LoggingService: Sẽ implement trong Epic 2 (basic), mở rộng khi cần
  - AlertService: Sẽ implement khi cần (Epic 5, 6...)
- **AWS Infrastructure (Story 1.4):**
  - S3 buckets: Sẽ implement trong Epic 5 khi cần lưu shipping labels
  - Lambda functions: Sẽ implement trong Epic 6, 10, 11, 12 khi cần batch processing
  - CloudWatch: Sẽ implement dần khi cần monitoring
  - Secrets Manager: Sẽ implement trong Epic 2 khi cần store API keys

---

## Epic 2: EC Order Intake Automation

**Goal:** Tự động nhập đơn EC từ Yoom (Shopify, Rakuten, Amazon) vào Kintone Orders App.

### Story 2.1: KintoneService - Basic Implementation

As a **Developer**,
I want **KintoneService cơ bản để tương tác với Kintone API**,
So that **có thể tạo orders trong Kintone**.

**Acceptance Criteria:**
- **Given** Kintone API credentials
- **When** gọi KintoneService.create_record
- **Then** có thể tạo record trong Kintone Orders App
- **And** có basic error handling
- **And** có simple retry mechanism (3 attempts)

**Prerequisites:** Story 1.1

**Technical Notes:**
- Kintone REST API client cơ bản
- Implement create_record method (cho Orders App)
- Basic error handling (API errors, network errors)
- Simple retry mechanism (3 attempts, fixed delay)
- Rate limiting: Basic check (sẽ cải thiện sau nếu cần)

**Estimated Effort:** 8 hours (base)
**Adjusted:** Medium task - Kintone API (mới) → +35%
- Research: 20% = 1.6h
- Buffer: 15% = 1.2h
- **Total: 8 + 1.6 + 1.2 = ~11 hours**

**Estimated Effort:** 11 hours (1.4 ngày)

---

### Story 2.2: IdempotencyService - Basic Implementation

As a **Developer**,
I want **IdempotencyService để prevent duplicate orders**,
So that **không tạo duplicate orders khi webhook retry**.

**Acceptance Criteria:**
- **Given** idempotency key
- **When** check idempotency
- **Then** có thể generate idempotency key
- **And** có thể check existing record (Redis hoặc DB)
- **And** return existing nếu duplicate

**Prerequisites:** Story 1.1

**Technical Notes:**
- IdempotencyService class
- Generate key: `{channel_order_id}_{store_id}`
- Store keys in Redis (hoặc PostgreSQL nếu chưa có Redis)
- Check existing records
- TTL cho keys (24 hours)

**Estimated Effort:** 4 hours (base)
**Adjusted:** Simple task - Redis (mới) → +25%
- Research: 10% = 0.4h
- Buffer: 15% = 0.6h
- **Total: 4 + 0.4 + 0.6 = ~5 hours**

**Estimated Effort:** 5 hours (0.6 ngày)

---

### Story 2.3: Yoom Webhook Endpoint

As a **System**,
I want **webhook endpoint nhận đơn từ Yoom**,
So that **đơn hàng được nhận realtime**.

**Acceptance Criteria:**
- **Given** Yoom webhook request
- **When** POST /webhooks/yoom/orders
- **Then** validate webhook signature (nếu có)
- **And** enqueue OrderIntakeJob
- **And** return 200 OK immediately

**Prerequisites:** Story 2.1, Story 2.2

**Technical Notes:**
- Rails controller cho webhook endpoint
- Webhook signature validation
- Enqueue Sidekiq job (async processing)
- Idempotency check (prevent duplicates)

**Estimated Effort:** 8 hours (base)
**Adjusted:** Medium task - Yoom API integration (khó) → +45%
- Research: 25% = 2h
- Buffer: 20% = 1.6h
- **Total: 8 + 2 + 1.6 = ~12 hours**

**Estimated Effort:** 12 hours (1.5 ngày)

---

### Story 2.4: Yoom Batch Polling (Fallback)

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

**Estimated Effort:** 8 hours (base)
**Adjusted:** Medium task - Yoom API integration (khó) → +45%
- Research: 25% = 2h
- Buffer: 20% = 1.6h
- **Total: 8 + 2 + 1.6 = ~12 hours**

**Estimated Effort:** 12 hours (1.5 ngày)

---

### Story 2.5: Order Intake Job (Process Order)

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

**Estimated Effort:** 16 hours (base)
**Adjusted:** Large task - Yoom + Kintone integration (khó) → +55%
- Research: 30% = 4.8h
- Buffer: 25% = 4h
- **Total: 16 + 4.8 + 4 = ~25 hours**

**Estimated Effort:** 25 hours (3.1 ngày)

---

**Epic 2 Total:** 65 hours (8.1 ngày)

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

**Estimated Effort:** 12 hours (base)
**Adjusted:** Medium task - Kintone + Redis (mới) → +35%
- Research: 20% = 2.4h
- Buffer: 15% = 1.8h
- **Total: 12 + 2.4 + 1.8 = ~16 hours**

**Estimated Effort:** 16 hours (2 ngày)

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

**Estimated Effort:** 16 hours (base)
**Adjusted:** Large task - Complex logic (khó) → +45%
- Research: 25% = 4h
- Buffer: 20% = 3.2h
- **Total: 16 + 4 + 3.2 = ~23 hours**

**Estimated Effort:** 23 hours (2.9 ngày)

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

**Estimated Effort:** 10 hours (base)
**Adjusted:** Medium task - Complex logic (khó) → +35%
- Research: 20% = 2h
- Buffer: 15% = 1.5h
- **Total: 10 + 2 + 1.5 = ~14 hours**

**Estimated Effort:** 14 hours (1.75 ngày)

---

**Epic 3 Total:** 53 hours (6.6 ngày)

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

**Estimated Effort:** 16 hours (base)
**Adjusted:** Large task - ReactJS (mới) + Barcode → +65%
- Research: 35% = 5.6h (ReactJS learning curve)
- Buffer: 30% = 4.8h
- **Total: 16 + 5.6 + 4.8 = ~26 hours**

**Estimated Effort:** 26 hours (3.25 ngày)

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

**Prerequisites:** Story 4.1, Story 2.1 (KintoneService)

**Technical Notes:**
- TaskList component
- API endpoint: GET /api/v1/tasks/today
- Fetch từ Kintone Task App
- Display tasks với status, priority

**Estimated Effort:** 12 hours (base)
**Adjusted:** Medium task - ReactJS (mới) + Kintone → +60%
- Research: 30% = 3.6h (ReactJS + Kintone)
- Buffer: 30% = 3.6h
- **Total: 12 + 3.6 + 3.6 = ~19 hours**

**Estimated Effort:** 19 hours (2.4 ngày)

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

**Estimated Effort:** 20 hours (base)
**Adjusted:** Large task - ReactJS (mới) + Rails API + Kintone → +65%
- Research: 35% = 7h (ReactJS + integration)
- Buffer: 30% = 6h
- **Total: 20 + 7 + 6 = ~33 hours**

**Estimated Effort:** 33 hours (4.1 ngày)

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

**Prerequisites:** Story 2.1 (KintoneService), Story 4.3

**Technical Notes:**
- StockSyncService
- Physical Stock DB → Kintone Stock App
- Realtime sync (on scan)
- Nightly batch validation
- Error handling (retry, DLQ)

**Estimated Effort:** 12 hours (base)
**Adjusted:** Medium task - Kintone + Sync logic (khó) → +45%
- Research: 25% = 3h
- Buffer: 20% = 2.4h
- **Total: 12 + 3 + 2.4 = ~17 hours**

**Estimated Effort:** 17 hours (2.1 ngày)

---

**Epic 4 Total:** 95 hours (11.9 ngày)

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

**Estimated Effort:** 12 hours (base)
**Adjusted:** Medium task - Ship&Co API (khó) → +45%
- Research: 25% = 3h
- Buffer: 20% = 2.4h
- **Total: 12 + 3 + 2.4 = ~17 hours**

**Estimated Effort:** 17 hours (2.1 ngày)

---

### Story 5.2: S3 Bucket Setup (Labels)

As a **DevOps Engineer**,
I want **S3 bucket để lưu shipping labels**,
So that **labels được lưu trữ và có thể truy cập**.

**Acceptance Criteria:**
- **Given** AWS account
- **When** deploy S3 bucket
- **Then** có bucket `silica-link-labels`
- **And** có IAM role cho Rails app access S3
- **And** có presigned URL generation

**Prerequisites:** Story 1.1

**Technical Notes:**
- Create S3 bucket: `silica-link-labels`
- IAM role và policy cho Rails app
- AWS SDK S3 integration
- Presigned URL generation (cho Kintone links)

**Estimated Effort:** 4 hours (base)
**Adjusted:** Simple task - AWS S3 (mới) → +30%
- Research: 15% = 0.6h
- Buffer: 15% = 0.6h
- **Total: 4 + 0.6 + 0.6 = ~5 hours**

**Estimated Effort:** 5 hours (0.6 ngày)

---

### Story 5.3: Create Shipping Label Job

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

**Prerequisites:** Story 5.1, Story 5.2, Story 4.3

**Technical Notes:**
- ShippingJob (Sidekiq)
- Trigger: Kintone webhook (task completed)
- Ship&Co API: Create label
- Save PDF to S3 (Story 5.2)
- Update Kintone Orders App (tracking_no, label_url)
- Error handling (retry 3x, DLQ)

**Estimated Effort:** 20 hours (base)
**Adjusted:** Large task - Ship&Co + S3 + Kintone (khó) → +55%
- Research: 30% = 6h
- Buffer: 25% = 5h
- **Total: 20 + 6 + 5 = ~31 hours**

**Estimated Effort:** 31 hours (3.9 ngày)

---

### Story 5.4: Tracking Notification

As a **Customer**,
I want **nhận thông báo tracking**,
So that **biết đơn hàng đang ở đâu**.

**Acceptance Criteria:**
- **Given** shipping label created
- **When** Ship&Co sends tracking update
- **Then** receive webhook từ Ship&Co
- **And** update order status trong Kintone
- **And** sync tracking về Shopify (nếu cần)

**Prerequisites:** Story 5.3

**Technical Notes:**
- Ship&Co webhook endpoint
- Update Kintone Orders App (tracking status)
- Sync to Shopify (nếu cần)
- Email/SMS notification (via Ship&Co)

**Estimated Effort:** 8 hours (base)
**Adjusted:** Medium task - Ship&Co webhook (khó) → +35%
- Research: 20% = 1.6h
- Buffer: 15% = 1.2h
- **Total: 8 + 1.6 + 1.2 = ~11 hours**

**Estimated Effort:** 11 hours (1.4 ngày)

---

**Epic 5 Total:** 64 hours (8 ngày)

---

## Epic 6: Payment Reconciliation (GMO AR)

**Goal:** Tự động đối soát thanh toán từ GMO Bank API và cập nhật PayStatus.

### Story 6.1: AWS Secrets Manager Setup

As a **DevOps Engineer**,
I want **Secrets Manager để store API keys securely**,
So that **GMO API credentials được bảo mật**.

**Acceptance Criteria:**
- **Given** AWS account
- **When** setup Secrets Manager
- **Then** có secret cho GMO API credentials
- **And** có IAM role cho Rails app access Secrets Manager
- **And** có code để retrieve secrets

**Prerequisites:** Story 1.1

**Technical Notes:**
- Create Secrets Manager secret: `gmo-api-credentials`
- IAM role và policy cho Rails app
- AWS SDK Secrets Manager integration
- Code để retrieve secrets (helper method)

**Estimated Effort:** 4 hours (base)
**Adjusted:** Simple task - AWS Secrets Manager (mới) → +30%
- Research: 15% = 0.6h
- Buffer: 15% = 0.6h
- **Total: 4 + 0.6 + 0.6 = ~5 hours**

**Estimated Effort:** 5 hours (0.6 ngày)

---

### Story 6.2: GMO API Client

As a **Developer**,
I want **GMO API client**,
So that **có thể gọi GMO Bank API**.

**Acceptance Criteria:**
- **Given** GMO API credentials (từ Secrets Manager)
- **When** call GMO API
- **Then** authenticate (signature HMAC, IP whitelist)
- **And** handle rate limiting
- **And** handle errors (retry, DLQ)

**Prerequisites:** Story 6.1

**Technical Notes:**
- GmoService
- REST API client với signature authentication
- IP whitelist configuration
- Rate limiting (queue jobs nếu cần)
- Retry mechanism (3x, exponential backoff)
- Retrieve credentials từ Secrets Manager (Story 6.1)

**Estimated Effort:** 16 hours (base)
**Adjusted:** Large task - GMO API (rất khó, financial) → +65%
- Research: 35% = 5.6h (security research cao)
- Buffer: 30% = 4.8h (high-risk financial)
- **Total: 16 + 5.6 + 4.8 = ~26 hours**

**Estimated Effort:** 26 hours (3.25 ngày)

---

### Story 6.3: Fetch Transactions (AR)

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

**Estimated Effort:** 12 hours (base)
**Adjusted:** Medium task - GMO API (khó) → +45%
- Research: 25% = 3h
- Buffer: 20% = 2.4h
- **Total: 12 + 3 + 2.4 = ~17 hours**

**Estimated Effort:** 17 hours (2.1 ngày)

---

### Story 6.4: Reconciliation Logic

As a **System**,
I want **đối soát transactions với Virtual Accounts**,
So that **PayStatus được cập nhật tự động**.

**Acceptance Criteria:**
- **Given** transaction từ GMO
- **When** reconcile
- **Then** match với Virtual Account (VA ID, amount, date)
- **And** update PayStatus trong Kintone Invoices App
- **And** handle mismatches (exceptions)

**Prerequisites:** Story 6.3

**Technical Notes:**
- ReconciliationService
- Match transaction với VA (exact match: VA ID, amount, date within 24h)
- Update Kintone Invoices App (PayStatus = PAID)
- Handle mismatches (name, amount, date) → Exceptions App
- Log reconciliation results

**Estimated Effort:** 20 hours (base)
**Adjusted:** Large task - GMO + Financial logic (rất khó) → +60%
- Research: 30% = 6h (financial logic phức tạp)
- Buffer: 30% = 6h (high-risk financial)
- **Total: 20 + 6 + 6 = ~32 hours**

**Estimated Effort:** 32 hours (4 ngày)

---

### Story 6.5: Exception Handling (Mismatches)

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

**Estimated Effort:** 12 hours (base)
**Adjusted:** Medium task - Kintone Exceptions App (cần dựng view) → +45%
- Research: 25% = 3h (Kintone view setup)
- Buffer: 20% = 2.4h
- **Total: 12 + 3 + 2.4 = ~17 hours**

**Estimated Effort:** 17 hours (2.1 ngày)

---

**Epic 6 Total:** 97 hours (12.1 ngày)

---

## Tổng kết Estimate (Epic 1-6: MVP Phase)

| Epic | Stories | Base Hours | Adjusted Hours | Days (8h/day) |
| ---- | ------- | ---------- | -------------- | ------------- |
| Epic 1: Foundation | 1 | 16 | 22 | 2.75 |
| Epic 2: EC Order Intake | 5 | 44 | 65 | 8.1 |
| Epic 3: Inventory | 3 | 38 | 53 | 6.6 |
| Epic 4: Warehouse Scan UI | 4 | 60 | 95 | 11.9 |
| Epic 5: Shipping | 4 | 44 | 64 | 8 |
| Epic 6: Payment AR | 5 | 64 | 97 | 12.1 |
| **MVP Total** | **24** | **286** | **396** | **49.5** |

**MVP Timeline:** ~10-12 tuần (1 developer) hoặc ~5-6 tuần (2 developers)

**Note về Estimate:**
- Base estimate: 286 giờ (Junior developer baseline)
- Adjusted estimate: 396 giờ (Junior-level developer, chỉ tăng cho tasks khó/UI)
- Tăng ~38% so với baseline, chỉ áp dụng cho:
  - Tasks trung bình/khó (medium/large stories) → +35-55%
  - Tasks có ReactJS (technology mới) → +60-65%
  - Tasks có Kintone view/UI → +45%
  - Tasks có integration phức tạp (GMO, Ship&Co, Yoom) → +45-65%
  - Tasks đơn giản: Tăng nhẹ (+25-30%)

**Note:** Epic 1 chỉ làm Project Setup. Các core services (Kintone, Common Services, AWS Infrastructure) được implement trong các epics sau khi cần thiết.

---

## Epic 7-13: Growth Features (Estimate Summary)

Do thời gian, các epics còn lại được estimate tổng quan:

| Epic | Description | Base Hours | Adjusted Hours | Days |
| ---- | ----------- | ---------- | -------------- | ---- |
| **Epic 7: B2B Portal** | Kintone Guest Space, Agency portal, order management | 80 | 96 | 12 |
| **Epic 8: Procurement** | Low stock alert, auto PO, supplier portal | 72 | 82 | 10.25 |
| **Epic 9: AR/AP Management** | Invoice App, Payment Request, GMO batch payment | 64 | 84 | 10.5 |
| **Epic 10: Agency Rank** | Rank calculation, discount application | 40 | 46 | 5.75 |
| **Epic 11: Affiliate** | GoAffPro integration, link generation, commission | 96 | 115 | 14.4 |
| **Epic 12: Rewards & Dividends** | RewardsLedger, quarterly calculation, payout | 88 | 104 | 13 |
| **Epic 13: Dashboard** | Realtime dashboard, reporting, analytics | 56 | 65 | 8.1 |
| **Growth Total** | | **496** | **592** | **74** |

**Growth Timeline:** ~15-18 tuần (1 developer) hoặc ~7-9 tuần (2 developers)

**Note:** Adjusted estimate tăng ~19% so với base, chỉ tăng cho tasks khó/UI

---

## Tổng kết Estimate Toàn bộ Dự án

| Phase | Epics | Stories | Base Hours | Adjusted Hours | Days | Weeks (1 dev) | Weeks (2 devs) |
| ----- | ----- | ------- | ---------- | -------------- | ---- | ------------ | -------------- |
| **MVP (Epic 1-6)** | 6 | 24 | 286 | 396 | 49.5 | 10-12 | 5-6 |
| **Growth (Epic 7-13)** | 7 | ~35 | 496 | 592 | 74 | 15-18 | 7-9 |
| **TOTAL** | **13** | **~59** | **782** | **988** | **123.5** | **25-30** | **12-15** |

**Lưu ý:**
- Estimate dựa trên **Junior Ruby Developer** (tăng từ Entry-level)
- **Chỉ tăng thời gian cho:**
  - Tasks trung bình/khó (medium/large stories): +35-55%
  - Tasks có ReactJS (technology mới): +60-65%
  - Tasks có Kintone view/UI: +45%
  - Tasks có integration phức tạp (GMO, Ship&Co, Yoom): +45-65%
  - Tasks đơn giản: Tăng nhẹ (+25-30%)
- Technology stack: Ruby on Rails (đã quen), ReactJS (mới), AWS (mới), Third-party APIs (mới)
- Không bao gồm: Testing (QA), Documentation, Training, Deployment
- Có thể giảm 15-25% với Mid-level Developer + AI assistance
- Có thể giảm 30-40% với Senior Developer + AI assistance
- Buffer: 15-30% tùy complexity (simple: 15%, medium: 15-20%, large: 25-30%)

**Chi tiết tiêu chí đánh giá:** Xem [estimation-criteria.md](./estimation-criteria.md)

---

_For implementation: Use the `create-story` workflow to generate individual story implementation plans from this epic breakdown._

