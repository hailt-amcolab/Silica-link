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
- Epic 14: Sample Outflow Management (BF-09)
- Epic 15: Production & Materials Management
- Epic 16: Return/Move/Adjustment Management (System Flow 6)
- Epic 17: System Administration

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

**Tasks chi tiết:**
- Setup Rails app structure (models, controllers, config): 3h
- Setup ReactJS app structure (components, routing, build config): 4h
- Setup AWS CDK/CloudFormation templates (structure only): 2h
- Environment configuration (.env.example, config files): 1h
- CI/CD pipeline setup (GitHub Actions basic structure): 2h
- Git repository setup (.gitignore, README, initial commit): 1h
- Testing và validation: 3h

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

**Tasks chi tiết:**
- Research Kintone REST API documentation: 1.5h
- Implement KintoneService class với create_record method: 3h
- Implement basic error handling: 1h
- Implement simple retry mechanism (3 attempts): 1h
- Testing và validation: 1.5h

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

**Tasks chi tiết:**
- Research Redis hoặc PostgreSQL cho idempotency: 0.5h
- Implement IdempotencyService class: 1.5h
- Implement key generation logic: 0.5h
- Implement check existing records: 1h
- Implement TTL cho keys: 0.5h

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

**Tasks chi tiết:**
- Research Yoom webhook API documentation: 2h
- Implement Rails controller cho webhook endpoint: 2h
- Implement webhook signature validation: 1.5h
- Implement enqueue Sidekiq job: 1h
- Implement idempotency check: 1h
- Testing và validation: 0.5h

**Estimated Effort:** 8 hours (base)
**Adjusted:** Medium task - Yoom API integration (khó) → +45%
- Research: 25% = 2h
- Buffer: 20% = 1.6h
- **Total: 8 + 2 + 1.6 = ~12 hours**

**Estimated Effort:** 12 hours (1.5 ngày)

---

### Story 2.4: Order Intake Job (Process Order)

As a **System**,
I want **process order từ Yoom**,
So that **order được tạo trong Kintone Orders App**.

**Acceptance Criteria:**
- **Given** order data từ Yoom
- **When** process order
- **Then** validate order data
- **And** validate SKU exists trong Kintone Product Master (SKU được khách hàng cung cấp, sử dụng chung trong toàn bộ dự án)
- **And** create record trong Kintone Orders App
- **And** handle errors (retry, DLQ)

**Prerequisites:** Story 2.1

**Technical Notes:**
- Sidekiq job: OrderIntakeJob
- Data transformation (Yoom format → Kintone format)
- SKU validation: Check SKU exists trong Kintone Product Master (SKU được khách hàng cung cấp, sử dụng chung trong toàn bộ dự án)
- KintoneService.create_record
- Error handling (retry 3x, DLQ)

**Tasks chi tiết:**
- Research Yoom order format và Kintone Orders App structure: 3h
- Implement OrderIntakeJob (Sidekiq): 2h
- Implement data transformation (Yoom → Kintone): 4h
- Implement SKU validation (check exists trong Product Master): 1h
- Integrate với KintoneService.create_record: 2h
- Implement error handling (retry, DLQ): 2h

**Estimated Effort:** 14 hours (base)
**Adjusted:** Large task - Yoom + Kintone integration (khó) → +55%
- Research: 30% = 4.2h
- Buffer: 25% = 3.5h
- **Total: 14 + 4.2 + 3.5 = ~22 hours**

**Estimated Effort:** 22 hours (2.75 ngày)

---

### Story 2.5: Materials App Setup

As a **System Administrator**,
I want **Materials App được setup**,
So that **quản lý master vật tư/nguyên liệu/bao bì**.

**Acceptance Criteria:**
- **Given** Kintone account
- **When** setup Materials App
- **Then** có Materials App với fields: material_id, name, type (原料/OEM), unit, supplier_id, reorder_point
- **And** link với Suppliers App

**Prerequisites:** Story 1.1

**Technical Notes:**
- Materials App trong Kintone
- Material types: 原料 (raw material), OEM, 包装 (packaging)
- Link với Suppliers App
- Support reorder_point

**Tasks chi tiết:**
- Research Materials App setup: 2h
- Setup Materials App trong Kintone: 2h
- Implement material types: 1.5h
- Implement link với Suppliers: 1h
- Testing: 1h

**Estimated Effort:** 7.5 hours (base)
**Adjusted:** Simple task - Kintone App setup → +30%
- Research: 15% = 1.1h
- Buffer: 15% = 1.1h
- **Total: 7.5 + 1.1 + 1.1 = ~10 hours**

**Estimated Effort:** 10 hours (1.25 ngày)

---

### Story 2.6: BOM App Setup

As a **System Administrator**,
I want **BOM App được setup**,
So that **quản lý cấu thành sản phẩm (Materials + định mức)**.

**Acceptance Criteria:**
- **Given** Materials App và Products App
- **When** setup BOM App
- **Then** có BOM App với fields: product_id, material_id, quantity, unit, waste_rate
- **And** link với Products App và Materials App
- **And** validate BOM integrity

**Prerequisites:** Story 2.5, Story 2.1 (Products App)

**Technical Notes:**
- BOM App trong Kintone
- Link với Products App và Materials App
- BOM validation (material must exist, active)
- Support waste_rate (định mức tiêu hao)

**Tasks chi tiết:**
- Research BOM App setup: 2h
- Setup BOM App trong Kintone: 2h
- Implement link với Products và Materials: 2h
- Implement BOM validation: 2h
- Testing: 1.5h

**Estimated Effort:** 9.5 hours (base)
**Adjusted:** Medium task - BOM logic + validation → +35%
- Research: 20% = 1.9h
- Buffer: 15% = 1.4h
- **Total: 9.5 + 1.9 + 1.4 = ~13 hours**

**Estimated Effort:** 13 hours (1.6 ngày)

---

**Epic 2 Total:** 73 hours (9.1 ngày)

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

**Tasks chi tiết:**
- Research Kintone Stock App structure: 2h
- Implement StockCheckService class: 3h
- Implement query Kintone Stock App: 2h
- Implement optimistic locking: 2h
- Implement cache stock levels (Redis): 2h
- Testing và validation: 1h

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

**Tasks chi tiết:**
- Research stock allocation logic và business rules: 3h
- Implement StockAllocationService class: 3h
- Implement check stock cho từng item: 3h
- Implement update order status trong Kintone: 2h
- Implement update Logic Stock (reserved quantity): 3h
- Implement handle partial allocation: 2h

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

**Tasks chi tiết:**
- Research optimistic locking patterns: 1.5h
- Implement version field trong Kintone Stock App: 2h
- Implement retry on conflict logic: 2.5h
- Implement queue concurrent requests: 2.5h
- Implement logging conflicts: 1h
- Testing và validation: 0.5h

**Estimated Effort:** 10 hours (base)
**Adjusted:** Medium task - Complex logic (khó) → +35%
- Research: 20% = 2h
- Buffer: 15% = 1.5h
- **Total: 10 + 2 + 1.5 = ~14 hours**

**Estimated Effort:** 14 hours (1.75 ngày)

---

### Story 3.4: Materials Inventory App Setup

As a **System Administrator**,
I want **Materials Inventory App được setup**,
So that **quản lý tồn kho vật tư theo LOT và Location**.

**Acceptance Criteria:**
- **Given** Materials App
- **When** setup Materials Inventory App
- **Then** có Materials Inventory App với fields: material_id, lot_no, location, quantity, warehouse
- **And** track nhập/xuất theo LOT và Location
- **And** link với Stock Movements App

**Prerequisites:** Story 2.5 (Materials App), Story 4.5 (StockMovement App)

**Technical Notes:**
- Materials Inventory App trong Kintone
- Track inventory per material, lot, location, warehouse
- Link với Stock Movements App
- Support nhập/xuất transactions

**Tasks chi tiết:**
- Research Materials Inventory App setup: 2h
- Setup Materials Inventory App trong Kintone: 2h
- Implement inventory tracking: 2.5h
- Implement link với Stock Movements: 1.5h
- Testing: 1.5h

**Estimated Effort:** 9.5 hours (base)
**Adjusted:** Medium task - Inventory tracking → +35%
- Research: 20% = 1.9h
- Buffer: 15% = 1.4h
- **Total: 9.5 + 1.9 + 1.4 = ~13 hours**

**Estimated Effort:** 13 hours (1.6 ngày)

---

**Epic 3 Total:** 66 hours (8.25 ngày)

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

**Tasks chi tiết:**
- Research ReactJS và barcode scanning libraries: 4h
- Setup ReactJS app structure: 3h
- Implement react-barcode-reader integration: 3h
- Implement barcode validation (format, checksum): 2.5h
- Implement UI feedback (success/error): 2h
- Testing và validation: 1.5h

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

**Tasks chi tiết:**
- Research Kintone Task App structure: 2h
- Implement Rails API endpoint GET /api/v1/tasks/today: 2h
- Implement fetch từ Kintone Task App: 2h
- Implement TaskList React component: 3h
- Implement display tasks với status, priority: 2h
- Testing và validation: 1h

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

**Tasks chi tiết:**
- Research Physical Stock DB structure: 2h
- Implement Rails API endpoint POST /api/v1/scans: 3h
- Implement update Physical Stock DB: 3h
- Implement sync Physical → Logic Stock: 4h
- Implement update Kintone Task App status: 3h
- Implement error handling (retry, rollback): 3h
- Testing và validation: 2h

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

**Tasks chi tiết:**
- Research sync patterns và best practices: 2h
- Implement StockSyncService class: 2.5h
- Implement realtime sync (on scan): 3h
- Implement nightly batch validation: 2h
- Implement error handling (retry, DLQ): 2h
- Testing và validation: 0.5h

**Estimated Effort:** 12 hours (base)
**Adjusted:** Medium task - Kintone + Sync logic (khó) → +45%
- Research: 25% = 3h
- Buffer: 20% = 2.4h
- **Total: 12 + 3 + 2.4 = ~17 hours**

**Estimated Effort:** 17 hours (2.1 ngày)

---

### Story 4.5: StockMovement App Setup

As a **System Administrator**,
I want **StockMovement App được setup**,
So that **quản lý các loại movement (sample out, return, transfer, adjustment)**.

**Acceptance Criteria:**
- **Given** Kintone account
- **When** setup StockMovement App
- **Then** có StockMovement App với các fields: movement_type, sku, qty, rep_id, reason, shelf_from, lot_no, photo_url, status
- **And** support multiple movement types: SAMPLE_OUT, RETURN, LOCATION_TRANSFER, ADJUSTMENT
- **And** có approval workflow (nếu vượt ngưỡng)

**Prerequisites:** Story 1.1

**Technical Notes:**
- StockMovement App trong Kintone
- Movement types: SAMPLE_OUT, RETURN, LOCATION_TRANSFER, ADJUSTMENT
- Approval workflow (threshold-based)
- Link với Stock App và Physical Stock DB

**Tasks chi tiết:**
- Research StockMovement App setup: 2h
- Setup StockMovement App trong Kintone: 2h
- Implement movement types: 1.5h
- Implement approval workflow (threshold): 2h
- Testing: 1h

**Estimated Effort:** 8.5 hours (base)
**Adjusted:** Medium task - Kintone App setup → +35%
- Research: 20% = 1.7h
- Buffer: 15% = 1.3h
- **Total: 8.5 + 1.7 + 1.3 = ~12 hours**

**Estimated Effort:** 12 hours (1.5 ngày)

---

### Story 4.6: Production App Setup

As a **System Administrator**,
I want **Production App được setup**,
So that **ghi nhận sản xuất và link với BOM**.

**Acceptance Criteria:**
- **Given** BOM App và Materials Inventory App
- **When** setup Production App
- **Then** có Production App với fields: production_id, product_id, bom_id, quantity, lot_no, expiry_date, start_date, complete_date, operator
- **And** link với BOM App
- **And** tạo task xuất nguyên liệu và nhập thành phẩm

**Prerequisites:** Story 2.6 (BOM App), Story 3.4 (Materials Inventory App)

**Technical Notes:**
- Production App trong Kintone
- Link với BOM App
- Create tasks: X-Out (xuất nguyên liệu), X-In (nhập thành phẩm)
- Track production status (start/complete)
- Integration với Scan UI cho production tasks

**Tasks chi tiết:**
- Research Production App setup: 2h
- Setup Production App trong Kintone: 2h
- Implement link với BOM: 2h
- Implement production task creation: 2.5h
- Implement production status tracking: 1.5h
- Integration với Scan UI: 2h
- Testing: 2h

**Estimated Effort:** 14 hours (base)
**Adjusted:** Medium task - Production logic + Scan UI → +40%
- Research: 20% = 2.8h
- Buffer: 20% = 2.8h
- **Total: 14 + 2.8 + 2.8 = ~20 hours**

**Estimated Effort:** 20 hours (2.5 ngày)

---

### Story 4.7: Production Start Workflow

As a **Production Staff**,
I want **ghi nhận sản xuất bắt đầu**,
So that **tự động tạo task xuất nguyên liệu**.

**Acceptance Criteria:**
- **Given** Production App đã setup
- **When** ghi nhận sản xuất bắt đầu (start_date, operator, quantity)
- **Then** update Production App status = 製造開始
- **And** trigger webhook → Ruby Server
- **And** Ruby Server đọc BOM App để lấy danh sách vật tư cần
- **And** tự động tạo Task X-Out (xuất kho) cho từng vật tư trong BOM
- **And** Task hiển thị trong Scan UI

**Prerequisites:** Story 4.6, Story 2.6 (BOM App), Story 4.2 (Task List)

**Technical Notes:**
- Production App webhook khi status = 製造開始
- Ruby Server: ProductionStartService
- Read BOM App (get materials list)
- Create Task X-Out cho từng material (quantity × BOM quantity)
- Link Task với Production App
- Update Materials Inventory App (reserve materials)

**Tasks chi tiết:**
- Research production start workflow: 2h
- Implement Production App webhook handler: 2h
- Implement ProductionStartService: 3h
- Implement BOM App lookup: 2h
- Implement Task X-Out creation: 3h
- Implement Materials Inventory reservation: 2h
- Integration với Scan UI: 1.5h
- Testing: 2h

**Estimated Effort:** 17.5 hours (base)
**Adjusted:** Medium task - Production workflow + BOM logic → +40%
- Research: 20% = 3.5h
- Buffer: 20% = 3.5h
- **Total: 17.5 + 3.5 + 3.5 = ~25 hours**

**Estimated Effort:** 25 hours (3.1 ngày)

---

### Story 4.8: Production Complete Workflow

As a **Production Staff**,
I want **ghi nhận sản xuất hoàn tất**,
So that **tự động tạo task nhập thành phẩm**.

**Acceptance Criteria:**
- **Given** Production đã bắt đầu
- **When** ghi nhận sản xuất hoàn tất (complete_date, lot_no, expiry_date, quantity_actual)
- **Then** update Production App status = 製造完了
- **And** trigger webhook → Ruby Server
- **And** Ruby Server tự động tạo Task X-In (nhập kho) cho thành phẩm
- **And** Task bao gồm: SKU, quantity, lot_no, expiry_date
- **And** Task hiển thị trong Scan UI

**Prerequisites:** Story 4.7

**Technical Notes:**
- Production App webhook khi status = 製造完了
- Ruby Server: ProductionCompleteService
- Create Task X-In cho thành phẩm
- Link Task với Production App và LOT
- Validate lot_no và expiry_date

**Tasks chi tiết:**
- Research production complete workflow: 2h
- Implement Production App webhook handler: 2h
- Implement ProductionCompleteService: 3h
- Implement Task X-In creation: 2.5h
- Implement LOT và expiry_date validation: 2h
- Integration với Scan UI: 1.5h
- Testing: 2h

**Estimated Effort:** 15 hours (base)
**Adjusted:** Medium task - Production workflow → +35%
- Research: 20% = 3h
- Buffer: 15% = 2.25h
- **Total: 15 + 3 + 2.25 = ~20 hours**

**Estimated Effort:** 20 hours (2.5 ngày)

---

**Epic 4 Total:** 172 hours (21.5 ngày)

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

**Tasks chi tiết:**
- Research Ship&Co API documentation: 2.5h
- Implement ShipCoService class: 3h
- Implement REST API client: 2h
- Implement rate limiting: 1.5h
- Implement retry mechanism (3x, exponential backoff): 2h
- Implement error handling: 1h

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

**Tasks chi tiết:**
- Research AWS S3 và IAM setup: 0.5h
- Create S3 bucket: 0.5h
- Setup IAM role và policy: 1h
- Implement AWS SDK S3 integration: 1h
- Implement presigned URL generation: 1h

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

**Tasks chi tiết:**
- Research Ship&Co label creation API: 3h
- Implement ShippingJob (Sidekiq): 2h
- Implement Kintone webhook trigger: 2h
- Implement Ship&Co API create label: 4h
- Implement save PDF to S3: 2h
- Implement update Kintone Orders App: 2h
- Implement error handling (retry, DLQ): 3h
- Testing và validation: 2h

**Estimated Effort:** 20 hours (base)
**Adjusted:** Large task - Ship&Co + S3 + Kintone (khó) → +55%
- Research: 30% = 6h
- Buffer: 25% = 5h
- **Total: 20 + 6 + 5 = ~31 hours**

**Estimated Effort:** 31 hours (3.9 ngày)

---

### Story 5.4: Tracking Notification (B2C only)

As a **B2C Customer**,
I want **nhận thông báo tracking**,
So that **biết đơn hàng đang ở đâu**.

**Acceptance Criteria:**
- **Given** shipping label created cho B2C order (Shopify)
- **When** Ship&Co sends tracking update
- **Then** receive webhook từ Ship&Co
- **And** update order status trong Kintone Orders App
- **And** Ship&Co tự động sync tracking lên Shopify (không cần implement)

**Prerequisites:** Story 5.3

**Technical Notes:**
- **Chỉ áp dụng cho B2C orders** (Shopify, Rakuten, Amazon)
- Ship&Co webhook endpoint
- Update Kintone Orders App (tracking status)
- **Ship&Co đã tích hợp Shopify và tự động sync tracking** - không cần implement sync
- Email/SMS notification được Ship&Co xử lý tự động

**Tasks chi tiết:**
- Research Ship&Co webhook documentation: 1h
- Implement Ship&Co webhook endpoint: 1.5h
- Implement update Kintone Orders App (tracking status): 1.5h
- Configure Ship&Co Shopify integration (setup only): 0.5h
- Testing và validation: 0.5h

**Estimated Effort:** 5 hours (base)
**Adjusted:** Simple task - Ship&Co webhook (đơn giản hơn vì Ship&Co tự sync Shopify) → +30%
- Research: 15% = 0.75h
- Buffer: 15% = 0.75h
- **Total: 5 + 0.75 + 0.75 = ~6.5 hours**

**Estimated Effort:** 6.5 hours (0.8 ngày)

---

**Epic 5 Total:** 59.5 hours (7.4 ngày)

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

**Tasks chi tiết:**
- Research AWS Secrets Manager: 0.5h
- Create Secrets Manager secret: 0.5h
- Setup IAM role và policy: 1h
- Implement AWS SDK Secrets Manager integration: 1.5h
- Implement helper method để retrieve secrets: 0.5h

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

**Tasks chi tiết:**
- Research GMO Bank API documentation (security focus): 4h
- Implement GmoService class: 3h
- Implement REST API client với signature authentication: 4h
- Implement IP whitelist configuration: 1.5h
- Implement rate limiting: 1.5h
- Implement retry mechanism: 1.5h
- Integrate với Secrets Manager: 0.5h

**Estimated Effort:** 16 hours (base)
**Adjusted:** Large task - GMO API (rất khó, financial) → +65%
- Research: 35% = 5.6h (security research cao)
- Buffer: 30% = 4.8h (high-risk financial)
- **Total: 16 + 5.6 + 4.8 = ~26 hours**

**Estimated Effort:** 26 hours (3.25 ngày)

---

### Story 6.3: Reconciliation Logic

As a **System**,
I want **fetch và đối soát transactions với Virtual Accounts**,
So that **PayStatus được cập nhật tự động**.

**Acceptance Criteria:**
- **Given** GMO transaction (webhook hoặc polling)
- **When** receive transaction notification
- **Then** process transaction (webhook: realtime, polling: daily batch)
- **And** match transaction với Virtual Account (VA ID, amount, date)
- **And** update PayStatus trong Kintone Invoices App
- **And** trigger shipping process nếu order cần payment confirmation (Agent/Supplier orders)
- **And** handle mismatches (exceptions)

**Prerequisites:** Story 6.2

**Technical Notes:**
- **Webhook mode (realtime)**: GMO webhook endpoint để nhận transaction ngay khi thanh toán thành công (ưu tiên cho Agent/Supplier orders cần payment confirmation trước khi ship)
- **Polling mode (fallback)**: Scheduled job (daily) để fetch transactions, đảm bảo không bỏ sót nếu webhook fail
- GMO API: GET /transactions (polling) hoặc POST /webhooks/gmo/transactions (webhook)
- Process transactions (parse, validate)
- ReconciliationService
- Match transaction với VA (exact match: VA ID, amount, date within 24h)
- Update Kintone Invoices App (PayStatus = PAID)
- **Trigger shipping process**: Nếu order type = Agent/Supplier và yêu cầu payment confirmation, tự động tạo shipping task sau khi PayStatus = PAID
- Handle mismatches (name, amount, date): Log và alert Finance team (email/Slack) - Finance team xử lý trực tiếp trong Invoice App
- Log reconciliation results

**Tasks chi tiết:**
- Research GMO transactions API, webhook support và reconciliation logic: 4.5h
- Implement GMO webhook endpoint (realtime): 3h
- Implement scheduled job (daily polling - fallback): 2h
- Implement GMO API GET /transactions (polling): 3h
- Implement process transactions (parse, validate): 2.5h
- Implement ReconciliationService class: 3h
- Implement match transaction với VA: 4h
- Implement update Kintone Invoices App: 3h
- Implement trigger shipping process (nếu order cần payment confirmation): 2h
- Implement handle mismatches (log + alert đơn giản): 1.5h
- Implement logging reconciliation results: 2h

**Estimated Effort:** 26.5 hours (base)
**Adjusted:** Large task - GMO API + Webhook + Financial logic (rất khó) → +60%
- Research: 30% = 8h (GMO API + webhook + financial logic phức tạp)
- Buffer: 30% = 8h (high-risk financial)
- **Total: 26.5 + 8 + 8 = ~42.5 hours**

**Estimated Effort:** 42.5 hours (5.3 ngày)

---

**Epic 6 Total:** 73.5 hours (9.2 ngày)

---

## Tổng kết Estimate (Epic 1-6: MVP Phase)

| Epic | Stories | Base Hours | Adjusted Hours | Days (8h/day) |
| ---- | ------- | ---------- | -------------- | ------------- |
| Epic 1: Foundation | 1 | 16 | 22 | 2.75 |
| Epic 2: EC Order Intake | 6 | 51 | 73 | 9.1 |
| Epic 3: Inventory | 4 | 47.5 | 66 | 8.25 |
| Epic 4: Warehouse Scan UI | 8 | 115 | 172 | 21.5 |
| Epic 5: Shipping | 4 | 41 | 59.5 | 7.4 |
| Epic 6: Payment AR | 3 | 46.5 | 73.5 | 9.2 |
| **MVP Total** | **28** | **318.5** | **470.5** | **58.8** |

**MVP Timeline:** ~10-12 tuần (1 developer) hoặc ~5-6 tuần (2 developers)

**Note về Estimate:**
- Base estimate: 318.5 giờ (Junior developer baseline)
- Adjusted estimate: 470.5 giờ (Junior-level developer, chỉ tăng cho tasks khó/UI)
- Tăng ~38% so với baseline, chỉ áp dụng cho:
  - Tasks trung bình/khó (medium/large stories) → +35-55%
  - Tasks có ReactJS (technology mới) → +60-65%
  - Tasks có Kintone view/UI → +45%
  - Tasks có integration phức tạp (GMO, Ship&Co, Yoom) → +45-65%
  - Tasks đơn giản: Tăng nhẹ (+25-30%)

**Note:** Epic 1 chỉ làm Project Setup. Các core services (Kintone, Common Services, AWS Infrastructure) được implement trong các epics sau khi cần thiết.

---

## Epic 7: B2B Agency Portal

**Goal:** Tạo portal cho đại lý B2B để đặt hàng, theo dõi đơn hàng và xem thông tin rank/discount.

### Story 7.1: Kintone Guest Space Setup

As a **System Administrator**,
I want **Kintone Guest Space được setup cho Agency Portal**,
So that **đại lý có thể đăng nhập và truy cập portal**.

**Acceptance Criteria:**
- **Given** Kintone account
- **When** setup Guest Space
- **Then** có Guest Space cho Agency Portal
- **And** có authentication mechanism (Kintone login)
- **And** có permission settings (mỗi agency chỉ thấy đơn của mình)

**Prerequisites:** Story 1.1

**Technical Notes:**
- Setup Kintone Guest Space
- Configure authentication (Kintone login)
- Setup permission rules (filter by agency_id)
- Mobile-responsive design (Kintone default)

**Tasks chi tiết:**
- Research Kintone Guest Space setup: 2h
- Setup Guest Space trong Kintone: 2h
- Configure authentication: 1h
- Setup permission rules (filter by agency): 2h
- Test mobile responsiveness: 1h

**Estimated Effort:** 8 hours (base)
**Adjusted:** Medium task - Kintone Guest Space (mới) → +40%
- Research: 20% = 1.6h
- Buffer: 20% = 1.6h
- **Total: 8 + 1.6 + 1.6 = ~11 hours**

**Estimated Effort:** 11 hours (1.4 ngày)

---

### Story 7.2: Order Creation Form

As an **Agency**,
I want **tạo đơn hàng qua Portal**,
So that **tôi có thể đặt hàng dễ dàng**.

**Acceptance Criteria:**
- **Given** Agency đã đăng nhập
- **When** tạo đơn hàng
- **Then** có form nhập SKU, số lượng, địa chỉ giao, ngày mong muốn
- **And** hiển thị warehouse đề xuất (theo route_rule)
- **And** hiển thị tồn kho tham chiếu
- **And** submit đơn → tạo record trong Orders App

**Prerequisites:** Story 7.1

**Technical Notes:**
- Kintone form trong Guest Space
- Lookup SKU từ Products App
- Display warehouse suggestion (route_rule: MWATER = Gifu, còn lại = Amagasaki)
- Display stock reference từ Stock App
- Create order record trong Orders App (via webhook hoặc API)

**Tasks chi tiết:**
- Research Kintone form customization: 2h
- Design order creation form: 2h
- Implement SKU lookup: 2h
- Implement warehouse suggestion logic: 2h
- Implement stock reference display: 2h
- Implement order creation (webhook/API): 3h
- Testing: 2h

**Estimated Effort:** 15 hours (base)
**Adjusted:** Medium task - Kintone form + logic (khó) → +45%
- Research: 25% = 3.75h
- Buffer: 20% = 3h
- **Total: 15 + 3.75 + 3 = ~22 hours**

**Estimated Effort:** 22 hours (2.75 ngày)

---

### Story 7.3: Order History & Tracking

As an **Agency**,
I want **xem lịch sử đơn hàng và theo dõi trạng thái**,
So that **tôi biết đơn hàng đang ở đâu**.

**Acceptance Criteria:**
- **Given** Agency đã đăng nhập
- **When** xem order history
- **Then** hiển thị danh sách đơn hàng của agency (filtered)
- **And** hiển thị order status (realtime)
- **And** có thể xem chi tiết đơn hàng
- **And** có thể tải PDF (acknowledgement, quote, delivery note)

**Prerequisites:** Story 7.2

**Technical Notes:**
- Kintone view/list trong Guest Space
- Filter orders by agency_id (permission)
- Display order status từ Orders App
- Link to PDF files (S3 presigned URLs)
- Realtime status update (webhook hoặc polling)

**Tasks chi tiết:**
- Research Kintone view customization: 1.5h
- Setup order list view (filtered by agency): 2h
- Implement order detail view: 2h
- Implement PDF download links (S3 presigned URLs): 2h
- Implement realtime status update: 2h
- Testing: 1.5h

**Estimated Effort:** 11 hours (base)
**Adjusted:** Medium task - Kintone view + S3 integration → +40%
- Research: 20% = 2.2h
- Buffer: 20% = 2.2h
- **Total: 11 + 2.2 + 2.2 = ~15 hours**

**Estimated Effort:** 15 hours (1.9 ngày)

---

### Story 7.4: Agency Dashboard

As an **Agency**,
I want **xem dashboard với rank, discount và commission**,
So that **tôi biết thông tin về hạng và phần thưởng**.

**Acceptance Criteria:**
- **Given** Agency đã đăng nhập
- **When** xem dashboard
- **Then** hiển thị agency rank (Starter/Silver/Gold/Platinum)
- **And** hiển thị discount rate (based on rank)
- **And** hiển thị commission/rewards summary
- **And** hiển thị gauge "còn X chai để đạt hạng kế tiếp"

**Prerequisites:** Story 7.1, Story 10.1 (Agency Rank)

**Technical Notes:**
- Kintone dashboard/widget trong Guest Space
- Display rank từ Partners App
- Display discount rate từ Pricing Rules App
- Display commission/rewards từ RewardsLedger App
- Calculate gauge (progress to next rank)

**Tasks chi tiết:**
- Research Kintone dashboard/widget: 2h
- Design dashboard layout: 2h
- Implement rank display: 1.5h
- Implement discount rate display: 1.5h
- Implement commission/rewards summary: 2h
- Implement gauge calculation: 2h
- Testing: 1h

**Estimated Effort:** 12 hours (base)
**Adjusted:** Medium task - Kintone dashboard + calculations → +40%
- Research: 20% = 2.4h
- Buffer: 20% = 2.4h
- **Total: 12 + 2.4 + 2.4 = ~17 hours**

**Estimated Effort:** 17 hours (2.1 ngày)

---

**Epic 7 Total:** 65 hours (8.1 ngày)

---

## Epic 8: Procurement Automation

**Goal:** Tự động giám sát tồn kho, tạo PO và quản lý nhà cung cấp.

### Story 8.1: Low Stock Monitoring

As a **System**,
I want **giám sát tồn kho và phát hiện khi stock < reorder point**,
So that **tự động tạo PO khi cần**.

**Acceptance Criteria:**
- **Given** scheduled job (daily, sau outbound processing)
- **When** check stock levels
- **Then** so sánh với reorder_point (per SKU, per warehouse)
- **And** trigger PO creation khi stock < reorder_point
- **And** log monitoring results

**Prerequisites:** Story 3.1 (Stock App), Story 4.4 (Stock Sync)

**Technical Notes:**
- Scheduled job (daily, sau outbound processing)
- Read stock từ Stock App (Logic Stock)
- Compare với reorder_point (per SKU, per warehouse)
- Trigger PO creation service
- Log monitoring results

**Tasks chi tiết:**
- Research stock monitoring logic: 2h
- Implement scheduled job (daily): 2h
- Implement stock level check: 2h
- Implement reorder_point comparison: 2h
- Implement PO creation trigger: 2h
- Implement logging: 1h

**Estimated Effort:** 11 hours (base)
**Adjusted:** Medium task - Scheduled job + logic → +35%
- Research: 20% = 2.2h
- Buffer: 15% = 1.65h
- **Total: 11 + 2.2 + 1.65 = ~15 hours**

**Estimated Effort:** 15 hours (1.9 ngày)

---

### Story 8.2: Auto PO Generation

As a **System**,
I want **tự động tạo PO khi stock < reorder point**,
So that **đảm bảo không thiếu hàng**.

**Acceptance Criteria:**
- **Given** stock < reorder_point
- **When** create PO
- **Then** tạo PO record trong PO App
- **And** generate PO PDF (items, quantities, prices, delivery date)
- **And** save PDF to S3
- **And** link PDF to PO record
- **And** send PO to supplier via email

**Prerequisites:** Story 8.1

**Technical Notes:**
- PO App trong Kintone
- Generate PO PDF (template)
- Save PDF to S3
- Link PDF to PO record (presigned URL)
- Send email với PDF attachment
- Handle lot size, batch rules

**Tasks chi tiết:**
- Research PO generation logic: 2h
- Setup PO App trong Kintone: 2h
- Implement PO record creation: 2h
- Implement PO PDF generation: 3h
- Implement S3 upload: 1.5h
- Implement email sending: 2h
- Implement lot size/batch rules: 2h
- Testing: 1.5h

**Estimated Effort:** 16 hours (base)
**Adjusted:** Medium task - PDF generation + email → +40%
- Research: 20% = 3.2h
- Buffer: 20% = 3.2h
- **Total: 16 + 3.2 + 3.2 = ~22 hours**

**Estimated Effort:** 22 hours (2.75 ngày)

---

### Story 8.3: Supplier Portal (Kintone Guest Space)

As a **Supplier**,
I want **xem và xác nhận PO qua Portal**,
So that **tôi có thể cập nhật delivery date và upload confirmation**.

**Acceptance Criteria:**
- **Given** Supplier đã đăng nhập Guest Space
- **When** xem PO
- **Then** hiển thị PO details
- **And** có thể xác nhận PO
- **And** có thể update delivery date
- **And** có thể upload delivery confirmation
- **And** có thể phân lô (分納)

**Prerequisites:** Story 8.2

**Technical Notes:**
- Kintone Guest Space cho Supplier Portal
- Permission: mỗi supplier chỉ thấy PO của mình
- Form để confirm PO, update delivery date
- File upload cho delivery confirmation
- Support phân lô (multiple deliveries)

**Tasks chi tiết:**
- Research Supplier Portal setup: 2h
- Setup Guest Space cho Supplier: 2h
- Implement PO list view (filtered): 2h
- Implement PO confirmation form: 2h
- Implement delivery date update: 1.5h
- Implement file upload: 2h
- Implement phân lô support: 2h
- Testing: 1.5h

**Estimated Effort:** 15 hours (base)
**Adjusted:** Medium task - Kintone Guest Space + forms → +40%
- Research: 20% = 3h
- Buffer: 20% = 3h
- **Total: 15 + 3 + 3 = ~21 hours**

**Estimated Effort:** 21 hours (2.6 ngày)

---

### Story 8.4: Approval Workflow

As a **Finance Team**,
I want **2-level approval workflow cho PO**,
So that **đảm bảo PO được duyệt đúng quy trình**.

**Acceptance Criteria:**
- **Given** PO được tạo
- **When** require approval
- **Then** yêu cầu approval cấp 1 (Purchasing Staff)
- **And** yêu cầu approval cấp 2 (Finance)
- **And** send reminders nếu approval pending > 24h
- **And** track approval history

**Prerequisites:** Story 8.2

**Technical Notes:**
- Kintone approval workflow
- 2-level approval: Purchasing → Finance
- Reminder mechanism (email/Slack)
- Approval history tracking
- Webhook trigger khi approved

**Tasks chi tiết:**
- Research Kintone approval workflow: 2h
- Setup 2-level approval: 2h
- Implement reminder mechanism: 2h
- Implement approval history tracking: 1.5h
- Implement webhook trigger: 1.5h
- Testing: 1h

**Estimated Effort:** 10 hours (base)
**Adjusted:** Medium task - Kintone approval workflow → +35%
- Research: 20% = 2h
- Buffer: 15% = 1.5h
- **Total: 10 + 2 + 1.5 = ~14 hours**

**Estimated Effort:** 14 hours (1.75 ngày)

---

**Epic 8 Total:** 72 hours (9 ngày)

---

## Epic 9: AR/AP Management

**Goal:** Quản lý Invoice (AR) và Payment Request (AP) với approval workflow và GMO batch payment.

### Story 9.1: Invoice Management (AR)

As a **Finance Team**,
I want **quản lý Invoice records**,
So that **theo dõi thanh toán từ khách hàng**.

**Acceptance Criteria:**
- **Given** Order được tạo
- **When** create Invoice
- **Then** tạo Invoice record trong Invoice App
- **And** link Invoice với Order và Virtual Account
- **And** track payment status (pending/paid/overdue)
- **And** update PayStatus từ Story 6.3 (Reconciliation)

**Prerequisites:** Story 2.4 (B2B Order Processing), Story 6.1 (GMO Setup)

**Technical Notes:**
- Invoice App trong Kintone
- Create Invoice khi Order được approve
- Link với Order và VA ID
- Track PayStatus (pending/paid/overdue)
- Integration với Story 6.3 (update PayStatus)

**Tasks chi tiết:**
- Research Invoice App setup: 2h
- Setup Invoice App trong Kintone: 2h
- Implement Invoice creation (from Order): 2h
- Implement link với Order và VA: 2h
- Implement PayStatus tracking: 2h
- Integration với Story 6.3: 1.5h
- Testing: 1.5h

**Estimated Effort:** 13 hours (base)
**Adjusted:** Medium task - Kintone Invoice App → +35%
- Research: 20% = 2.6h
- Buffer: 15% = 1.95h
- **Total: 13 + 2.6 + 1.95 = ~18 hours**

**Estimated Effort:** 18 hours (2.25 ngày)

---

### Story 9.2: Payment Request (AP)

As a **Finance Team**,
I want **quản lý Payment Request records**,
So that **theo dõi thanh toán cho nhà cung cấp**.

**Acceptance Criteria:**
- **Given** PO được approve hoặc Invoice từ supplier
- **When** create Payment Request
- **Then** tạo Payment Request record trong Payment Request App
- **And** require 2-level approval (Finance → CEO)
- **And** track approval status và history
- **And** trigger GMO payment khi approved

**Prerequisites:** Story 8.4 (PO Approval), Story 6.2 (GMO API Client)

**Technical Notes:**
- Payment Request App trong Kintone
- Create Payment Request từ PO hoặc Supplier Invoice
- 2-level approval: Finance → CEO
- Approval history tracking
- Webhook trigger khi approved → Story 9.3

**Tasks chi tiết:**
- Research Payment Request App setup: 2h
- Setup Payment Request App trong Kintone: 2h
- Implement Payment Request creation: 2h
- Implement 2-level approval workflow: 2h
- Implement approval history tracking: 1.5h
- Implement webhook trigger: 1.5h
- Testing: 1h

**Estimated Effort:** 12 hours (base)
**Adjusted:** Medium task - Kintone Payment Request App → +35%
- Research: 20% = 2.4h
- Buffer: 15% = 1.8h
- **Total: 12 + 2.4 + 1.8 = ~16 hours**

**Estimated Effort:** 16 hours (2 ngày)

---

### Story 9.3: GMO Batch Payment Execution

As a **System**,
I want **thực hiện batch payment qua GMO API**,
So that **thanh toán tự động cho nhà cung cấp**.

**Acceptance Criteria:**
- **Given** Payment Request được approve
- **When** execute payment
- **Then** call GMO API (/payments)
- **And** handle batch payments (multiple payouts)
- **And** update Payouts App với results
- **And** handle payment failures (retry, alert)

**Prerequisites:** Story 9.2, Story 6.2 (GMO API Client)

**Technical Notes:**
- GMO API: POST /payments (batch payment)
- Process multiple payouts trong một batch
- Update Payouts App với results (success/failure)
- Retry mechanism cho failures
- Alert Finance team nếu failure

**Tasks chi tiết:**
- Research GMO batch payment API: 3h
- Implement batch payment service: 3h
- Implement GMO API call (/payments): 3h
- Implement update Payouts App: 2h
- Implement retry mechanism: 2h
- Implement failure alert: 1.5h
- Testing: 2h

**Estimated Effort:** 16.5 hours (base)
**Adjusted:** Large task - GMO batch payment (financial, khó) → +55%
- Research: 30% = 5h (financial API phức tạp)
- Buffer: 25% = 4.1h (high-risk financial)
- **Total: 16.5 + 5 + 4.1 = ~26 hours**

**Estimated Effort:** 26 hours (3.25 ngày)

---

**Epic 9 Total:** 60 hours (7.5 ngày)

---

## Epic 10: Agency Rank Management

**Goal:** Tự động tính rank đại lý và áp dụng discount rate.

### Story 10.1: Rank Calculation

As a **System**,
I want **tính rank đại lý hàng tháng**,
So that **xác định hạng và discount rate**.

**Acceptance Criteria:**
- **Given** scheduled job (monthly)
- **When** calculate rank
- **Then** tổng hợp sales volume (last 12 months) per agency
- **And** xác định rank tier: Starter (< 1M), Silver (1M-5M), Gold (5M-20M), Platinum (> 20M)
- **And** lưu rank vào Partners App
- **And** lưu lịch sử thay đổi rank

**Prerequisites:** Story 2.4 (B2B Orders), Story 6.3 (Payment Reconciliation)

**Technical Notes:**
- Scheduled job (monthly batch)
- Aggregate sales volume từ Orders App (last 12 months, PAID orders only)
- Calculate rank tier based on sales volume
- Update Partners App với rank
- Log rank history

**Tasks chi tiết:**
- Research rank calculation logic: 3h
- Implement scheduled job (monthly): 2h
- Implement sales volume aggregation: 3h
- Implement rank tier calculation: 2h
- Implement update Partners App: 2h
- Implement rank history logging: 1.5h
- Testing: 2h

**Estimated Effort:** 15.5 hours (base)
**Adjusted:** Medium task - Batch calculation + logic → +40%
- Research: 20% = 3.1h
- Buffer: 20% = 3.1h
- **Total: 15.5 + 3.1 + 3.1 = ~22 hours**

**Estimated Effort:** 22 hours (2.75 ngày)

---

### Story 10.2: Discount Application

As a **System**,
I want **áp dụng discount rate tự động dựa trên rank**,
So that **đại lý được hưởng discount đúng hạng**.

**Acceptance Criteria:**
- **Given** Order được tạo
- **When** apply discount
- **Then** lookup agency rank từ Partners App
- **And** lookup discount rate từ Pricing Rules App
- **And** apply discount vào order total
- **And** log discount history cho audit

**Prerequisites:** Story 10.1, Story 7.2 (Order Creation)

**Technical Notes:**
- Pricing Rules App trong Kintone
- Lookup rank từ Partners App
- Lookup discount rate từ Pricing Rules App
- Apply discount vào order (realtime hoặc batch)
- Log discount history

**Tasks chi tiết:**
- Research discount application logic: 2h
- Setup Pricing Rules App trong Kintone: 2h
- Implement rank lookup: 1.5h
- Implement discount rate lookup: 1.5h
- Implement discount application: 2h
- Implement discount history logging: 1.5h
- Testing: 1.5h

**Estimated Effort:** 12 hours (base)
**Adjusted:** Medium task - Discount logic → +35%
- Research: 20% = 2.4h
- Buffer: 15% = 1.8h
- **Total: 12 + 2.4 + 1.8 = ~16 hours**

**Estimated Effort:** 16 hours (2 ngày)

---

**Epic 10 Total:** 38 hours (4.75 ngày)

---

## Epic 11: Affiliate Commission (GoAffPro)

**Goal:** Tích hợp GoAffPro để quản lý affiliate links và commission.

### Story 11.1: GoAffPro Integration

As a **Developer**,
I want **tích hợp GoAffPro API**,
So that **có thể quản lý affiliate links và commission**.

**Acceptance Criteria:**
- **Given** GoAffPro account
- **When** integrate GoAffPro
- **Then** có GoAffPro API client
- **And** có authentication mechanism
- **And** có error handling (retry, DLQ)

**Prerequisites:** Story 1.1

**Technical Notes:**
- GoAffPro API client
- Authentication (API key)
- Error handling (retry, DLQ)
- Webhook receiver (nếu GoAffPro support)

**Tasks chi tiết:**
- Research GoAffPro API documentation: 4h
- Implement GoAffPro API client: 3h
- Implement authentication: 1.5h
- Implement error handling: 2h
- Implement webhook receiver (nếu có): 2h
- Testing: 2h

**Estimated Effort:** 14.5 hours (base)
**Adjusted:** Medium task - Third-party API integration → +45%
- Research: 25% = 3.6h
- Buffer: 20% = 2.9h
- **Total: 14.5 + 3.6 + 2.9 = ~21 hours**

**Estimated Effort:** 21 hours (2.6 ngày)

---

### Story 11.2: Link Generation

As an **Affiliate Partner**,
I want **generate affiliate links**,
So that **tôi có thể share links để kiếm commission**.

**Acceptance Criteria:**
- **Given** Affiliate partner
- **When** generate link
- **Then** tạo link: links.credie.jp/p/{token}
- **And** link include partner_id trong cookie
- **And** link redirect to EC store (Shopify)
- **And** track clicks

**Prerequisites:** Story 11.1

**Technical Notes:**
- Link shortener service (hoặc tự build)
- Generate unique token per partner
- Set cookie với partner_id
- Redirect to Shopify store
- Track clicks (analytics)

**Tasks chi tiết:**
- Research link shortener options: 2h
- Implement link generation service: 3h
- Implement token generation: 1.5h
- Implement cookie setting: 2h
- Implement redirect to Shopify: 1.5h
- Implement click tracking: 2h
- Testing: 1.5h

**Estimated Effort:** 14 hours (base)
**Adjusted:** Medium task - Link generation + tracking → +40%
- Research: 20% = 2.8h
- Buffer: 20% = 2.8h
- **Total: 14 + 2.8 + 2.8 = ~20 hours**

**Estimated Effort:** 20 hours (2.5 ngày)

---

### Story 11.3: Commission Tracking

As a **System**,
I want **track commission từ affiliate orders**,
So that **tính toán và ghi nhận commission đúng**.

**Acceptance Criteria:**
- **Given** Order có partner_id (từ cookie)
- **When** track commission
- **Then** track clicks và orders với partner_id
- **And** calculate commission based on rules (SKU, channel, tier)
- **And** record trong RewardsLedger App

**Prerequisites:** Story 11.2, Story 2.1 (Order Intake)

**Technical Notes:**
- Track partner_id từ cookie khi order created
- Calculate commission từ GoAffPro rules (SKU, channel, tier)
- Record trong RewardsLedger App
- Integration với Story 2.1 (capture partner_id)

**Tasks chi tiết:**
- Research commission calculation rules: 3h
- Implement partner_id tracking: 2h
- Implement commission calculation: 3h
- Implement RewardsLedger App integration: 2h
- Integration với Story 2.1: 2h
- Testing: 2h

**Estimated Effort:** 14 hours (base)
**Adjusted:** Medium task - Commission logic → +40%
- Research: 20% = 2.8h
- Buffer: 20% = 2.8h
- **Total: 14 + 2.8 + 2.8 = ~20 hours**

**Estimated Effort:** 20 hours (2.5 ngày)

---

### Story 11.4: Monthly Aggregation & Payout

As a **System**,
I want **tổng hợp commission hàng tháng và thực hiện payout**,
So that **affiliate partners nhận được commission**.

**Acceptance Criteria:**
- **Given** scheduled job (monthly)
- **When** aggregate commissions
- **Then** tổng hợp commission per partner (last month)
- **And** generate Payouts records
- **And** execute payout via GMO API
- **And** update RewardsLedger App

**Prerequisites:** Story 11.3, Story 6.2 (GMO API Client), Story 9.3 (Batch Payment)

**Technical Notes:**
- Scheduled job (monthly)
- Aggregate commission từ RewardsLedger App
- Generate Payouts records
- Execute payout via GMO API (reuse Story 9.3)
- Update RewardsLedger App với payout status

**Tasks chi tiết:**
- Research monthly aggregation logic: 2h
- Implement scheduled job (monthly): 2h
- Implement commission aggregation: 3h
- Implement Payouts record generation: 2h
- Integration với Story 9.3 (GMO payment): 2h
- Implement RewardsLedger update: 1.5h
- Testing: 2h

**Estimated Effort:** 14.5 hours (base)
**Adjusted:** Medium task - Aggregation + payment → +40%
- Research: 20% = 2.9h
- Buffer: 20% = 2.9h
- **Total: 14.5 + 2.9 + 2.9 = ~20 hours**

**Estimated Effort:** 20 hours (2.5 ngày)

---

**Epic 11 Total:** 81 hours (10.1 ngày)

---

## Epic 12: Rewards & Dividends

**Goal:** Quản lý RewardsLedger và tính toán dividends hàng quý.

### Story 12.1: RewardsLedger Management

As a **Finance Team**,
I want **quản lý RewardsLedger records**,
So that **theo dõi tất cả commission và reward transactions**.

**Acceptance Criteria:**
- **Given** Commission hoặc reward transaction
- **When** record trong RewardsLedger
- **Then** tạo record với type, amount, period, status
- **And** support multiple reward types (commission, dividend)
- **And** track status (pending/paid/cancelled)

**Prerequisites:** Story 11.3 (Commission Tracking)

**Technical Notes:**
- RewardsLedger App trong Kintone
- Record types: commission, dividend
- Track amount, period, status
- Integration với Story 11.3 và Story 12.2

**Tasks chi tiết:**
- Research RewardsLedger App setup: 2h
- Setup RewardsLedger App trong Kintone: 2h
- Implement record creation: 2h
- Implement multiple reward types: 2h
- Implement status tracking: 1.5h
- Integration với Story 11.3: 1.5h
- Testing: 1h

**Estimated Effort:** 12 hours (base)
**Adjusted:** Medium task - Kintone RewardsLedger App → +35%
- Research: 20% = 2.4h
- Buffer: 15% = 1.8h
- **Total: 12 + 2.4 + 1.8 = ~16 hours**

**Estimated Effort:** 16 hours (2 ngày)

---

### Story 12.2: Quarterly Dividend Calculation

As a **System**,
I want **tính toán dividends hàng quý**,
So that **tính toán và ghi nhận dividends đúng**.

**Acceptance Criteria:**
- **Given** scheduled job (quarterly)
- **When** calculate dividends
- **Then** sử dụng MWATER/SILICA_H2 unit prices
- **And** apply rate rules (threshold-based)
- **And** calculate dividend per partner
- **And** record trong RewardsLedger App

**Prerequisites:** Story 12.1, Story 10.1 (Rank Calculation)

**Technical Notes:**
- Scheduled job (quarterly)
- Use MWATER/SILICA_H2 unit prices
- Apply rate rules (threshold-based, có thể từ Pricing Rules App)
- Calculate dividend per partner
- Record trong RewardsLedger App

**Tasks chi tiết:**
- Research dividend calculation logic: 4h
- Implement scheduled job (quarterly): 2h
- Implement unit price lookup: 2h
- Implement rate rules application: 3h
- Implement dividend calculation: 3h
- Implement RewardsLedger recording: 2h
- Testing: 2h

**Estimated Effort:** 19 hours (base)
**Adjusted:** Large task - Financial calculation logic (khó) → +50%
- Research: 30% = 5.7h (financial logic phức tạp)
- Buffer: 20% = 3.8h
- **Total: 19 + 5.7 + 3.8 = ~29 hours**

**Estimated Effort:** 29 hours (3.6 ngày)

---

### Story 12.3: Payout Execution & Statements

As a **System**,
I want **thực hiện payout và generate statements**,
So that **partners nhận được rewards và có chứng từ**.

**Acceptance Criteria:**
- **Given** Rewards được approve
- **When** execute payout
- **Then** generate Payouts records
- **And** execute payout via GMO API
- **And** generate PDF statements (S3 storage)
- **And** send statements to recipients (email/portal)

**Prerequisites:** Story 12.2, Story 9.3 (GMO Batch Payment)

**Technical Notes:**
- Generate Payouts records từ RewardsLedger
- Execute payout via GMO API (reuse Story 9.3)
- Generate PDF statements (template)
- Save PDF to S3
- Send email với PDF attachment
- Portal download (presigned URL)

**Tasks chi tiết:**
- Research payout execution logic: 2h
- Implement Payouts record generation: 2h
- Integration với Story 9.3 (GMO payment): 2h
- Implement PDF statement generation: 3h
- Implement S3 upload: 1.5h
- Implement email sending: 2h
- Implement portal download: 1.5h
- Testing: 2h

**Estimated Effort:** 16 hours (base)
**Adjusted:** Medium task - PDF generation + payment → +40%
- Research: 20% = 3.2h
- Buffer: 20% = 3.2h
- **Total: 16 + 3.2 + 3.2 = ~22 hours**

**Estimated Effort:** 22 hours (2.75 ngày)

---

**Epic 12 Total:** 67 hours (8.4 ngày)

---

## Epic 13: Dashboard & Reporting

**Goal:** Tạo dashboard realtime và reporting system.

### Story 13.1: Realtime Dashboard

As a **Manager**,
I want **xem dashboard realtime**,
So that **theo dõi orders, inventory, payments**.

**Acceptance Criteria:**
- **Given** Dashboard được truy cập
- **When** view dashboard
- **Then** hiển thị orders (realtime)
- **And** hiển thị inventory status
- **And** hiển thị payments (AR/AP)
- **And** support filtering (date range, channel, status)
- **And** accessible 24/7

**Prerequisites:** Story 1.1, Story 2.1, Story 3.1, Story 6.3

**Technical Notes:**
- Kintone dashboard hoặc custom dashboard (ReactJS)
- Realtime data từ các Kintone Apps
- Filtering mechanism
- Responsive design

**Tasks chi tiết:**
- Research dashboard options (Kintone vs custom): 3h
- Design dashboard layout: 3h
- Implement orders display: 3h
- Implement inventory display: 2h
- Implement payments display: 2h
- Implement filtering: 2h
- Implement realtime updates: 3h
- Testing: 2h

**Estimated Effort:** 20 hours (base)
**Adjusted:** Large task - Dashboard + realtime (khó) → +50%
- Research: 30% = 6h (dashboard design phức tạp)
- Buffer: 20% = 4h
- **Total: 20 + 6 + 4 = ~30 hours**

**Estimated Effort:** 30 hours (3.75 ngày)

---

### Story 13.2: Audit Trail

As a **System**,
I want **log tất cả critical operations**,
So that **có audit trail đầy đủ**.

**Acceptance Criteria:**
- **Given** Critical operation (order creation, payment, stock update)
- **When** operation executed
- **Then** log user, timestamp, action, result
- **And** store logs in S3 for long-term retention
- **And** có thể query logs

**Prerequisites:** Story 1.1

**Technical Notes:**
- Audit log service
- Log critical operations (order, payment, stock)
- Store logs in S3 (organized by date)
- Query mechanism

**Tasks chi tiết:**
- Research audit trail best practices: 2h
- Implement audit log service: 3h
- Implement logging cho critical operations: 3h
- Implement S3 storage: 2h
- Implement query mechanism: 2h
- Testing: 1.5h

**Estimated Effort:** 13.5 hours (base)
**Adjusted:** Medium task - Audit trail + S3 → +35%
- Research: 20% = 2.7h
- Buffer: 15% = 2h
- **Total: 13.5 + 2.7 + 2 = ~18 hours**

**Estimated Effort:** 18 hours (2.25 ngày)

---

### Story 13.3: Reporting System

As a **Manager**,
I want **generate reports**,
So that **phân tích sales, inventory, payments, commissions**.

**Acceptance Criteria:**
- **Given** Report request
- **When** generate report
- **Then** generate reports: sales, inventory, payments, commissions
- **And** export reports (CSV, PDF)
- **And** support scheduled generation (daily, weekly, monthly)

**Prerequisites:** Story 13.1

**Technical Notes:**
- Report generation service
- Multiple report types (sales, inventory, payments, commissions)
- Export formats (CSV, PDF)
- Scheduled generation (Sidekiq cron)

**Tasks chi tiết:**
- Research reporting options: 2h
- Design report templates: 2h
- Implement report generation service: 3h
- Implement CSV export: 2h
- Implement PDF export: 3h
- Implement scheduled generation: 2h
- Testing: 2h

**Estimated Effort:** 16 hours (base)
**Adjusted:** Medium task - Report generation → +40%
- Research: 20% = 3.2h
- Buffer: 20% = 3.2h
- **Total: 16 + 3.2 + 3.2 = ~22 hours**

**Estimated Effort:** 22 hours (2.75 ngày)

---

**Epic 13 Total:** 70 hours (8.75 ngày)

---

## Epic 14: Sample Outflow Management (BF-09)

**Goal:** Quản lý xuất mẫu dùng thử qua Scan UI với approval workflow và audit trail.

### Story 14.1: Sample Outflow via Scan UI

As a **Sales Staff**,
I want **xuất mẫu qua Scan UI**,
So that **xuất mẫu nhanh chóng và có audit trail**.

**Acceptance Criteria:**
- **Given** Sales tạo sample request trong StockMovement App
- **When** warehouse staff scan via Scan UI
- **Then** hiển thị sample request trong Scan UI
- **And** quét kệ (Shelf) → quét sản phẩm/LOT → nhập số lượng
- **And** có thể đính kèm ảnh minh chứng
- **And** xác nhận → giảm tồn vật lý và logic
- **And** ghi audit log

**Prerequisites:** Story 4.5 (StockMovement App), Story 4.1 (Scan UI)

**Technical Notes:**
- Integration với Scan UI (ReactJS)
- Display sample requests trong Scan UI
- Barcode scanning (Shelf, JAN/EAN, LOT)
- Photo upload (S3)
- Update Physical Stock DB và Stock App
- Audit logging

**Tasks chi tiết:**
- Research sample outflow flow: 2h
- Implement display sample requests trong Scan UI: 2h
- Implement barcode scanning cho sample: 2h
- Implement photo upload: 2h
- Implement update Physical Stock DB: 2h
- Implement update Stock App (logic): 1.5h
- Implement audit logging: 1.5h
- Testing: 2h

**Estimated Effort:** 15 hours (base)
**Adjusted:** Medium task - Scan UI integration + stock update → +40%
- Research: 20% = 3h
- Buffer: 20% = 3h
- **Total: 15 + 3 + 3 = ~21 hours**

**Estimated Effort:** 21 hours (2.6 ngày)

---

**Epic 14 Total:** 21 hours (2.6 ngày)

---

## Epic 15: Production & Materials Management

**Goal:** Quản lý Materials, BOM, Production và Materials Inventory.

**Note:** Các app setup đã được di chuyển lên các epic trên:
- Materials App, BOM App → Epic 2 (Story 2.5, 2.6)
- Materials Inventory App → Epic 3 (Story 3.4)
- Production App → Epic 4 (Story 4.6)

**Epic 15 Total:** 0 hours (đã di chuyển hết)

---

## Epic 16: Return/Move/Adjustment Management (System Flow 6)

**Goal:** Quản lý Return, Location Transfer và Stock Adjustment qua Scan UI.

### Story 16.1: Return Management via Scan UI

As a **Warehouse Staff**,
I want **xử lý return qua Scan UI**,
So that **nhập hàng hoàn trả nhanh chóng**.

**Acceptance Criteria:**
- **Given** Return request trong StockMovement App
- **When** warehouse staff scan via Scan UI
- **Then** hiển thị return request trong Scan UI
- **And** quét kệ → quét sản phẩm/LOT → nhập số lượng
- **And** xác nhận → tăng tồn vật lý và logic
- **And** update StockMovement App status

**Prerequisites:** Story 4.5 (StockMovement App), Story 4.1 (Scan UI)

**Technical Notes:**
- Integration với Scan UI
- Display return requests
- Barcode scanning
- Update Physical Stock DB (increase)
- Update Stock App (increase)
- Update StockMovement App status

**Tasks chi tiết:**
- Research return flow: 1.5h
- Implement display return requests trong Scan UI: 2h
- Implement barcode scanning cho return: 2h
- Implement update Physical Stock DB (increase): 2h
- Implement update Stock App (increase): 1.5h
- Implement update StockMovement App: 1h
- Testing: 1.5h

**Estimated Effort:** 11.5 hours (base)
**Adjusted:** Medium task - Scan UI + stock update → +35%
- Research: 20% = 2.3h
- Buffer: 15% = 1.7h
- **Total: 11.5 + 2.3 + 1.7 = ~16 hours**

**Estimated Effort:** 16 hours (2 ngày)

---

### Story 16.2: Location Transfer via Scan UI

As a **Warehouse Staff**,
I want **di chuyển hàng giữa các kệ qua Scan UI**,
So that **tối ưu vị trí kho**.

**Acceptance Criteria:**
- **Given** Transfer request trong StockMovement App
- **When** warehouse staff scan via Scan UI
- **Then** hiển thị transfer request (from shelf → to shelf)
- **And** quét kệ nguồn → quét sản phẩm/LOT → quét kệ đích
- **And** xác nhận → giảm tồn ở kệ nguồn, tăng tồn ở kệ đích
- **And** không thay đổi tồn logic (chỉ thay đổi location)

**Prerequisites:** Story 4.5 (StockMovement App), Story 4.1 (Scan UI)

**Technical Notes:**
- Integration với Scan UI
- Display transfer requests
- Barcode scanning (from shelf, product/LOT, to shelf)
- Update Physical Stock DB (decrease from, increase to)
- Stock App không đổi (chỉ đổi location)

**Tasks chi tiết:**
- Research location transfer flow: 1.5h
- Implement display transfer requests trong Scan UI: 2h
- Implement barcode scanning cho transfer: 2.5h
- Implement update Physical Stock DB (from/to): 2.5h
- Implement update StockMovement App: 1h
- Testing: 1.5h

**Estimated Effort:** 11 hours (base)
**Adjusted:** Medium task - Scan UI + location update → +35%
- Research: 20% = 2.2h
- Buffer: 15% = 1.65h
- **Total: 11 + 2.2 + 1.65 = ~15 hours**

**Estimated Effort:** 15 hours (1.9 ngày)

---

### Story 16.3: Stock Adjustment via Scan UI

As a **Warehouse Staff**,
I want **điều chỉnh tồn kho qua Scan UI**,
So that **xử lý chênh lệch kiểm kê**.

**Acceptance Criteria:**
- **Given** Adjustment request trong StockMovement App
- **When** warehouse staff scan via Scan UI
- **Then** hiển thị adjustment request
- **And** quét kệ → quét sản phẩm/LOT → nhập số lượng thực tế
- **And** xác nhận → điều chỉnh tồn vật lý và logic (±Qty)
- **And** ghi audit log với reason

**Prerequisites:** Story 4.5 (StockMovement App), Story 4.1 (Scan UI)

**Technical Notes:**
- Integration với Scan UI
- Display adjustment requests
- Barcode scanning
- Update Physical Stock DB (±adjustment)
- Update Stock App (±adjustment)
- Audit logging với reason

**Tasks chi tiết:**
- Research adjustment flow: 1.5h
- Implement display adjustment requests trong Scan UI: 2h
- Implement barcode scanning cho adjustment: 2h
- Implement update Physical Stock DB (±adjustment): 2h
- Implement update Stock App (±adjustment): 1.5h
- Implement audit logging: 1.5h
- Testing: 1.5h

**Estimated Effort:** 12 hours (base)
**Adjusted:** Medium task - Scan UI + adjustment logic → +35%
- Research: 20% = 2.4h
- Buffer: 15% = 1.8h
- **Total: 12 + 2.4 + 1.8 = ~16 hours**

**Estimated Effort:** 16 hours (2 ngày)

---

**Epic 16 Total:** 47 hours (5.9 ngày)

---

## Epic 17: System Administration

**Goal:** Quản lý Staffs và Error Log Apps.

### Story 17.1: Staffs App Setup

As a **System Administrator**,
I want **Staffs App được setup**,
So that **quản lý người dùng nội bộ và quyền**.

**Acceptance Criteria:**
- **Given** Kintone account
- **When** setup Staffs App
- **Then** có Staffs App với fields: staff_id, name, role, department, permissions, groups
- **And** link với audit log
- **And** support role-based access control

**Prerequisites:** Story 1.1

**Technical Notes:**
- Staffs App trong Kintone
- Role management (Sales, Warehouse, Finance, Admin)
- Permission groups
- Link với audit log

**Tasks chi tiết:**
- Research Staffs App setup: 2h
- Setup Staffs App trong Kintone: 2h
- Implement role management: 2h
- Implement permission groups: 1.5h
- Implement link với audit log: 1h
- Testing: 1h

**Estimated Effort:** 9.5 hours (base)
**Adjusted:** Medium task - Kintone App + permissions → +35%
- Research: 20% = 1.9h
- Buffer: 15% = 1.4h
- **Total: 9.5 + 1.9 + 1.4 = ~13 hours**

**Estimated Effort:** 13 hours (1.6 ngày)

---

### Story 17.2: Error Log App Setup

As a **System Administrator**,
I want **Error Log App được setup**,
So that **theo dõi exceptions và errors**.

**Acceptance Criteria:**
- **Given** Kintone account
- **When** setup Error Log App
- **Then** có Error Log App với fields: error_id, timestamp, source (webhook/API/EDI), error_type, message, status, retry_count
- **And** track DLQ và retry status
- **And** support filtering và search

**Prerequisites:** Story 1.1

**Technical Notes:**
- Error Log App trong Kintone
- Track errors từ webhook, API, EDI
- DLQ tracking
- Retry status
- Filtering và search

**Tasks chi tiết:**
- Research Error Log App setup: 2h
- Setup Error Log App trong Kintone: 2h
- Implement error tracking: 2h
- Implement DLQ tracking: 1.5h
- Implement retry status: 1.5h
- Implement filtering/search: 1h
- Testing: 1h

**Estimated Effort:** 11 hours (base)
**Adjusted:** Medium task - Kintone App + error tracking → +35%
- Research: 20% = 2.2h
- Buffer: 15% = 1.65h
- **Total: 11 + 2.2 + 1.65 = ~15 hours**

**Estimated Effort:** 15 hours (1.9 ngày)

---

**Epic 17 Total:** 28 hours (3.5 ngày)

---

## Tổng kết Estimate (Epic 7-17: Growth Phase)

| Epic | Stories | Base Hours | Adjusted Hours | Days |
| ---- | ------- | ---------- | -------------- | ---- |
| Epic 7: B2B Portal | 4 | 46 | 65 | 8.1 |
| Epic 8: Procurement | 4 | 52 | 72 | 9 |
| Epic 9: AR/AP Management | 3 | 41.5 | 60 | 7.5 |
| Epic 10: Agency Rank | 2 | 27.5 | 38 | 4.75 |
| Epic 11: Affiliate | 4 | 57 | 81 | 10.1 |
| Epic 12: Rewards & Dividends | 3 | 47 | 67 | 8.4 |
| Epic 13: Dashboard | 3 | 49.5 | 70 | 8.75 |
| Epic 14: Sample Outflow | 1 | 15 | 21 | 2.6 |
| Epic 15: Production & Materials | 0 | 0 | 0 | 0 |
| Epic 16: Return/Move/Adjustment | 3 | 34.5 | 47 | 5.9 |
| Epic 17: System Administration | 2 | 20.5 | 28 | 3.5 |
| **Growth Total** | **29** | **381.5** | **549** | **68.6** |

**Growth Timeline:** ~15-19 tuần (1 developer) hoặc ~7.5-9.5 tuần (2 developers)

---

## Tổng kết Estimate Toàn bộ Dự án

| Phase | Epics | Stories | Base Hours | Adjusted Hours | Days | Weeks (1 dev) | Weeks (2 devs) |
| ----- | ----- | ------- | ---------- | -------------- | ---- | ------------ | -------------- |
| **MVP (Epic 1-6)** | 6 | 28 | 318.5 | 470.5 | 58.8 | 11-14 | 5.5-7 |
| **Growth (Epic 7-17)** | 11 | 29 | 381.5 | 549 | 68.6 | 14-18 | 7-9 |
| **TOTAL** | **17** | **57** | **700** | **1019** | **127.4** | **25-32** | **12.5-16** |

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

