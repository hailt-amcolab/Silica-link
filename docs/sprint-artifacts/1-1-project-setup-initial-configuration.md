# Story 1.1: Project Setup & Initial Configuration

Status: ready-for-dev

## Story

As a **developer**,
I want **Rails project được setup với đầy đủ dependencies và configuration**,
so that **tôi có thể bắt đầu development ngay**.

## Acceptance Criteria

1. **Given** project chưa được initialize
   **When** tôi chạy setup commands
   **Then** Rails project được tạo với:
   - Ruby on Rails (latest stable)
   - MySQL database connection
   - Redis connection
   - Sidekiq configuration
   - Basic project structure (app/, config/, db/, spec/)
   - Environment variables setup (.env.example)
   - Git repository initialized

2. **Given** Rails project đã được tạo
   **When** tôi start application
   **Then** tất cả services sẽ:
   - Rails server running và accessible
   - MySQL database accessible
   - Redis server running và accessible
   - Sidekiq workers running

3. **Given** project structure đã được setup
   **When** tôi review project structure
   **Then** project phải có:
   - Proper directory structure theo Rails conventions
   - Initializers cho Kintone, Redis
   - Database configuration (database.yml)
   - Sidekiq configuration (sidekiq.yml)
   - Environment variables template (.env.example)

## Tasks / Subtasks

- [ ] Task 1: Initialize Rails project (AC: #1)
  - [ ] Subtask 1.1: Create new Rails application với MySQL database
  - [ ] Subtask 1.2: Configure database.yml với MySQL connection
  - [ ] Subtask 1.3: Add Redis gem và configure Redis connection
  - [ ] Subtask 1.4: Add Sidekiq gem và configure Sidekiq
  - [ ] Subtask 1.5: Create .env.example với required environment variables
  - [ ] Subtask 1.6: Initialize Git repository và create .gitignore

- [ ] Task 2: Setup project structure (AC: #1, #3)
  - [ ] Subtask 2.1: Create initializers directory structure
  - [ ] Subtask 2.2: Create services directory structure
  - [ ] Subtask 2.3: Create adapters directory structure
  - [ ] Subtask 2.4: Create jobs directory structure
  - [ ] Subtask 2.5: Create spec directory structure với RSpec setup

- [ ] Task 3: Configure initializers (AC: #1, #3)
  - [ ] Subtask 3.1: Create config/initializers/kintone.rb với Kintone API configuration
  - [ ] Subtask 3.2: Create config/initializers/redis.rb với Redis connection configuration
  - [ ] Subtask 3.3: Create config/sidekiq.yml với Sidekiq configuration

- [ ] Task 4: Verify setup (AC: #2)
  - [ ] Subtask 4.1: Test Rails server starts successfully
  - [ ] Subtask 4.2: Test MySQL database connection
  - [ ] Subtask 4.3: Test Redis connection
  - [ ] Subtask 4.4: Test Sidekiq workers start successfully
  - [ ] Subtask 4.5: Verify all environment variables are documented in .env.example

## Dev Notes

### Architecture Patterns and Constraints

- **Technology Stack:** Ruby on Rails (latest stable), MySQL, Redis, Sidekiq
- **Project Structure:** Follow Rails conventions với separation of concerns:
  - `app/services/` - Business logic abstraction
  - `app/adapters/` - External API integration (Kintone, Shopify, Ship&Co, GMO Bank)
  - `app/jobs/` - Background job processing
  - `app/models/` - Data persistence (MySQL)
  - `app/controllers/` - Request handling
- **Configuration Management:** Use environment variables cho sensitive data (API keys, secrets)
- **Database:** MySQL cho temporary data storage, Kintone là source of truth cho business data

[Source: docs/architecture.md#Technology-Stack-Details]

### Source Tree Components to Touch

- `config/database.yml` - MySQL database configuration
- `config/sidekiq.yml` - Sidekiq worker configuration
- `config/initializers/kintone.rb` - Kintone API configuration
- `config/initializers/redis.rb` - Redis connection configuration
- `.env.example` - Environment variables template
- `Gemfile` - Ruby dependencies
- `config/application.rb` - Rails application configuration
- `config/environments/` - Environment-specific configurations

[Source: docs/architecture.md#Project-Structure]

### Testing Standards Summary

- Setup RSpec cho testing framework
- Create basic test structure trong `spec/` directory
- Ensure test database configuration
- Document testing approach (unit tests, integration tests)

### Project Structure Notes

- **Alignment:** Project structure follows Rails conventions với additional directories cho services và adapters
- **Naming Conventions:**
  - Controllers: `Webhooks::ShopifyController`, `BtoB::OrdersController`
  - Services: `OrderProcessor`, `InventorySync`, `RebateCalculator`
  - Adapters: `KintoneAdapter`, `ShopifyAdapter`, `ShipCoAdapter`
  - Jobs: `ProcessWebhookJob`, `SyncToKintoneJob`
  - Models: `Order`, `WebhookEvent`, `InventoryMovement`

[Source: docs/architecture.md#Consistency-Rules]

### References

- **Architecture Document:** [Source: docs/architecture.md]
  - Technology Stack: Rails + Sidekiq + Redis + MySQL
  - Project Structure: Separation of concerns với services và adapters
  - Configuration: Environment variables cho sensitive data

- **PRD:** [Source: docs/prd.md#Epic-1-Foundation--Infrastructure]
  - Story 1.1 requirements và acceptance criteria
  - Technical notes về Rails setup

- **Epics:** [Source: docs/epics.md#Story-11-Project-Setup--Initial-Configuration]
  - Story statement và acceptance criteria
  - Technical notes về architecture decisions

### Learnings from Previous Story

**From Story:** First story in epic - no predecessor context

Đây là story đầu tiên trong Epic 1, không có previous story để tham khảo. Story này sẽ establish foundation cho tất cả subsequent development work.

## Dev Agent Record

### Context Reference

- docs/sprint-artifacts/1-1-project-setup-initial-configuration.context.xml

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List

