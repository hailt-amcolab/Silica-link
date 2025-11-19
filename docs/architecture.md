# Silica-link - Architecture Document

**Author:** Potato  
**Date:** 2025-11-19  
**Version:** 1.0

---

## Executive Summary

Silica-link là **integration layer thông minh** tự động hóa toàn bộ chuỗi giá trị **Đặt hàng → Giao hàng → Nhập hàng → Quản lý hoa hồng/cổ tức** cho CREDIE. Hệ thống tích hợp seamless với các hệ thống hiện có (Kintone, Shopify, Ship&Co, GMO Bank) thông qua webhook pipeline realtime, đảm bảo zero duplicate orders và tăng năng suất operations team.

**Architectural Approach:**
- **Brownfield Integration:** Tích hợp với Kintone apps hiện có, không rebuild
- **Event-Driven Architecture:** Webhook pipeline với idempotency-first design
- **Separation of Concerns:** Rails = backend processing, Kintone = frontend display
- **Realtime Everything:** Inventory sync, order processing, dashboard updates

---

## Project Context

### Project Scale

- **Functional Requirements:** 113 FRs
- **Non-Functional Requirements:** 39 NFRs
- **Complexity:** Medium (multiple integrations, complex business logic, realtime requirements)
- **Project Type:** SaaS B2B Platform (brownfield integration)

### Key Technical Challenges

1. **Webhook Reliability & Idempotency**
   - Zero duplicate orders requirement
   - At-least-once delivery guarantee
   - Research recommendation: Rails + Sidekiq + Redis

2. **Realtime Inventory Sync**
   - Shopify ↔ Kintone inventory sync
   - Multi-warehouse support (Amagasaki, Gifu, Marusho)
   - Location-level tracking (棚)

3. **Multi-Channel Order Routing**
   - BtoC: No backorder (hard stop khi hết hàng)
   - BtoB: Partial shipment + prepayment required
   - FIS shop: Separate fulfillment flow

4. **Payment Reconciliation Automation**
   - GMO Bank API integration
   - Auto match payments với orders
   - Realtime reconciliation

### Technology Stack (From Research)

**Core Stack:**
- **Backend:** Ruby on Rails
- **Job Queue:** Sidekiq
- **Cache/Idempotency:** Redis
- **Database:** MySQL
- **Frontend (BtoB Portal):** Hotwire (Turbo + Stimulus)

**Integrations:**
- **Kintone:** REST API (Orders, Stock, Rewards apps)
- **Shopify:** Webhook API
- **Yoom:** Webhook gateway/orchestrator
- **Ship&Co:** API (shipping labels)
- **GMO Bank:** API (payment reconciliation)
- **AWS S3:** Audit log storage

---

## Decision Summary

| Category | Decision | Version | Affects FR Categories | Rationale |
| -------- | -------- | ------- | --------------------- | --------- |
| Dashboard Approach | Kintone Native (100% Kintone) | - | Dashboard (FR32-FR38) | Operations team familiarity, native realtime updates, no Rails API overhead |
| Webhook Pipeline | Rails + Sidekiq + Redis | Latest stable | Order Processing (FR1-FR15) | Idempotency, retry, DLQ, proven pattern |
| Data Persistence | MySQL (Rails) + Kintone Apps | - | All | Brownfield integration, Kintone là source of truth |
| Frontend (BtoB Portal) | Hotwire (Turbo + Stimulus) | Latest | BtoB Portal (FR19-FR31) | Modern Rails approach, minimal JavaScript |
| API Pattern | REST (Kintone API, Shopify Webhooks) | - | All integrations | Standard pattern, Kintone native support |

---

## Architecture Decisions

### Decision 1: Webhook Pipeline Architecture

**Decision:** Rails + Sidekiq + Redis với Idempotency Pattern

**Rationale:**
- Familiar stack (Rails, Redis đã có sẵn)
- Sidekiq là battle-tested solution
- Cost effective (không cần thêm AWS services)
- Easy debugging (Rails logs + Sidekiq web UI)

**Implementation:**
- Webhook reception: Rails controller với HMAC verification
- Idempotency: Redis-based với Shopify webhook ID
- Job processing: Sidekiq workers với retry mechanism
- DLQ: Sidekiq dead job queue cho manual review

**Affects:** FR1-FR15 (Order Processing)

---

### Decision 2: Data Persistence Strategy

**Decision:** Hybrid - MySQL (Rails) + Kintone Apps

**Rationale:**
- **Kintone Apps:** Source of truth cho business data (Orders, Stock, Rewards)
- **MySQL (Rails):** Temporary storage cho webhook processing, idempotency tracking, job state
- **Separation:** Rails sync data lên Kintone, Kintone là persistent storage

**Data Flow:**
```
Webhook → Rails (MySQL: temporary) → Process → Sync → Kintone Apps (persistent)
```

**Affects:** All FRs

---

### Decision 3: Frontend Architecture (BtoB Portal)

**Decision:** Hotwire (Turbo + Stimulus)

**Rationale:**
- Modern Rails approach với minimal JavaScript
- Server-rendered HTML với realtime updates
- Mobile-friendly
- Familiar cho Rails developers

**Implementation:**
- Turbo: Real-time updates, form submissions
- Stimulus: Interactive UI components
- Separate from Kintone dashboard (Decision 5)

**Affects:** FR19-FR31 (BtoB Portal)

---

### Decision 4: Integration Layer Pattern

**Decision:** Service Layer + Adapter Pattern

**Rationale:**
- **Service Layer:** Business logic abstraction
- **Adapter Pattern:** Abstract external APIs (Kintone, Ship&Co, GMO Bank)
- Easy to test và maintain
- Handle API versioning và changes

**Structure:**
```
app/
  services/
    order_processor.rb
    inventory_sync.rb
    rebate_calculator.rb
  adapters/
    kintone_adapter.rb
    shopify_adapter.rb
    shipco_adapter.rb
    gmo_adapter.rb
```

**Affects:** All integration FRs

---

### Decision 5: Dashboard Approach

**Decision:** Kintone Native Dashboard (100% Kintone)

**Rationale:**
- Operations team đã quen với Kintone interface
- Kintone có native dashboard capabilities (krewDashboard plugin)
- Realtime updates tự động khi data changes
- No Rails API overhead
- Clear separation: Rails = backend, Kintone = frontend

**Implementation:**
- Rails sync data lên Kintone apps
- Kintone dashboard aggregate data từ apps
- No Rails involvement trong dashboard data flow

**Affects:** FR32-FR38 (Dashboard)

**Reference:** `docs/architecture-decision-5-kintone-dashboard.md`

---

### Decision 6: Event-Driven Architecture

**Decision:** Webhook-based Event Processing với Sidekiq

**Rationale:**
- Realtime processing requirements
- At-least-once delivery guarantee
- Idempotency critical
- Queue-based để handle spikes

**Flow:**
```
Shopify → Yoom → Rails Webhook Controller → Sidekiq Job → Process → Sync Kintone
```

**Affects:** FR1-FR15, FR39-FR55 (Order & Fulfillment)

---

### Decision 7: Warehouse Management Data Model

**Decision:** Physical Inventory trong MySQL (Rails), Logical Inventory trong Kintone

**Rationale:**
- **Physical Inventory (MySQL):** Location-level tracking (棚), LOT management, realtime operations
- **Logical Inventory (Kintone):** Business inventory (論理在庫), reporting, dashboard
- Rails sync physical → logical khi có changes

**Data Flow:**
```
Scan Barcode → Rails (MySQL: physical) → Process → Sync → Kintone (logical)
```

**Affects:** FR56-FR73 (Warehouse Management)

---

### Decision 8: Authentication & Authorization

**Decision:** 
- **BtoB Portal:** Rails Devise hoặc custom authentication
- **Kintone:** Native Kintone permissions
- **API:** API keys hoặc OAuth tokens

**Rationale:**
- Separate authentication cho BtoB Portal
- Kintone handles internal user permissions
- API keys cho external integrations

**Affects:** FR19-FR31 (BtoB Portal), NFR20 (Security)

---

## Project Structure

```
silica-link/
├── app/
│   ├── controllers/
│   │   ├── webhooks/
│   │   │   ├── shopify_controller.rb
│   │   │   └── yoom_controller.rb
│   │   ├── api/
│   │   │   └── v1/
│   │   └── btob/
│   │       └── orders_controller.rb
│   ├── models/
│   │   ├── order.rb
│   │   ├── webhook_event.rb
│   │   ├── inventory_movement.rb
│   │   └── rebate_ledger.rb
│   ├── services/
│   │   ├── order_processor.rb
│   │   ├── inventory_sync.rb
│   │   ├── rebate_calculator.rb
│   │   └── shipment_processor.rb
│   ├── adapters/
│   │   ├── kintone_adapter.rb
│   │   ├── shopify_adapter.rb
│   │   ├── shipco_adapter.rb
│   │   └── gmo_adapter.rb
│   ├── jobs/
│   │   ├── process_webhook_job.rb
│   │   ├── sync_to_kintone_job.rb
│   │   └── reconcile_payment_job.rb
│   ├── workers/
│   │   └── (Sidekiq workers)
│   └── views/
│       └── btob/
│           └── (Hotwire views)
├── config/
│   ├── routes.rb
│   ├── sidekiq.yml
│   └── initializers/
│       ├── kintone.rb
│       └── redis.rb
├── db/
│   ├── migrate/
│   └── schema.rb
├── lib/
│   └── tasks/
├── spec/
│   ├── models/
│   ├── services/
│   ├── adapters/
│   └── jobs/
├── docs/
│   ├── prd.md
│   ├── architecture.md
│   └── architecture-decision-5-kintone-dashboard.md
└── initial-docs/
    └── (existing documentation)
```

---

## Epic to Architecture Mapping

**Note:** Epics chưa được tạo, mapping dựa trên FR categories từ PRD.

| FR Category | Architecture Component | Location |
| ---------- | ---------------------- | -------- |
| Order Processing (FR1-FR15) | Webhook Controller, Order Processor Service | `app/controllers/webhooks/`, `app/services/order_processor.rb` |
| Fulfillment (FR16-FR18) | Shipment Processor Service, Ship&Co Adapter | `app/services/shipment_processor.rb`, `app/adapters/shipco_adapter.rb` |
| BtoB Portal (FR19-FR31) | BtoB Controllers, Hotwire Views | `app/controllers/btob/`, `app/views/btob/` |
| Dashboard (FR32-FR38) | Kintone Dashboard (100% Kintone) | Kintone platform |
| Warehouse Management (FR56-FR73) | Inventory Models, Warehouse Services | `app/models/`, `app/services/inventory_sync.rb` |
| Financial (FR74-FR91) | Rebate Calculator, GMO Adapter | `app/services/rebate_calculator.rb`, `app/adapters/gmo_adapter.rb` |

---

## Technology Stack Details

### Core Technologies

**Backend Framework:**
- **Ruby on Rails:** Latest stable version
- **Sidekiq:** Job queue processing
- **Redis:** Caching và idempotency tracking
- **MySQL:** Temporary data storage

**Frontend:**
- **Hotwire (Turbo + Stimulus):** BtoB Portal
- **Kintone Dashboard:** Native Kintone (krewDashboard plugin)

**Infrastructure:**
- **AWS S3:** Audit log storage
- **AWS EC2:** Application hosting

### Integration Points

**Kintone API:**
- REST API v1
- Rate limiting: NFR26
- Authentication: API token
- Apps: Orders, Stock, Stock Movements, Rewards, Tasks, Error Log

**Shopify Webhooks:**
- Events: orders/create, orders/updated, orders/paid, orders/cancelled, orders/fulfilled
- HMAC SHA256 signature verification
- Idempotency: Shopify webhook ID

**Ship&Co API:**
- Shipping label generation
- Tracking status sync

**GMO Bank API:**
- Payment reconciliation
- Transaction matching

---

## Implementation Patterns

### Webhook Processing Pattern

```ruby
# 1. Receive webhook
class Webhooks::ShopifyController < ApplicationController
  def create
    # Verify HMAC signature
    # Check idempotency (Redis)
    # Enqueue job
    ProcessWebhookJob.perform_async(webhook_params)
  end
end

# 2. Process in background
class ProcessWebhookJob < ApplicationJob
  def perform(webhook_data)
    # Process order
    # Sync to Kintone
    # Handle errors
  end
end
```

### Idempotency Pattern

```ruby
# Redis-based idempotency
class IdempotencyService
  def self.process_once(key, &block)
    return if Redis.exists("idempotency:#{key}")
    
    Redis.setex("idempotency:#{key}", 86400, "processing")
    result = yield
    Redis.setex("idempotency:#{key}", 86400, "completed")
    result
  end
end
```

### Service Layer Pattern

```ruby
# Business logic abstraction
class OrderProcessor
  def initialize(order_data)
    @order_data = order_data
  end

  def process
    validate_order
    check_inventory
    create_order_in_kintone
    trigger_downstream_processes
  end
end
```

### Adapter Pattern

```ruby
# Abstract external APIs
class KintoneAdapter
  def create_order(order)
    # Kintone API call
  end

  def update_inventory(sku, quantity)
    # Kintone API call
  end
end
```

---

## Consistency Rules

### Naming Conventions

- **Controllers:** `Webhooks::ShopifyController`, `BtoB::OrdersController`
- **Services:** `OrderProcessor`, `InventorySync`, `RebateCalculator`
- **Adapters:** `KintoneAdapter`, `ShopifyAdapter`, `ShipCoAdapter`
- **Jobs:** `ProcessWebhookJob`, `SyncToKintoneJob`
- **Models:** `Order`, `WebhookEvent`, `InventoryMovement`

### Code Organization

- **Services:** Business logic, orchestration
- **Adapters:** External API integration
- **Jobs:** Background processing
- **Models:** Data persistence (MySQL)
- **Controllers:** Request handling

### Error Handling

- **Standardized Error Classes:** `OrderProcessingError`, `InventorySyncError`
- **Error Logging:** Structured logging với context
- **DLQ:** Failed jobs → Sidekiq dead queue → Manual review
- **Alerting:** CloudWatch alerts cho critical errors

### Logging Strategy

- **Structured Logging:** JSON format với context
- **Audit Trail:** All events → S3 (ai/khi nào/cái gì/bao nhiêu/ở đâu)
- **Log Levels:** DEBUG, INFO, WARN, ERROR
- **Correlation IDs:** Track requests across services

---

## Data Architecture

### Rails Models (MySQL)

**Order:**
- Temporary storage cho webhook processing
- Sync to Kintone sau khi process

**WebhookEvent:**
- Track processed webhooks
- Idempotency key storage

**InventoryMovement:**
- Physical inventory movements
- Location-level tracking (棚)

### Kintone Apps (Source of Truth)

**Orders App:**
- Order data (persistent)
- Status tracking
- Customer information

**Stock App:**
- Logical inventory (論理在庫)
- SKU-level tracking

**Stock Movements App:**
- Movement history
- Audit trail

**Rewards App:**
- Rebate/commission data
- Quarterly summaries

---

## API Contracts

### Webhook Endpoints

**POST /webhooks/shopify**
- HMAC signature verification
- Idempotency check
- Enqueue processing job

**POST /webhooks/yoom**
- Gateway webhook reception
- Similar to Shopify endpoint

### BtoB Portal API

**GET /api/v1/orders**
- List orders for agent
- Authentication required

**POST /api/v1/orders**
- Create new order
- Validation required

---

## Security Architecture

- **HMAC Verification:** Mandatory cho all webhooks (NFR16)
- **API Authentication:** API keys hoặc OAuth tokens
- **Rate Limiting:** Prevent abuse (NFR18)
- **PII Encryption:** Sensitive data trong audit logs (NFR17)
- **IP Whitelist:** Shopify webhook IPs (if possible) (NFR19)

---

## Performance Considerations

- **Webhook Processing:** < 2 seconds (p95) (NFR1)
- **Dashboard Refresh:** < 1 second (NFR2)
- **Inventory Sync:** < 500ms (NFR5)
- **Horizontal Scaling:** Multiple Rails workers (NFR11)
- **Caching:** Redis caching cho inventory data (NFR15)

---

## Deployment Architecture

- **Application:** AWS EC2
- **Database:** MySQL (RDS hoặc EC2)
- **Cache/Queue:** Redis (ElastiCache hoặc EC2)
- **Storage:** AWS S3 (audit logs)
- **Monitoring:** CloudWatch metrics và alerts

---

## Development Environment

### Prerequisites

- Ruby (latest stable)
- Rails (latest stable)
- MySQL
- Redis
- Sidekiq

### Setup Commands

```bash
# Install dependencies
bundle install

# Setup database
rails db:create
rails db:migrate

# Start services
redis-server
sidekiq
rails server
```

---

## Architecture Decision Records (ADRs)

1. **Decision 5: Kintone Dashboard Approach**
   - Document: `docs/architecture-decision-5-kintone-dashboard.md`
   - Status: Approved
   - Summary: Dashboard 100% Kintone, no Rails involvement

---

## Advanced Elicitation Analysis

Phần này tổng hợp insights từ 5 phương pháp Advanced Elicitation được áp dụng để validate và enhance architecture:

### 1. Pre-mortem Analysis

**Scenario:** Architecture thất bại sau 6 tháng triển khai - hệ thống không đáp ứng được requirements.

**Contributing Factors:**
- **Redis Single Point of Failure:** Idempotency tracking phụ thuộc hoàn toàn vào Redis, nếu Redis down → không thể process webhooks
- **Kintone API Rate Limits:** Không có proper throttling → API calls bị reject → sync failures
- **Data Consistency Gap:** Physical inventory (MySQL) và logical inventory (Kintone) không sync realtime → inventory discrepancies
- **No Fallback Mechanism:** Nếu Kintone API down → không có cách nào process orders
- **Sidekiq Queue Overflow:** Peak load → queue backlog → webhooks timeout → Shopify retry → duplicate processing

**Warning Signs:**
- Redis connection errors > 1% of requests
- Kintone API rate limit errors
- Inventory sync lag > 5 seconds
- Sidekiq queue depth > 1000 jobs
- Failed webhook processing rate > 0.1%

**Preventive Measures (Architecture Enhancements):**
- **Idempotency Redundancy:** Database fallback cho idempotency tracking (NFR10)
- **Circuit Breakers:** Implement circuit breakers cho tất cả external APIs (Kintone, Ship&Co, GMO Bank)
- **Rate Limiting:** Queue throttling cho Kintone API với exponential backoff (NFR26)
- **Realtime Sync:** Event-driven sync với immediate updates, không batch
- **Queue Monitoring:** Auto-scaling Sidekiq workers based on queue depth
- **Health Checks:** Automated health checks cho tất cả integrations với alerting

---

### 2. Red Team Analysis

**Adversarial Perspective:** Attacker muốn exploit hệ thống để tạo duplicate orders, steal data, hoặc disrupt operations.

**Attack Vectors:**

1. **Webhook Replay Attack:**
   - Attacker capture webhook payload → replay nhiều lần
   - **Vulnerability:** Idempotency key có thể bị bypass nếu Redis down
   - **Countermeasure:** Database fallback cho idempotency, HMAC verification mandatory

2. **API Key Theft:**
   - Attacker steal Kintone API key → access sensitive data
   - **Vulnerability:** API keys stored in plaintext config
   - **Countermeasure:** Encrypt API keys, use environment variables, rotate keys regularly

3. **Inventory Manipulation:**
   - Attacker manipulate inventory sync → overselling
   - **Vulnerability:** No validation layer giữa physical và logical inventory
   - **Countermeasure:** Validation layer, audit trail cho mọi inventory changes

4. **Queue Poisoning:**
   - Attacker send malformed webhooks → poison Sidekiq queue
   - **Vulnerability:** No input validation trước khi enqueue
   - **Countermeasure:** Validate webhook payload structure trước khi enqueue, DLQ cho malformed payloads

5. **Rate Limit Bypass:**
   - Attacker bypass rate limiting → overwhelm Kintone API
   - **Vulnerability:** Rate limiting chỉ ở Rails, không ở Kintone side
   - **Countermeasure:** Queue throttling với exponential backoff, circuit breakers

**Security Enhancements:**
- **Input Validation:** Validate tất cả webhook payloads trước khi process
- **API Key Management:** Encrypt API keys, rotation policy
- **Audit Trail:** Log tất cả API calls với correlation IDs
- **Rate Limiting:** Multi-layer rate limiting (Rails + Kintone)
- **Monitoring:** Alert on suspicious patterns (unusual webhook volume, API errors)

---

### 3. Decision Matrix Analysis

**Evaluation of Key Architectural Decisions:**

#### Decision: Webhook Pipeline (Rails + Sidekiq + Redis vs Alternatives)

| Criteria | Weight | Rails+Sidekiq+Redis | AWS SQS + Lambda | RabbitMQ + Workers |
|----------|--------|-------------------|------------------|---------------------|
| **Familiarity** | 20% | 9 (familiar stack) | 4 (new AWS services) | 5 (new message broker) |
| **Cost** | 15% | 9 (existing infra) | 5 (AWS costs) | 7 (self-hosted) |
| **Idempotency** | 25% | 8 (Redis-based) | 9 (SQS built-in) | 7 (manual) |
| **Debugging** | 15% | 9 (Sidekiq web UI) | 6 (CloudWatch logs) | 7 (RabbitMQ UI) |
| **Scalability** | 15% | 8 (horizontal scaling) | 9 (auto-scaling) | 8 (horizontal scaling) |
| **Maintenance** | 10% | 8 (familiar) | 7 (AWS managed) | 6 (self-managed) |
| **Total Score** | 100% | **8.35** | **6.65** | **6.75** |

**Conclusion:** Rails + Sidekiq + Redis là best choice với highest score.

#### Decision: Dashboard Approach (Kintone Native vs Rails API + Frontend)

| Criteria | Weight | Kintone Native | Rails API + React | Rails API + Hotwire |
|----------|--------|----------------|-------------------|---------------------|
| **User Familiarity** | 25% | 10 (operations team quen) | 3 (new interface) | 5 (new but Rails) |
| **Development Speed** | 20% | 10 (no development) | 4 (full stack) | 7 (Rails only) |
| **Realtime Updates** | 15% | 9 (native) | 8 (WebSocket) | 8 (Turbo) |
| **Maintenance** | 15% | 10 (Kintone handles) | 5 (full stack) | 7 (Rails only) |
| **Customization** | 10% | 5 (limited) | 10 (full control) | 8 (Rails patterns) |
| **Cost** | 10% | 10 (no additional) | 5 (hosting) | 7 (hosting) |
| **Data Consistency** | 5% | 10 (direct from apps) | 6 (API sync) | 6 (API sync) |
| **Total Score** | 100% | **9.15** | **5.45** | **6.90** |

**Conclusion:** Kintone Native là best choice với highest score.

---

### 4. Risk Matrix

**Risk Assessment và Prioritization:**

| Risk | Probability | Impact | Risk Score | Priority | Mitigation Strategy |
|------|-------------|--------|------------|----------|-------------------|
| **Redis Single Point of Failure** | Medium | High | **12** | **HIGH** | Database fallback cho idempotency, Redis cluster |
| **Kintone API Rate Limits** | High | Medium | **12** | **HIGH** | Queue throttling, exponential backoff, circuit breakers |
| **Inventory Sync Lag** | Medium | High | **12** | **HIGH** | Realtime event-driven sync, validation layer |
| **Webhook Duplicate Processing** | Low | High | **8** | **MEDIUM** | Idempotency với database fallback, HMAC verification |
| **Sidekiq Queue Overflow** | Medium | Medium | **9** | **MEDIUM** | Auto-scaling workers, priority queues, monitoring |
| **Data Loss (Webhook)** | Low | High | **8** | **MEDIUM** | Store webhook trước khi process, S3 backup |
| **Kintone API Downtime** | Low | High | **8** | **MEDIUM** | Circuit breakers, retry mechanism, manual fallback |
| **Security Breach (API Keys)** | Low | High | **8** | **MEDIUM** | Encrypt API keys, rotation policy, audit trail |
| **BtoB Portal Adoption Failure** | Medium | Medium | **9** | **MEDIUM** | Phased rollout, training, UX focus |
| **Performance Degradation** | Medium | Low | **6** | **LOW** | Horizontal scaling, caching, optimization |

**Top 3 Risks cần address ngay:**
1. **Redis Single Point of Failure** - Implement database fallback
2. **Kintone API Rate Limits** - Implement queue throttling
3. **Inventory Sync Lag** - Implement realtime sync

---

### 5. Devil's Advocate Analysis

**Challenging Key Assumptions:**

#### Assumption 1: "Kintone là Source of Truth"

**Counter-argument:**
- Kintone có thể down → không thể access data
- Kintone API có rate limits → không thể sync realtime
- Kintone không có transaction support → data inconsistency risk

**Strengthened Proposal:**
- **Hybrid Approach:** MySQL là primary storage, Kintone là sync target
- **Fallback Mechanism:** Nếu Kintone down → store trong MySQL, sync later
- **Validation Layer:** Validate data consistency giữa MySQL và Kintone

#### Assumption 2: "Rails chỉ sync data, không serve dashboard"

**Counter-argument:**
- Kintone dashboard có limitations → không thể customize
- Operations team có thể cần advanced analytics → Kintone không support
- Dashboard performance phụ thuộc vào Kintone → không control được

**Strengthened Proposal:**
- **Hybrid Dashboard:** Kintone cho basic dashboard, Rails API cho advanced analytics (nếu cần)
- **Future-proofing:** Architecture support cả 2 approaches, không lock-in

#### Assumption 3: "Sidekiq + Redis là đủ cho idempotency"

**Counter-argument:**
- Redis có thể down → mất idempotency tracking
- Redis memory limit → có thể evict keys → duplicate processing
- No persistence → restart mất state

**Strengthened Proposal:**
- **Database Fallback:** Store idempotency keys trong MySQL as backup
- **Dual Tracking:** Redis (fast) + MySQL (persistent) cho idempotency
- **Monitoring:** Alert khi Redis down, switch to database fallback

#### Assumption 4: "Physical Inventory (MySQL) + Logical Inventory (Kintone) là đủ"

**Counter-argument:**
- Sync lag giữa physical và logical → inventory discrepancies
- No single source of truth → confusion
- Complex sync logic → error-prone

**Strengthened Proposal:**
- **Realtime Sync:** Event-driven sync với immediate updates
- **Validation Layer:** Validate consistency, alert on discrepancies
- **Reconciliation Job:** Daily reconciliation job để fix discrepancies

#### Assumption 5: "Hotwire là đủ cho BtoB Portal"

**Counter-argument:**
- Hotwire có limitations cho complex interactions
- Mobile experience có thể không optimal
- SEO không tốt (server-rendered)

**Strengthened Proposal:**
- **Progressive Enhancement:** Start với Hotwire, enhance với Stimulus khi cần
- **Mobile-first Design:** Ensure mobile-friendly từ đầu
- **Future Option:** Architecture support thêm React nếu cần (không lock-in)

---

## Architecture Enhancements Based on Elicitation

### Critical Enhancements (Must Have)

1. **Idempotency Redundancy:**
   - Database fallback cho idempotency tracking (MySQL)
   - Dual tracking: Redis (fast) + MySQL (persistent)
   - Monitoring và alerting khi Redis down

2. **Circuit Breakers:**
   - Implement cho tất cả external APIs (Kintone, Ship&Co, GMO Bank)
   - Auto-recovery mechanism
   - Fallback strategies

3. **Queue Throttling:**
   - Rate limiting cho Kintone API với exponential backoff
   - Priority queues cho critical jobs
   - Auto-scaling Sidekiq workers based on queue depth

4. **Realtime Sync:**
   - Event-driven sync với immediate updates
   - No batch processing cho critical data
   - Validation layer cho consistency

5. **Security Hardening:**
   - Encrypt API keys
   - Input validation cho tất cả webhooks
   - Audit trail với correlation IDs
   - Multi-layer rate limiting

### Recommended Enhancements (Should Have)

1. **Health Checks:**
   - Automated health checks cho tất cả integrations
   - Alerting on failures
   - Dashboard cho system health

2. **Monitoring & Alerting:**
   - CloudWatch metrics cho critical paths
   - Alert on suspicious patterns
   - Queue depth monitoring

3. **Data Consistency Validation:**
   - Daily reconciliation jobs
   - Alert on discrepancies
   - Manual review interface

4. **Fallback Mechanisms:**
   - Manual fallback cho critical operations
   - Graceful degradation khi integrations down
   - Data recovery procedures

---

## Next Steps

1. **Validate Architecture:** Review với team và stakeholders
2. **Create Epics & Stories:** Break down FRs thành implementable stories
3. **Setup Development Environment:** Initialize Rails project với stack
4. **Implement MVP:** Start với Phase 1 (Order Processing Automation)

---

**Document Status:** Enhanced with Advanced Elicitation  
**Last Updated:** 2025-11-19

