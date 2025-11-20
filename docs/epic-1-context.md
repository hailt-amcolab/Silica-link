# Epic Technical Specification: Foundation & Infrastructure

Date: 2025-11-20
Author: Potato
Epic ID: 1
Status: Draft

---

## Overview

Epic 1 establishes the foundational infrastructure và development environment cho Silica-link project. Đây là greenfield Rails application tích hợp với brownfield systems (Kintone, Shopify, Ship&Co, GMO Bank). Epic này setup project structure, deployment pipeline, và core infrastructure services (Redis, Sidekiq, MySQL) để enable tất cả subsequent development work.

Epic này là prerequisite cho tất cả epics khác vì nó provides:
- Project structure và development environment
- CI/CD pipeline cho automated deployment
- Core infrastructure services (Redis, Sidekiq, MySQL) cho webhook processing và idempotency

---

## Objectives and Scope

### In-Scope

- **Project Setup:**
  - Rails 7.x project initialization
  - MySQL database configuration
  - Redis connection setup
  - Sidekiq configuration
  - Basic project structure (app/, config/, db/, spec/)
  - Environment variables management (.env.example)

- **Deployment Pipeline:**
  - CI/CD setup (GitHub Actions hoặc similar)
  - Automated testing trong pipeline
  - Staging environment deployment
  - Production environment deployment
  - Smoke tests sau deployment

- **Core Infrastructure Services:**
  - Redis server connection và pooling
  - Sidekiq workers configuration với queues
  - MySQL database setup và migrations structure
  - Health check endpoints (/health, /ready)

### Out-of-Scope

- Application-specific business logic (covered in Epic 2+)
- Kintone integration implementation (covered in Epic 2)
- Webhook processing logic (covered in Epic 2)
- BtoB Portal frontend (covered in Epic 5)
- Dashboard implementation (covered in Epic 7)

---

## System Architecture Alignment

Epic 1 aligns với Architecture Decision 1 (Webhook Pipeline Architecture) và Decision 2 (Technology Stack):

**Architecture Components:**
- **Rails Application:** Core backend framework (Decision 1)
- **Sidekiq:** Job queue processing (Decision 1)
- **Redis:** Cache và idempotency storage (Decision 1)
- **MySQL:** Primary database (Decision 1)
- **Hotwire:** Frontend framework (Decision 4) - setup trong Epic 1, implementation trong Epic 5

**Architectural Constraints:**
- Must support Rails 7.x với latest stable versions
- Must support MySQL 8.0+
- Must support Redis 6.0+
- Must support Sidekiq 7.0+
- Must follow Rails conventions và best practices

**Integration Points Prepared:**
- Kintone API integration layer (prepared, implemented in Epic 2)
- Shopify webhook reception (prepared, implemented in Epic 2)
- AWS S3 for audit logs (prepared, implemented in Epic 8)

---

## Detailed Design

### Services and Modules

| Service/Module | Responsibility | Inputs | Outputs | Owner |
|----------------|----------------|--------|---------|-------|
| **Rails Application** | Core application framework | HTTP requests, webhooks | HTTP responses, job enqueue | Rails |
| **Sidekiq** | Background job processing | Job enqueue từ Rails | Job execution results | Sidekiq workers |
| **Redis** | Cache và idempotency storage | Key-value operations | Cached data, idempotency keys | Redis client |
| **MySQL** | Primary data persistence | SQL queries | Query results | ActiveRecord |
| **Health Check Endpoints** | System health monitoring | HTTP GET requests | Health status JSON | Rails controller |

### Data Models and Contracts

**Initial Database Schema:**

```ruby
# db/migrate/YYYYMMDDHHMMSS_create_schema_migrations.rb
# Rails standard migration tracking table
# No custom tables needed in Epic 1 - foundation only
```

**Environment Variables (.env.example):**

```bash
# Database
DATABASE_URL=mysql2://user:password@localhost:3306/silica_link_development

# Redis
REDIS_URL=redis://localhost:6379/0

# Sidekiq
SIDEKIQ_REDIS_URL=redis://localhost:6379/1

# Kintone (prepared for Epic 2)
KINTONE_DOMAIN=
KINTONE_API_TOKEN=

# Shopify (prepared for Epic 2)
SHOPIFY_WEBHOOK_SECRET=

# AWS S3 (prepared for Epic 8)
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_REGION=
AWS_S3_BUCKET=

# Application
RAILS_ENV=development
SECRET_KEY_BASE=
```

**Redis Data Structures (Prepared for Epic 2):**

- **Idempotency Keys:** `idempotency:{webhook_id}` → TTL 24 hours
- **Cache Keys:** `cache:{namespace}:{key}` → TTL varies
- **Sidekiq Queues:** `queue:default`, `queue:webhooks`, `queue:fulfillment`

### APIs and Interfaces

**Health Check Endpoints:**

```
GET /health
Response: 200 OK
{
  "status": "healthy",
  "timestamp": "2025-11-20T02:34:33Z",
  "services": {
    "database": "connected",
    "redis": "connected",
    "sidekiq": "running"
  }
}

GET /ready
Response: 200 OK (if all services ready)
Response: 503 Service Unavailable (if any service not ready)
{
  "status": "ready",
  "timestamp": "2025-11-20T02:34:33Z"
}
```

**Internal Interfaces:**

- **Sidekiq Job Interface:**
  ```ruby
  class BaseJob < ApplicationJob
    queue_as :default
    retry_on StandardError, wait: :exponentially_longer, attempts: 3
  end
  ```

- **Redis Client Interface:**
  ```ruby
  Redis.current # Singleton connection
  Redis.new(url: ENV['REDIS_URL']) # Custom connection
  ```

### Workflows and Sequencing

**Project Setup Workflow:**

1. **Initialize Rails Project:**
   ```
   rails new silica-link --database=mysql --skip-test
   cd silica-link
   ```

2. **Add Dependencies:**
   ```
   bundle add sidekiq
   bundle add redis
   bundle add dotenv-rails
   ```

3. **Configure Database:**
   - Update `config/database.yml` với MySQL settings
   - Create database: `rails db:create`

4. **Configure Redis:**
   - Add Redis initializer: `config/initializers/redis.rb`
   - Configure connection pooling

5. **Configure Sidekiq:**
   - Add Sidekiq initializer: `config/initializers/sidekiq.rb`
   - Configure queues và workers
   - Add Sidekiq web UI route

6. **Setup Environment Variables:**
   - Create `.env.example` với all required variables
   - Add `.env` to `.gitignore`

7. **Initialize Git:**
   ```
   git init
   git add .
   git commit -m "Initial Rails project setup"
   ```

**Deployment Pipeline Workflow:**

1. **GitHub Actions Setup:**
   - Create `.github/workflows/ci.yml`
   - Configure test, build, deploy stages

2. **Staging Deployment:**
   - Setup staging environment trên AWS EC2
   - Configure deployment script
   - Add staging environment variables

3. **Production Deployment:**
   - Setup production environment trên AWS EC2
   - Configure deployment script
   - Add production environment variables
   - Setup monitoring và alerting

**Infrastructure Services Startup Sequence:**

1. **Database:**
   - MySQL server starts
   - Rails connects via ActiveRecord
   - Health check: `ActiveRecord::Base.connection.execute("SELECT 1")`

2. **Redis:**
   - Redis server starts
   - Rails connects via Redis client
   - Health check: `Redis.current.ping == "PONG"`

3. **Sidekiq:**
   - Sidekiq workers start
   - Connect to Redis
   - Health check: `Sidekiq::ProcessSet.new.size > 0`

4. **Rails Application:**
   - Rails server starts
   - All services connected
   - Health check endpoints respond

---

## Non-Functional Requirements

### Performance

**Targets:**
- Health check endpoint response time: < 100ms
- Database connection time: < 50ms
- Redis connection time: < 10ms
- Sidekiq worker startup time: < 5 seconds

**Measurement:**
- Health check endpoint latency monitoring
- Database connection pool monitoring
- Redis connection pool monitoring

**Link to PRD:**
- NFR1: System response time < 200ms (health checks must be faster)
- NFR2: Support 1000+ orders/hour (infrastructure must support this load)

### Security

**Authentication/Authorization:**
- No authentication required trong Epic 1 (health checks are public)
- Environment variables must be secure (not committed to git)
- Secrets management via environment variables

**Data Handling:**
- No sensitive data processed trong Epic 1
- Database credentials stored in environment variables
- Redis credentials stored in environment variables

**Threat Notes:**
- Health check endpoints should not expose sensitive information
- Environment variables must be properly secured

**Source Sections:**
- Architecture Decision 8: Security (prepared for Epic 2+)
- PRD FR97-FR100: Audit & Compliance (prepared for Epic 8)

### Reliability/Availability

**Availability:**
- Health check endpoints must be available 99.9% of the time
- Database connection must be resilient (connection pooling)
- Redis connection must be resilient (connection pooling)
- Sidekiq workers must auto-restart on failure

**Recovery:**
- Database connection auto-reconnect on failure
- Redis connection auto-reconnect on failure
- Sidekiq workers auto-restart on crash

**Degradation Behavior:**
- If database unavailable: Health check returns 503
- If Redis unavailable: Health check returns 503
- If Sidekiq unavailable: Health check returns 503

### Observability

**Logging:**
- Rails application logs (standard Rails logging)
- Sidekiq job logs (Sidekiq web UI)
- Health check access logs

**Metrics:**
- Health check endpoint response time
- Database connection pool usage
- Redis connection pool usage
- Sidekiq queue lengths

**Tracing:**
- Request tracing (prepared for Epic 2+)
- Job tracing (prepared for Epic 2+)

**Required Signals:**
- Health check endpoint availability
- Database connection status
- Redis connection status
- Sidekiq worker status

---

## Dependencies and Integrations

### Ruby Dependencies (Gemfile)

```ruby
source 'https://rubygems.org'
ruby '~> 3.2.0'

gem 'rails', '~> 7.1.0'
gem 'mysql2', '~> 0.5.0'
gem 'sidekiq', '~> 7.2.0'
gem 'redis', '~> 5.0.0'
gem 'dotenv-rails', '~> 2.8.0'

group :development, :test do
  gem 'rspec-rails', '~> 6.0.0'
  gem 'factory_bot_rails', '~> 6.2.0'
end

group :development do
  gem 'sidekiq-web', '~> 5.0.0'
end
```

### System Dependencies

- **Ruby:** 3.2.0+
- **MySQL:** 8.0+
- **Redis:** 6.0+
- **Node.js:** 18.0+ (for asset pipeline)
- **Git:** 2.30+

### External Integrations (Prepared, Not Implemented)

- **Kintone API:** Prepared for Epic 2
- **Shopify Webhooks:** Prepared for Epic 2
- **Ship&Co API:** Prepared for Epic 3
- **GMO Bank API:** Prepared for Epic 6
- **AWS S3:** Prepared for Epic 8

### CI/CD Dependencies

- **GitHub Actions:** For CI/CD pipeline
- **AWS EC2:** For staging và production deployment
- **Docker (Optional):** For containerized deployment

---

## Acceptance Criteria (Authoritative)

1. **AC1: Rails Project Setup**
   - Rails 7.x project được tạo với MySQL database
   - Redis connection được configure
   - Sidekiq được install và configure
   - Basic project structure exists (app/, config/, db/, spec/)
   - Environment variables setup (.env.example exists)
   - Git repository được initialize

2. **AC2: Deployment Pipeline**
   - CI/CD pipeline được setup (GitHub Actions hoặc similar)
   - Automated tests chạy trong pipeline
   - Staging environment deployment works
   - Production environment deployment works
   - Smoke tests pass sau deployment

3. **AC3: Core Infrastructure Services**
   - Redis server running và accessible
   - Sidekiq workers running
   - MySQL database accessible
   - Health check endpoints respond correctly (/health, /ready)

4. **AC4: Health Check Endpoints**
   - GET /health returns 200 với service status
   - GET /ready returns 200 khi all services ready
   - GET /ready returns 503 khi any service not ready

5. **AC5: Environment Variables**
   - .env.example exists với all required variables
   - .env được add vào .gitignore
   - Environment variables được load correctly trong application

---

## Traceability Mapping

| AC | Spec Section | Component/API | Test Idea |
|----|--------------|---------------|-----------|
| AC1 | Story 1.1 | Rails project, Gemfile, config files | Test Rails project structure, dependencies install |
| AC2 | Story 1.2 | CI/CD pipeline, deployment scripts | Test pipeline runs, deployment succeeds |
| AC3 | Story 1.3 | Redis, Sidekiq, MySQL connections | Test services connect, health checks work |
| AC4 | Story 1.3 | Health check endpoints | Test endpoints return correct status |
| AC5 | Story 1.1 | .env.example, environment loading | Test env vars load correctly |

**PRD Traceability:**
- Infrastructure needs for all FRs (Epic 1 enables all subsequent epics)

**Architecture Traceability:**
- Decision 1: Webhook Pipeline Architecture (Rails + Sidekiq + Redis)
- Decision 2: Technology Stack (Rails, MySQL, Redis, Sidekiq)

---

## Risks, Assumptions, Open Questions

### Risks

**R1: Infrastructure Setup Complexity**
- **Risk:** Setting up multiple services (Rails, MySQL, Redis, Sidekiq) có thể complex
- **Mitigation:** Use Docker Compose cho local development, follow Rails conventions
- **Status:** Low risk, standard Rails setup

**R2: Deployment Pipeline Issues**
- **Risk:** CI/CD pipeline có thể fail hoặc không reliable
- **Mitigation:** Start với simple pipeline, iterate và improve
- **Status:** Medium risk, can be addressed iteratively

**R3: Environment Variable Management**
- **Risk:** Environment variables có thể be misconfigured hoặc insecure
- **Mitigation:** Use .env.example, document all variables, use secure storage
- **Status:** Low risk, standard practice

### Assumptions

**A1: Infrastructure Availability**
- **Assumption:** MySQL, Redis servers sẽ be available và accessible
- **Validation:** Health checks will validate this
- **Status:** Standard assumption

**A2: Development Environment**
- **Assumption:** Developers có Ruby, MySQL, Redis installed locally
- **Validation:** Document setup requirements
- **Status:** Standard assumption

**A3: Deployment Access**
- **Assumption:** Team có access to AWS EC2 và GitHub Actions
- **Validation:** Verify access before starting
- **Status:** Need to verify

### Open Questions

**Q1: Docker Usage**
- **Question:** Should we use Docker for local development?
- **Decision:** Optional, but recommended for consistency
- **Status:** Can be decided during implementation

**Q2: Monitoring Tools**
- **Question:** What monitoring tools should we use?
- **Decision:** Start với basic health checks, add monitoring later
- **Status:** Can be decided during implementation

**Q3: Staging Environment**
- **Question:** Do we need separate staging environment?
- **Decision:** Yes, for safe testing before production
- **Status:** Will be addressed in Story 1.2

---

## Test Strategy Summary

### Test Levels

**Unit Tests:**
- Test health check endpoint logic
- Test environment variable loading
- Test Redis connection
- Test database connection
- Test Sidekiq configuration

**Integration Tests:**
- Test health check endpoints với all services
- Test deployment pipeline
- Test service startup sequence

**System Tests:**
- Test full application startup
- Test health check endpoints end-to-end
- Test deployment to staging

### Test Frameworks

- **RSpec:** For unit và integration tests
- **Factory Bot:** For test data generation
- **GitHub Actions:** For CI/CD pipeline tests

### Coverage of ACs

- **AC1:** Unit tests cho project structure, integration tests cho services
- **AC2:** Integration tests cho CI/CD pipeline
- **AC3:** Integration tests cho service connections
- **AC4:** Integration tests cho health check endpoints
- **AC5:** Unit tests cho environment variable loading

### Edge Cases

- Database connection failure
- Redis connection failure
- Sidekiq worker crash
- Health check khi services partially available
- Environment variables missing hoặc invalid

---

**Document Status:** Complete
**Next Steps:** Proceed với Story 1.1 implementation

