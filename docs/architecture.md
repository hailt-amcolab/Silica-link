# Architecture - Silica-link

## Executive Summary

Silica-link là nền tảng tích hợp tự động hóa quy trình Đặt hàng – Giao hàng – Nhập hàng – Quản lý hoa hồng/cổ tức với Kintone làm Database & Workflow trung tâm. Hệ thống tích hợp với Yoom, Ship&Co, WMS, GMO Bank API thông qua Ruby on Rails middleware, sử dụng Redis + Sidekiq cho queue processing và ReactJS cho Warehouse Scan UI.

**Kiến trúc chính:**
- **Kintone**: Single source of truth cho business data và workflow
- **Ruby on Rails**: Middleware orchestrator cho integrations
- **Redis + Sidekiq**: Queue system cho async processing
- **ReactJS**: Frontend cho Warehouse Scan UI
- **AWS**: Lambda (batch), S3 (storage), CloudWatch (monitoring)

---

## Decision Summary

| Category | Decision | Version | Affects Epics | Rationale |
| -------- | -------- | ------- | ------------- | --------- |
| **Core Platform** | Kintone | Latest | All | Đã có sẵn, phù hợp workflow, cost-effective |
| **Backend Framework** | Ruby on Rails | 7.x | All | Team đã quen, ecosystem tốt, dễ maintain |
| **Queue System** | Redis + Sidekiq | Latest | All async operations | Reliable, battle-tested, good Rails integration |
| **Frontend Framework** | ReactJS | 18.x | Warehouse Scan UI | Modern, component-based, good barcode scanning support |
| **Cloud Provider** | AWS | - | Infrastructure | Lambda, S3, CloudWatch, IAM integration |
| **Integration Pattern** | Webhook + Batch | - | EC Order, Payment | Webhook primary, batch fallback for reliability |
| **Idempotency** | Key-based | - | All integrations | Prevent duplicates, safe retries |
| **Error Handling** | DLQ + Retry | - | All external APIs | Handle failures gracefully, alert on critical issues |
| **Stock Management** | Dual Stock (Logic + Physical) | - | Inventory | Logic for allocation, Physical for actual warehouse |
| **Payment Integration** | GMO Aozora Bank API | Latest | AR/AP | Required by business, supports VA and batch payments |
| **Shipping Integration** | Ship&Co API | Latest | Shipping | Required by business, supports multiple carriers |
| **Authentication** | Kintone Auth | - | Portals | Leverage existing Kintone authentication |
| **Storage** | S3 | - | Files, PDFs, Labels | Scalable, cost-effective, integrates with Kintone |

---

## Project Structure

```
silica-link/
├── ruby-server/              # Rails middleware
│   ├── app/
│   │   ├── controllers/      # API endpoints
│   │   ├── models/           # Business logic
│   │   ├── services/         # Integration services
│   │   │   ├── yoom_service.rb
│   │   │   ├── shipco_service.rb
│   │   │   ├── gmo_service.rb
│   │   │   └── kintone_service.rb
│   │   ├── jobs/             # Sidekiq jobs
│   │   │   ├── order_intake_job.rb
│   │   │   ├── stock_allocation_job.rb
│   │   │   ├── shipping_job.rb
│   │   │   └── payment_reconciliation_job.rb
│   │   └── workers/          # Background workers
│   ├── config/
│   │   ├── routes.rb
│   │   ├── sidekiq.yml
│   │   └── initializers/
│   └── spec/                 # Tests
├── scan-ui/                  # ReactJS Warehouse UI
│   ├── src/
│   │   ├── components/
│   │   │   ├── BarcodeScanner.jsx
│   │   │   ├── TaskList.jsx
│   │   │   └── StockUpdate.jsx
│   │   ├── services/
│   │   │   └── api.js
│   │   └── App.jsx
│   └── package.json
├── aws/                      # Infrastructure as Code
│   ├── lambda/               # Batch functions
│   │   ├── stock_sync/
│   │   ├── payment_reconciliation/
│   │   └── rank_calculation/
│   ├── cloudformation/       # Infrastructure templates
│   └── scripts/              # Deployment scripts
├── docs/                     # Documentation
│   ├── PRD.md
│   ├── architecture.md
│   └── api/                  # API documentation
└── kintone/                  # Kintone app configurations
    ├── orders_app.json
    ├── stock_app.json
    └── ...
```

---

## Epic to Architecture Mapping

| Epic | Components | Integrations | Data Stores |
| ---- | ---------- | ------------ | ----------- |
| **EC Order Intake** | Rails API, Sidekiq Jobs | Yoom (Webhook/Batch) | Kintone Orders App |
| **Inventory Allocation** | Rails Service, Sidekiq Jobs | - | Kintone Stock App (Logic) |
| **Warehouse Scan UI** | ReactJS App, Rails API | - | Kintone Task App, Physical Stock DB |
| **Shipping Integration** | Rails Service, Sidekiq Jobs | Ship&Co API | S3 (Labels), Kintone Orders App |
| **Payment Reconciliation** | Rails Service, Lambda | GMO API | Kintone Invoices App, Exceptions App |
| **B2B Portal** | Kintone Guest Space, Rails API | - | Kintone Orders App, Partners App |
| **Procurement Automation** | Rails Service, Lambda | Email, Supplier Portal | Kintone PO App, Suppliers App |
| **AR/AP Management** | Rails Service, Lambda | GMO API | Kintone Invoices App, Payouts App |
| **Agency Rank** | Rails Service, Lambda | - | Kintone Partners App, Rules App |
| **Affiliate Commission** | Rails Service, Lambda | GoAffPro, Link Shortener | Kintone RewardsLedger App |
| **Rewards & Dividends** | Rails Service, Lambda | GMO API | Kintone RewardsLedger App, Payouts App |
| **Dashboard** | Kintone Dashboard, Rails API | - | All Kintone Apps |

---

## Technology Stack Details

### Core Technologies

**Ruby on Rails 7.x**
- Framework: RESTful API, MVC pattern
- Gems: sidekiq (queue), redis (cache), httparty (HTTP client), aws-sdk-s3 (S3)
- Database: PostgreSQL (for Rails app metadata, not business data)
- Purpose: Middleware orchestrator, API backend, integration services

**Kintone**
- Role: Single source of truth for business data
- Apps: Orders, Stock, Task, PO, Invoice, Payouts, RewardsLedger, Partners, Rules
- Features: Workflow, Webhooks, Guest Spaces, REST API
- Purpose: Business data storage, workflow management, portal hosting

**Redis + Sidekiq**
- Redis: Queue storage, caching, session storage
- Sidekiq: Background job processing
- Purpose: Async processing, retry mechanism, rate limiting

**ReactJS 18.x**
- Framework: Component-based UI
- Libraries: react-barcode-reader (barcode scanning), axios (HTTP client)
- Purpose: Warehouse Scan UI for barcode scanning and task management

**AWS Services**
- Lambda: Batch processing (stock sync, payment reconciliation, rank calculation)
- S3: File storage (PDFs, labels, audit logs)
- CloudWatch: Monitoring, logging, alerts
- IAM: Access control, API keys management
- SQS: Dead Letter Queue (DLQ) for failed jobs

### Integration Points

**Yoom Integration**
- Pattern: Webhook primary + Batch fallback
- Endpoints: `/webhooks/yoom/orders` (POST), batch polling (every 15 min)
- Authentication: API key, webhook signature validation
- Idempotency: `{channel_order_id}_{store_id}`
- Error Handling: Retry 3x, DLQ for failures

**Ship&Co Integration**
- Pattern: REST API with retry
- Endpoints: Create shipping label, get tracking
- Authentication: API key
- Rate Limiting: Queue jobs, respect API limits
- Error Handling: Retry 3x with exponential backoff, DLQ

**GMO Aozora Bank API**
- Pattern: REST API with signature authentication
- Endpoints: `/transactions` (AR), `/payments` (AP)
- Authentication: Signature (HMAC), IP whitelist
- Security: Encrypted credentials, secure storage
- Error Handling: Retry 3x, exception queue for manual review

**WMS Integration**
- Pattern: REST API (if available) or file-based sync
- Purpose: Sync Physical Stock ↔ Logic Stock
- Frequency: Realtime (on scan) + nightly batch sync

**Kintone Integration**
- Pattern: REST API
- Authentication: API token
- Rate Limiting: Respect Kintone API limits
- Webhooks: Kintone → Rails (on approval, task completion)

---

## Implementation Patterns

### Idempotency Pattern

**All operations MUST be idempotent:**
- Use idempotency keys: `{source_id}_{timestamp}` or `{channel_order_id}_{store_id}`
- Check for existing records before creating
- Return existing record if duplicate detected
- Log duplicate attempts for audit

**Example:**
```ruby
def create_order_with_idempotency(order_data)
  idempotency_key = "#{order_data[:channel_order_id]}_#{order_data[:store_id]}"
  
  existing_order = Order.find_by(idempotency_key: idempotency_key)
  return existing_order if existing_order
  
  Order.create(order_data.merge(idempotency_key: idempotency_key))
end
```

### Retry Pattern

**All external API calls MUST have retry:**
- Retry 3 times with exponential backoff
- Use Sidekiq retry mechanism
- Log retry attempts
- Send to DLQ after max retries

**Example:**
```ruby
class ShipCoService
  include Sidekiq::Worker
  
  sidekiq_options retry: 3, backtrace: true
  
  def perform(order_id)
    # API call logic
  rescue => e
    # Log error, Sidekiq will retry automatically
    raise e
  end
end
```

### Queue Pattern

**Use Sidekiq for async processing:**
- Webhook handlers: Enqueue job immediately, return 200 OK
- Batch processes: Schedule periodic jobs
- Long-running operations: Always use background jobs

**Example:**
```ruby
# Webhook handler
post '/webhooks/yoom/orders' do
  OrderIntakeJob.perform_async(params)
  status 200
end

# Background job
class OrderIntakeJob
  include Sidekiq::Worker
  
  def perform(order_data)
    # Process order
  end
end
```

### Error Handling Pattern

**Comprehensive error handling:**
- Catch all exceptions
- Log to CloudWatch
- Send critical errors to DLQ
- Alert on critical failures (email, Slack)

**Example:**
```ruby
def process_order(order_data)
  # Business logic
rescue StandardError => e
  Rails.logger.error("Order processing failed: #{e.message}")
  CloudWatch.log_error(e, context: order_data)
  
  if critical_error?(e)
    AlertService.notify(e, order_data)
    raise e  # Send to DLQ
  end
end
```

### Stock Synchronization Pattern

**Dual Stock Management:**
- Logic Stock (Kintone): For allocation, reporting
- Physical Stock (DB): For actual warehouse operations
- Sync: Physical → Logic (realtime on scan) + nightly batch validation

**Example:**
```ruby
def sync_physical_to_logic(scan_data)
  # Update Physical Stock
  physical_stock = PhysicalStock.find_by(sku: scan_data[:sku], location: scan_data[:location])
  physical_stock.update(quantity: physical_stock.quantity - scan_data[:qty])
  
  # Sync to Logic Stock
  KintoneService.update_stock(scan_data[:sku], scan_data[:qty])
end
```

---

## Consistency Rules

### Naming Conventions

**Files & Classes:**
- Services: `{Service}Service` (e.g., `YoomService`, `ShipCoService`)
- Jobs: `{Action}Job` (e.g., `OrderIntakeJob`, `StockAllocationJob`)
- Models: Singular (e.g., `Order`, `Stock`)
- Controllers: Plural (e.g., `OrdersController`, `StocksController`)

**Variables & Methods:**
- snake_case for variables and methods
- UPPER_CASE for constants
- Descriptive names (avoid abbreviations)

**API Endpoints:**
- RESTful: `/api/v1/{resource}` (e.g., `/api/v1/orders`)
- Webhooks: `/webhooks/{service}/{event}` (e.g., `/webhooks/yoom/orders`)

### Code Organization

**Service Layer Pattern:**
- Business logic in Services (not Controllers)
- Services are testable, reusable
- Controllers are thin (delegate to Services)

**Example:**
```ruby
# Controller (thin)
class OrdersController < ApplicationController
  def create
    order = OrderIntakeService.create(params)
    render json: order
  end
end

# Service (business logic)
class OrderIntakeService
  def self.create(order_data)
    # Validate, transform, create
  end
end
```

### Error Handling

**Consistent error responses:**
```json
{
  "error": {
    "code": "ORDER_NOT_FOUND",
    "message": "Order with ID 123 not found",
    "details": {}
  }
}
```

**Error codes:**
- `VALIDATION_ERROR`: Input validation failed
- `NOT_FOUND`: Resource not found
- `EXTERNAL_API_ERROR`: External API call failed
- `INTERNAL_ERROR`: Unexpected internal error

### Logging Strategy

**Structured logging:**
- Use JSON format for CloudWatch
- Include: timestamp, level, message, context
- Log all external API calls (request/response)
- Log all critical operations (order creation, payment)

**Example:**
```ruby
Rails.logger.info({
  event: "order_created",
  order_id: order.id,
  channel: order.channel,
  timestamp: Time.current
}.to_json)
```

---

## Data Architecture

### Kintone Apps (Primary Data Store)

**Orders App:**
- Fields: order_id, channel, items, customer, shipping, status, tracking_no, invoice_id
- Relationships: → Stock (items), → Invoice (payment), → Task (fulfillment)

**Stock App (Logic Stock):**
- Fields: sku, quantity, reserved_quantity, location
- Purpose: Allocation, reporting, prevent overselling

**Task App:**
- Fields: task_id, order_id, type (pick/putaway/movement), status, assigned_to
- Relationships: → Orders (fulfillment), → Physical Stock (scan results)

**Invoices App (AR):**
- Fields: invoice_id, order_id, va_id, amount, pay_status, paid_date
- Relationships: → Orders (payment), → GMO Transactions (reconciliation)

**Payouts App (AP):**
- Fields: payout_id, supplier_id, amount, status, approval_level, gmo_result
- Relationships: → Suppliers (payment), → GMO Payments (execution)

**RewardsLedger App:**
- Fields: ledger_id, partner_id, type (commission/dividend), amount, period, status
- Relationships: → Partners (recipient), → Payouts (payment)

**Partners App:**
- Fields: partner_id, type (agency/supplier/affiliate), rank, discount_rate, bank_info
- Relationships: → Orders (agency), → RewardsLedger (commissions)

### Rails App Database (PostgreSQL)

**Purpose:** Metadata, job tracking, caching (NOT business data)

**Tables:**
- `sidekiq_jobs`: Job queue (managed by Sidekiq)
- `api_logs`: API call logs for audit
- `error_logs`: Error tracking
- `cache`: Redis cache (via Redis, not DB)

### Physical Stock Database

**Purpose:** Actual warehouse inventory (Shelf × LOT × SKU)

**Structure:**
- `physical_stock`: sku, location (shelf), lot, quantity, last_updated
- Sync with Logic Stock (Kintone) after each scan

---

## API Contracts

### Internal APIs (Rails → Kintone)

**Kintone REST API:**
- Base URL: `https://{subdomain}.cybozu.com/k/v1/`
- Authentication: API Token (header: `X-Cybozu-API-Token`)
- Rate Limit: 10 requests/second per app

**Endpoints:**
- `POST /records.json`: Create records
- `PUT /records.json`: Update records
- `GET /records.json`: Query records
- `DELETE /records.json`: Delete records

### External APIs

**Yoom API:**
- Webhook: `POST /webhooks/yoom/orders`
- Batch: `GET /api/orders?since={timestamp}`
- Authentication: API key

**Ship&Co API:**
- Create Label: `POST /api/v1/labels`
- Get Tracking: `GET /api/v1/tracking/{tracking_no}`
- Authentication: API key

**GMO Aozora Bank API:**
- Transactions: `GET /api/v1/transactions`
- Payments: `POST /api/v1/payments`
- Authentication: Signature (HMAC), IP whitelist

### Scan UI API (ReactJS → Rails)

**Endpoints:**
- `GET /api/v1/tasks/today`: Get today's tasks
- `POST /api/v1/scans`: Submit scan result
- `GET /api/v1/stock/{sku}`: Get stock info

**Authentication:** API key (per warehouse staff)

---

## Security Architecture

### Authentication & Authorization

**Kintone Portals:**
- Use Kintone Guest Space authentication
- Each partner has isolated access (only see their data)

**Rails API:**
- API key authentication for Scan UI
- JWT tokens for internal services (optional)

**External APIs:**
- GMO: Signature authentication (HMAC), IP whitelist
- Yoom: API key, webhook signature validation
- Ship&Co: API key

### Data Protection

**Encryption:**
- Sensitive data encrypted at rest (S3, database)
- API communications use HTTPS/TLS
- Virtual Account IDs stored securely (encrypted)

**Secrets Management:**
- AWS Secrets Manager for API keys
- Environment variables for non-sensitive config
- Never commit secrets to git

### Audit & Compliance

**Audit Trail:**
- Log all critical operations (order creation, payment, stock update)
- Store logs in S3 (long-term retention)
- Log format: JSON with timestamp, user, action, result

**Compliance:**
- Financial transactions logged for audit
- Support audit trail queries
- Data retention policies (7 years for financial data)

---

## Performance Considerations

### Response Time Targets

- Webhook processing: < 5 seconds (p95)
- API endpoints: < 2 seconds (p95)
- Portal page load: < 2 seconds (p95)
- Dashboard refresh: < 3 seconds (p95)

### Throughput Targets

- Order processing: ≥ 100 orders/minute
- Stock check: ≥ 1000 checks/minute
- Payment reconciliation: ≥ 500 transactions/hour

### Optimization Strategies

**Caching:**
- Redis cache for frequently accessed data (product master, stock levels)
- Cache TTL: 5 minutes (stock), 1 hour (product master)

**Database Optimization:**
- Indexes on frequently queried fields (order_id, sku, status)
- Batch operations for bulk updates
- Connection pooling for Kintone API

**Queue Optimization:**
- Multiple Sidekiq workers for parallel processing
- Priority queues (high priority for payments, low for reports)

**API Rate Limiting:**
- Respect external API rate limits (queue jobs if needed)
- Implement exponential backoff for retries

---

## Deployment Architecture

### Infrastructure

**Rails App:**
- Deploy to: AWS EC2 or ECS (containerized)
- Load balancer: ALB (Application Load Balancer)
- Auto-scaling: Based on CPU/memory usage

**Redis:**
- Managed service: AWS ElastiCache (Redis)
- High availability: Multi-AZ deployment

**Lambda Functions:**
- Batch processing: Scheduled (Cron) or event-triggered
- Timeout: 15 minutes max
- Memory: 512 MB - 3 GB (based on workload)

**S3:**
- Buckets: `silica-link-labels`, `silica-link-pdfs`, `silica-link-logs`
- Lifecycle policies: Move old logs to Glacier after 90 days

### CI/CD

**Pipeline:**
1. Code commit → GitHub
2. GitHub Actions: Run tests
3. Build Docker image (if containerized)
4. Deploy to staging
5. Run integration tests
6. Deploy to production (manual approval)

**Environment Variables:**
- Staging: `.env.staging`
- Production: AWS Secrets Manager

---

## Development Environment

### Prerequisites

- Ruby 3.2+
- Rails 7.x
- PostgreSQL 14+
- Redis 7+
- Node.js 18+ (for ReactJS)
- AWS CLI (for deployment)

### Setup Commands

```bash
# Clone repository
git clone https://github.com/credie/silica-link.git
cd silica-link

# Ruby setup
cd ruby-server
bundle install
rails db:create db:migrate
rails db:seed

# Redis setup (local)
redis-server

# Sidekiq setup
bundle exec sidekiq

# ReactJS setup
cd ../scan-ui
npm install
npm start

# Run tests
cd ../ruby-server
bundle exec rspec
```

---

## Architecture Decision Records (ADRs)

### ADR-001: Kintone as Single Source of Truth

**Decision:** Use Kintone as primary data store for all business data.

**Rationale:**
- Already in use, team familiar
- Good workflow capabilities
- Cost-effective (no additional database license)
- REST API for integration

**Alternatives Considered:**
- PostgreSQL: Would require migration, lose workflow features
- MongoDB: No workflow capabilities, additional cost

**Consequences:**
- All business logic must integrate with Kintone API
- Rate limits must be respected (10 req/sec per app)
- Need robust error handling for API failures

---

### ADR-002: Dual Stock Management (Logic + Physical)

**Decision:** Maintain separate Logic Stock (Kintone) and Physical Stock (DB).

**Rationale:**
- Logic Stock: For allocation, reporting, prevent overselling
- Physical Stock: For actual warehouse operations (Shelf × LOT)
- Different update frequencies and purposes

**Alternatives Considered:**
- Single Stock: Would be complex, hard to maintain accuracy
- Physical Stock only: Lose allocation capabilities

**Consequences:**
- Must sync Physical → Logic after each scan
- Nightly batch validation to ensure consistency
- More complex but more accurate

---

### ADR-003: Webhook + Batch Pattern

**Decision:** Use webhook as primary, batch polling as fallback.

**Rationale:**
- Webhook: Realtime, efficient
- Batch: Fallback for reliability (if webhook fails)
- Best of both worlds

**Alternatives Considered:**
- Webhook only: Risk of missing orders if webhook fails
- Batch only: Delay, inefficient

**Consequences:**
- Need to handle both patterns
- Idempotency critical (prevent duplicates)
- More complex but more reliable

---

### ADR-004: Sidekiq for Async Processing

**Decision:** Use Sidekiq (Redis-based) for background job processing.

**Rationale:**
- Battle-tested, reliable
- Good Rails integration
- Supports retry, DLQ, scheduling
- Team familiar with Rails ecosystem

**Alternatives Considered:**
- AWS SQS: More AWS-native but less Rails integration
- Delayed Job: Simpler but less features

**Consequences:**
- Need Redis infrastructure
- Good for Rails apps, less suitable for other languages

---

_Generated by BMAD Decision Architecture Workflow v1.0_
_Date: 2025-11-12_
_For: amcolab_

