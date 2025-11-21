# Kickoff

# 🧩 **1. Mục tiêu dự án (Project Goal)**

## Sản phẩm giải quyết vấn đề gì?

**Silica-link** là nền tảng tích hợp tự động hóa toàn bộ chuỗi giá trị **Đặt hàng → Giao hàng → Nhập hàng → Quản lý hoa hồng/cổ tức** cho CREDIE. Hệ thống thay thế các quy trình thủ công rời rạc bằng automation realtime, tích hợp seamless với hệ thống hiện có (Kintone, Shopify, Ship&Co, GMO Bank).

**Vấn đề hiện tại:**
- Nhân viên đơn hàng phải nhập orders thủ công từ email/FAX/CSV (2-3 giờ/ngày)
- Check tồn kho manual, dễ oversell
- Tính rebate bằng Excel, dễ sai sót
- Tạo shipping labels thủ công, tốn thời gian
- Payment reconciliation thủ công, mất 1 tuần
- Không có realtime visibility cho stakeholders

## Giá trị mang lại cho end-user?

**Operations Team (Nhân viên đơn hàng, Nhân viên kho, Kế toán):**
- **80% time savings:** Từ 2-3 giờ/ngày xuống <30 phút/ngày
- **100% accuracy:** Zero manual data entry errors
- **Realtime visibility:** Dashboard realtime cho tất cả operations
- **Familiar interface:** Tiếp tục sử dụng Kintone như quen thuộc, không cần training lại

**Đại lý (BtoB Portal Users):**
- **Self-service:** Tự đặt hàng, track orders, xem lịch sử
- **Realtime inventory:** Biết tồn kho realtime khi đặt hàng
- **Faster confirmation:** Order được confirm nhanh hơn

**Business Impact:**
- **Order-to-ship time:** Giảm 50% (từ 2 ngày xuống 1 ngày)
- **Payment reconciliation:** Giảm 90% (từ 1 tuần xuống <1 ngày)
- **Scalability:** Hỗ trợ 1000+ orders/hour

## KPI/OKR mong muốn (3 tháng)

### Primary Goals

1. **100% BtoC Orders Automated**
   - Zero manual order entry
   - 99.9%+ webhook processing success rate
   - Zero duplicate orders

2. **Operations Team Satisfaction**
   - 80% time savings (2-3 hours/day → <30 min/day)
   - 90%+ user satisfaction score
   - Smooth adoption, minimal training needed

3. **Technical Excellence**
   - Webhook processing latency: < 2 seconds (p95)
   - System uptime: 99.9%+
   - Test coverage: ≥ 80%

### Success Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Order Processing Time | < 2 seconds | Monitoring dashboard |
| System Uptime | 99.9% | Uptime monitoring |
| Test Coverage | ≥ 80% | Code coverage tool |
| Bug Rate | < 0.25 bugs/story point | Bug tracking |
| User Satisfaction | ≥ 90% | Survey after 1 month |

## Scope tổng quát của phiên bản đầu tiên (MVP)

**MVP Timeline:** 3 months (6 sprints, 2 weeks/sprint)

**MVP Scope - Phases:**

**Phase 1: Order Processing Automation (MVP Core)**
- Webhook pipeline Shopify → Yoom → Rails → Kintone với idempotency
- Realtime inventory check trước khi confirm order
- Auto create order trong Kintone Orders App
- Support BtoC orders (no backorder - hard stop khi hết hàng)
- Audit log tất cả events vào S3

**Phase 2: Fulfillment Automation**
- Auto create picking tasks trong Kintone khi order confirmed
- Ship&Co API integration - auto generate shipping labels
- Auto sync tracking status về Shopify và notify customers
- Dashboard realtime cho operations team (Kintone native)

**Phase 2.5: Warehouse Management (Core)**
- Nhập kho nguyên vật liệu với scan barcode vị trí kệ
- Quản lý tồn kho tới cấp vị trí kệ (棚) và kho
- Scan barcode để track vị trí tồn kho và confirm picking
- Quản lý LOT và ngày sản xuất
- Stock Movement tracking

**Phase 3: BtoB Portal**
- BtoB Portal với authentication (cho đại lý)
- Order creation, review, approval workflow
- Partial shipment + backorder support (BtoB specific)
- Payment reconciliation với GMO Bank API
- Proforma Invoice generation

**Phase 4: Financial Automation**
- Auto calculate rebate từ sales data
- Quarterly locking và dashboard realtime
- Affiliate tracking và commission calculation

👉 **MVP Success Criteria:** 100% BtoC orders từ Shopify được xử lý tự động (zero manual entry), 99.9% webhook processing success rate, zero duplicate orders, operations team có thể sử dụng hệ thống mà không cần training nhiều.

---

# 📝 **2. Phạm vi (Project Scope)**

## Các module chính

**10 Epics (97+ Stories):**

1. **Foundation & Infrastructure** - Project setup, CI/CD, core services
2. **Order Processing Automation** - Webhook pipeline, auto sync orders
3. **Fulfillment Automation** - Auto picking tasks, shipping labels, tracking
4. **Warehouse Management Core** - Location tracking, LOT, stock movements
5. **BtoB Portal & Self-Service** - Đại lý tự đặt hàng và track
6. **Payment & Financial Automation** - Payment reconciliation, rebate calculation
7. **Dashboard & Visibility** - Realtime dashboards (Kintone native)
8. **Audit & Compliance** - Full audit trail, security
9. **Error Handling & Reliability** - Retry, DLQ, health checks
10. **Value Chain Optimization** - Advanced features, reporting

## Các màn hình / Interfaces

**Kintone Apps (Operations Team):**
- Orders App - Order management
- Stock App - Inventory management
- Stock Movements App - Movement tracking
- Rewards App - Rebate/commission tracking
- Tasks App - Picking/packing tasks
- Dashboard - Realtime operations dashboard

**BtoB Portal (Đại lý):**
- Login/Authentication
- Order Placement
- Order Tracking
- Order History
- Inventory Visibility

**Rails Admin (Internal):**
- Webhook monitoring
- Error logs
- System health checks
- Audit log viewer

## Luồng nghiệp vụ tổng thể

**BtoC Order Flow:**
```
Shopify Order → Webhook → Yoom → Rails → 
  → Check Inventory (Kintone) → 
  → Create Order (Kintone) → 
  → Create Picking Task (Kintone) → 
  → Generate Shipping Label (Ship&Co) → 
  → Sync Tracking (Shopify)
```

**BtoB Order Flow:**
```
Đại lý (BtoB Portal) → Create Order → 
  → Approval Workflow → 
  → Check Inventory → 
  → Partial Shipment Support → 
  → Payment Reconciliation (GMO Bank) → 
  → Generate Proforma Invoice
```

**Inventory Flow:**
```
Nhập kho (Scan barcode) → Update Stock (Kintone) → 
  → Sync to Shopify → 
  → Realtime Dashboard Update
```

## Tính năng có / không có trong Phase 1 (MVP)

### ✅ Must-have (Phase 1-3)

**Order Processing:**
- ✅ Webhook pipeline với idempotency
- ✅ Auto sync orders Shopify → Kintone
- ✅ Realtime inventory check
- ✅ BtoC order support (no backorder)
- ✅ BtoB order support (partial shipment)

**Fulfillment:**
- ✅ Auto picking tasks
- ✅ Ship&Co integration (shipping labels)
- ✅ Tracking sync Shopify

**Warehouse:**
- ✅ Location-level tracking (棚)
- ✅ LOT management
- ✅ Barcode scanning
- ✅ Stock movements

**BtoB Portal:**
- ✅ Authentication
- ✅ Order placement
- ✅ Order tracking

**Financial:**
- ✅ Payment reconciliation (GMO Bank)
- ✅ Rebate calculation
- ✅ Proforma Invoice

**Dashboard:**
- ✅ Operations dashboard (Kintone)
- ✅ Financial dashboard (Kintone)
- ✅ Warehouse dashboard (Kintone)

### ⚠️ Should-have (Phase 4, có thể defer)

- ⚠️ Advanced warehouse features (inventory count, multi-warehouse routing)
- ⚠️ Affiliate tracking advanced features
- ⚠️ Advanced reporting và analytics

### ❌ Nice-to-have (Post-MVP)

- ❌ Multi-language support
- ❌ Mobile app
- ❌ Rakuten/Amazon integration (Phase 2)
- ❌ Advanced analytics dashboard
- ❌ Custom CMS

👉 **Scope rõ ràng:** MVP focus vào automation core (Order Processing, Fulfillment, Warehouse Core, BtoB Portal). Advanced features sẽ được implement sau MVP khi operations team đã quen với hệ thống.

---

# 📅 **3. Timeline & Milestones**

## Tổng thời gian dự kiến

**MVP Timeline:** **3 months (12 weeks, 6 sprints)**

**Start Date:** TBD  
**Target MVP Completion:** TBD + 3 months

## Các phase

### Month 1: Foundation & Core Automation

**Sprint 1 (Weeks 1-2):**
- Epic 1: Foundation & Infrastructure
  - Project Setup
  - Deployment Pipeline
  - Core Infrastructure Services

**Sprint 2 (Weeks 3-4):**
- Epic 2: Order Processing Automation (Part 1)
  - Webhook Reception
  - Idempotency Implementation
  - Kintone Integration Layer

### Month 2: Automation & Portal

**Sprint 3 (Weeks 5-6):**
- Epic 2: Order Processing Automation (Part 2)
  - Order Sync to Kintone
  - Inventory Check
  - Multi-channel Routing

**Sprint 4 (Weeks 7-8):**
- Epic 3: Fulfillment Automation
  - Picking Task Creation
  - Ship&Co Integration
  - Tracking Sync
- Epic 5: BtoB Portal (Part 1)
  - Portal Setup
  - Authentication

### Month 3: Completion & Polish

**Sprint 5 (Weeks 9-10):**
- Epic 5: BtoB Portal (Part 2)
  - Order Placement
  - Order Tracking
- Epic 6: Payment & Financial Automation
  - GMO Bank Integration
  - Payment Reconciliation

**Sprint 6 (Weeks 11-12):**
- Epic 7: Dashboard & Visibility
- Epic 9: Error Handling & Reliability
- Bug fixes và polish
- User acceptance testing
- Production deployment

## Mốc bàn giao

**Milestone 1: Foundation Complete (End of Sprint 1)**
- Rails project setup
- CI/CD pipeline working
- Health check endpoints

**Milestone 2: Order Processing Core (End of Sprint 2)**
- Webhook pipeline working
- Idempotency implemented
- Kintone integration layer

**Milestone 3: Full Automation (End of Sprint 4)**
- 100% BtoC orders automated
- Fulfillment automation complete
- BtoB Portal basic features

**Milestone 4: MVP Complete (End of Sprint 6)**
- All MVP features complete
- Production deployment
- User acceptance testing passed

## Sprint overview

**Sprint Duration:** **2 weeks (10 working days)**

**Sprint Structure:**
- **Week 1:**
  - Monday: Sprint Planning (2-4 hours)
  - Tuesday-Friday: Development + Daily Standups
- **Week 2:**
  - Monday-Thursday: Development + Daily Standups
  - Friday: Sprint Review (2 hours) + Retrospective (1 hour)

**Sprint Ceremonies:**
- **Sprint Planning:** Day 1 (2-4 hours)
- **Daily Standup:** 15 minutes, every day at 9:00 AM
- **Sprint Review:** Last day (2 hours) - Demo completed stories
- **Sprint Retrospective:** Last day (1 hour) - Process improvement

## Cách demo mỗi Sprint

**Sprint Review Format:**
- Each developer demos their completed stories
- Show working features trên staging environment
- Answer questions từ stakeholders
- Collect feedback
- Review sprint goal achievement

**Demo Environment:**
- Staging environment với test data
- Kintone sandbox cho Kintone features
- All features integrated và tested

## Khi nào sẽ có bản test public

**Staging Environment:**
- Available từ Sprint 2 (sau khi foundation complete)
- Updated sau mỗi sprint
- Access cho operations team để test

**UAT (User Acceptance Testing):**
- Sprint 6 (Weeks 11-12)
- Operations team test tất cả MVP features
- Collect feedback và fix critical bugs

**Production Release:**
- End of Sprint 6 (Week 12)
- After UAT passed
- Phased rollout (nếu cần)

👉 **Timeline commitment:** Team sẽ deliver MVP trong 3 months (6 sprints). Mỗi sprint có demo để stakeholders review progress. Staging environment available từ Sprint 2 cho early testing.

---

# 🧠 **4. Phương pháp làm việc (Process)**

## Mô hình: Scrum / Agile

**Framework:** **Scrum với 2-week sprints**

**Agile Principles:**
- Incremental value delivery
- Working software over documentation
- Responding to change
- Customer collaboration

## Chia sprint như thế nào

**Sprint Duration:** 2 weeks (10 working days)

**Sprint Planning:**
- Day 1, Monday (2-4 hours)
- Select stories từ backlog
- Estimate stories (Story Points)
- Set sprint goal
- Break down tasks

**Daily Standup:**
- Every day, 9:00 AM (15 minutes)
- 3 questions: What did I complete? What will I work on? What blockers?
- Update task status

**Sprint Review:**
- Last day, Friday (2 hours)
- Demo completed stories
- Review sprint goal
- Collect feedback

**Sprint Retrospective:**
- Last day, Friday (1 hour)
- What went well? What didn't? What to improve?
- Action items

## Ai là PM, ai là PO

**Product Owner / Scrum Master (PM):**
- **Name:** Potato
- **Responsibilities:**
  - Define product requirements (PRD)
  - Prioritize backlog
  - Facilitate sprint ceremonies
  - Remove blockers
  - Communicate với stakeholders
  - Track progress và metrics

**Backend Developer(s):**
- Implement backend features (Rails)
- Write tests (RSpec)
- Code review
- Deploy to staging/production

**Frontend Developer (Optional):**
- Implement BtoB Portal (Hotwire)
- UI/UX implementation

**QA Tester (Optional):**
- Test stories after development
- Report bugs
- Validate acceptance criteria

## Cách planning, daily, review, retro

**Sprint Planning:**
1. Review previous sprint results
2. Review product backlog priorities
3. Select stories từ backlog
4. Estimate stories (Story Points)
5. Break down stories into tasks
6. Set sprint goal
7. Identify risks và blockers

**Daily Standup:**
- Format: Round-robin, 2-3 minutes per person
- 3 Questions:
  1. What did I complete yesterday?
  2. What will I work on today?
  3. What blockers do I have?
- Update task status after standup

**Sprint Review:**
- Demo each completed story
- Show working features
- Answer questions
- Collect feedback
- Review sprint goal achievement
- Update product backlog

**Sprint Retrospective:**
- Format: Start-Stop-Continue
- What went well? (15 min)
- What didn't go well? (15 min)
- What should we improve? (15 min)
- Action items (15 min)

## Cách đổi requirement (change request flow)

**Change Request Process:**

1. **Request Submission:**
   - Stakeholder submits change request
   - PM evaluates impact (scope, timeline, cost)
   - PM discusses với team

2. **Impact Assessment:**
   - Estimate effort (story points)
   - Identify affected stories/epics
   - Assess timeline impact
   - Assess risk

3. **Decision:**
   - **Accept:** Add to backlog, prioritize
   - **Defer:** Add to backlog, schedule later
   - **Reject:** Explain reason

4. **If Accepted:**
   - Add to product backlog
   - Re-prioritize backlog
   - Plan for next sprint (nếu urgent)
   - Communicate với stakeholders

**Change Request Rules:**

- ✅ **Within Sprint:** Only critical bugs/blockers
- ✅ **Between Sprints:** Can add to next sprint planning
- ✅ **Major Changes:** May require sprint goal adjustment
- ❌ **Mid-Sprint Feature Addition:** Not allowed (disrupts sprint goal)

**Why không thể yêu cầu thêm features giữa Sprint:**

1. **Sprint Goal Commitment:** Team đã commit to sprint goal
2. **Resource Allocation:** Stories đã được assigned và estimated
3. **Quality Impact:** Adding features mid-sprint → less testing time
4. **Velocity Impact:** Disrupts team velocity tracking
5. **Scope Creep:** Leads to missed deadlines và quality issues

**Exception:** Only critical bugs/blockers can be added mid-sprint với PM approval.

👉 **Process commitment:** Team follows Scrum với 2-week sprints. Change requests được evaluate và prioritized trong sprint planning. Mid-sprint feature additions không được phép (except critical issues).

---

# 💬 **5. Kênh liên lạc & tần suất họp**

## Group chat: Slack/Teams/Zalo/Discord

**Primary Communication:**
- **Slack:** Team communication, daily updates
- **Email:** Formal communications, status updates
- **GitHub:** Code reviews, PR discussions

**Channels:**
- `#silica-link-dev` - Development discussions
- `#silica-link-ops` - Operations team questions
- `#silica-link-alerts` - System alerts, errors

## Tần suất daily

**Daily Standup:**
- **Time:** 9:00 AM (hoặc agreed time)
- **Duration:** 15 minutes
- **Format:** Round-robin, 2-3 minutes per person
- **Location:** Slack hoặc in-person

**Daily Updates:**
- Update task status trong tracking tool (Jira/Linear/GitHub Projects)
- Post blockers immediately trong Slack
- Update sprint dashboard

## Weekly sync với khách

**Sprint Review (Bi-weekly):**
- **Time:** Last day of sprint, Friday (2 hours)
- **Attendees:** PM, Developers, Stakeholders, Operations team
- **Agenda:**
  - Demo completed stories
  - Review sprint goal
  - Collect feedback
  - Discuss next sprint priorities

**Weekly Status Update (Optional):**
- **Time:** Friday afternoon
- **Format:** Email summary
  - Progress summary
  - Completed stories
  - Upcoming priorities
  - Blockers (nếu có)

## Ai approve requirement

**Product Owner (PM):**
- Approve product requirements
- Approve PRD changes
- Approve story acceptance criteria
- Approve change requests

**Stakeholders:**
- Approve major scope changes
- Approve business logic changes
- Provide feedback trong sprint review

## Ai approve UI/UX

**Product Owner (PM):**
- Approve UI/UX designs
- Approve Kintone app configurations
- Approve BtoB Portal UI
- Approve dashboard layouts

**Operations Team:**
- Review và provide feedback
- Approve Kintone interface changes
- Test và validate usability

## Bạn cần khách làm gì và khi nào họ phải trả lời

**Kintone Access & Credentials:**
- Provide Kintone sandbox access: **Before Sprint 1**
- Provide Kintone production credentials: **Before Sprint 4**
- Provide Kintone app schemas: **Before Sprint 2**

**API Access & Documentation:**
- Shopify webhook setup: **Before Sprint 2**
- Ship&Co API credentials: **Before Sprint 4**
- GMO Bank API credentials: **Before Sprint 5**
- API documentation: **Before integration stories**

**Feedback & Approvals:**
- Sprint Review feedback: **Within 24 hours after review**
- Change request decisions: **Within 48 hours**
- UI/UX approval: **Within 48 hours**
- Story acceptance: **Within 24 hours after demo**

**Data & Testing:**
- Test data for staging: **Before Sprint 2**
- UAT participation: **Sprint 6 (Weeks 11-12)**
- Production data access: **Before Sprint 6**

**Communication Rules:**

- ✅ **Response Time:** 24-48 hours for non-urgent, immediate for blockers
- ✅ **Escalation:** Blockers → escalate immediately
- ✅ **Documentation:** All decisions documented trong Slack/Email
- ❌ **Ghost:** No response after 48 hours → PM will escalate

👉 **Communication commitment:** Team communicates daily qua standup, bi-weekly qua sprint review. Stakeholders cần respond within 24-48 hours để không block development progress.

---

# 🧪 **6. QA Strategy**

## Cách test

**Test-Driven Development (TDD):**
- Write tests first
- Implement code
- Make tests pass
- Refactor

**Test Levels:**

1. **Unit Tests:**
   - Test individual components
   - Target: ≥ 80% coverage
   - Framework: RSpec

2. **Integration Tests:**
   - Test Kintone API integration
   - Test Shopify webhook processing
   - Test Ship&Co integration
   - Test GMO Bank integration

3. **System Tests:**
   - End-to-end workflows
   - Full order processing flow
   - BtoB Portal workflows

4. **Kintone Testing:**
   - Test trên Kintone sandbox
   - Verify app configurations
   - Test dashboard updates
   - Verify data sync

**Test Automation:**
- CI/CD pipeline runs tests automatically
- Tests run on every PR
- Coverage reports generated

## Test environment

**Development:**
- Local development environment
- Kintone sandbox
- Test data only

**Staging:**
- AWS EC2 instance
- MySQL database
- Redis server
- Sidekiq workers
- Kintone sandbox
- Available từ Sprint 2

**Production:**
- AWS EC2 instance (separate)
- MySQL database (separate)
- Redis server (separate)
- Kintone production
- Deploy after UAT passed

## Bug process

**Bug Lifecycle:**
```
Found → Reported → Assigned → Fixed → Verified → Closed
```

**Bug Reporting:**
- Report trong tracking tool (Jira/Linear/GitHub Issues)
- Include: Steps to reproduce, expected vs actual, screenshots/logs
- Link to story (nếu related)

**Bug Assignment:**
- PM assigns to developer
- Priority based on severity
- Estimated và tracked

**Bug Fixing:**
- Developer fixes bug
- Write test (nếu missing)
- Code review
- Deploy to staging

**Bug Verification:**
- QA/PM verifies fix
- Test regression
- Close bug if verified

## Severity & priority

**Severity Levels:**

1. **P0 - Critical:**
   - System down
   - Data loss
   - Security breach
   - **Response:** Immediate fix

2. **P1 - High:**
   - Major feature broken
   - Production issue affecting users
   - **Response:** Fix within 24 hours

3. **P2 - Medium:**
   - Minor feature issue
   - Workaround available
   - **Response:** Fix within current sprint

4. **P3 - Low:**
   - UI/UX minor issues
   - Nice-to-have improvements
   - **Response:** Fix in next sprint

**Priority Assignment:**
- PM assigns priority based on severity và business impact
- Critical bugs can be added mid-sprint
- Other bugs go to backlog

## Regression test theo sprint

**Per Sprint:**
- Test all completed stories
- Test critical workflows
- Test integrations
- Test Kintone apps

**Before Release:**
- Full regression test
- Test all MVP features
- Test end-to-end workflows
- Performance testing
- Security testing

**Regression Test Scope:**
- All completed stories
- Critical user journeys
- Integration points
- Kintone apps và dashboards

## Khách sẽ nhận bản test lúc nào

**Staging Environment:**
- **Available:** Sprint 2 (after foundation complete)
- **Updated:** After each sprint
- **Access:** Operations team có thể test anytime

**UAT (User Acceptance Testing):**
- **Timing:** Sprint 6 (Weeks 11-12)
- **Scope:** All MVP features
- **Duration:** 1-2 weeks
- **Feedback:** Collected và prioritized

**Production Release:**
- **Timing:** End of Sprint 6
- **After:** UAT passed, all critical bugs fixed
- **Rollout:** Phased (nếu cần)

## Công cụ: Jira, Notion, Linear…

**Bug Tracking:**
- **Jira/Linear/GitHub Issues:** Bug tracking
- **GitHub PRs:** Code review, linked to bugs
- **Slack:** Bug notifications, discussions

**Test Management:**
- **RSpec:** Test framework
- **SimpleCov:** Coverage tracking
- **GitHub Actions:** CI/CD, automated testing

**Documentation:**
- **GitHub Wiki:** Test documentation
- **Google Docs/Notion:** Test plans, results

👉 **QA commitment:** Team follows TDD, tests at multiple levels, tracks bugs với priority. Staging environment available từ Sprint 2. UAT trong Sprint 6. All bugs tracked và fixed before production release.

---

# 🧱 **7. Technical Architecture (ở mức high-level)**

## Kiến trúc tổng quát

**Architecture Pattern:** Event-Driven Architecture với Brownfield Integration

**Data Flow:**
```
Shopify → Webhook → Yoom → Rails → Process → Sync → Kintone Apps → Dashboard
```

**Key Principles:**
- **Brownfield Integration:** Tích hợp với Kintone apps hiện có, không rebuild
- **Separation of Concerns:** Rails = backend processing, Kintone = frontend display
- **Idempotency-First:** Zero duplicate orders với Redis-based idempotency
- **Realtime Everything:** Inventory sync, order processing, dashboard updates

## BE stack, FE stack, DB, cloud, CI/CD

**Backend Stack:**
- **Framework:** Ruby on Rails 7.x
- **Job Queue:** Sidekiq (background jobs)
- **Cache/Idempotency:** Redis
- **Database:** MySQL 8.0+
- **Testing:** RSpec
- **Linting:** Rubocop

**Frontend Stack:**
- **BtoB Portal:** Hotwire (Turbo + Stimulus)
- **Kintone Dashboard:** Kintone native (krewDashboard plugin)
- **No separate frontend framework** (server-rendered HTML)

**Database:**
- **MySQL (Rails):** Temporary storage cho webhook processing, idempotency tracking
- **Kintone Apps:** Source of truth cho business data (Orders, Stock, Rewards)

**Cloud & Infrastructure:**
- **Hosting:** AWS EC2
- **Storage:** AWS S3 (audit logs)
- **CI/CD:** GitHub Actions
- **Monitoring:** Sentry (error tracking), New Relic/DataDog (performance)

**Integrations:**
- **Kintone:** REST API (Orders, Stock, Rewards apps)
- **Shopify:** Webhook API
- **Yoom:** Webhook gateway/orchestrator
- **Ship&Co:** API (shipping labels)
- **GMO Bank:** API (payment reconciliation)

## Security

**Authentication:**
- BtoB Portal: Session-based authentication
- Kintone: Kintone native authentication
- API: Token-based authentication

**Authorization:**
- Role-based access control
- Kintone permissions
- BtoB Portal user permissions

**Data Security:**
- HTTPS for all communications
- API credentials stored in environment variables
- Audit trail cho tất cả operations
- Data encryption at rest (AWS)

**Security Best Practices:**
- No secrets in code
- Regular security updates
- Input validation
- SQL injection prevention
- XSS prevention

## API contract (openAPI?)

**Rails API:**
- Webhook endpoints (Shopify → Rails)
- Internal APIs (nếu cần)
- **Note:** No public API cho dashboard (Kintone handles that)

**External APIs:**
- **Kintone REST API:** Documented trong Kintone Developer Portal
- **Shopify Webhook API:** Shopify webhook format
- **Ship&Co API:** Ship&Co API documentation
- **GMO Bank API:** GMO Bank API documentation

**API Documentation:**
- Kintone integration guide: `docs/kintone-integration.md`
- Webhook format: Documented trong code
- API contracts: Documented trong PRD

## Integration cần phía khách cung cấp

**Kintone:**
- ✅ Kintone sandbox access (Before Sprint 1)
- ✅ Kintone production credentials (Before Sprint 4)
- ✅ Kintone app schemas (Before Sprint 2)
- ✅ Kintone API tokens
- ✅ Kintone app permissions setup

**Shopify:**
- ✅ Shopify webhook setup (Before Sprint 2)
- ✅ Shopify API credentials
- ✅ Webhook endpoint configuration
- ✅ HMAC secret key

**Ship&Co:**
- ✅ Ship&Co API credentials (Before Sprint 4)
- ✅ Ship&Co API documentation
- ✅ Test account access

**GMO Bank:**
- ✅ GMO Bank API credentials (Before Sprint 5)
- ✅ GMO Bank API documentation
- ✅ Test environment access

**Yoom:**
- ✅ Yoom webhook gateway configuration
- ✅ Yoom API access (nếu cần)

**Data:**
- ✅ Test data for staging (Before Sprint 2)
- ✅ Sample orders, inventory data
- ✅ Production data access (Before Sprint 6)

👉 **Technical commitment:** Team sử dụng Rails + Sidekiq + Redis + MySQL cho backend, Hotwire cho BtoB Portal, Kintone native cho dashboard. All integrations require credentials và access từ client side. Architecture designed cho scalability và reliability.

---

# 🧨 **8. Risk & Assumption**

## Risk Management

**Risk Identification & Mitigation:**

### Technical Risks

**R1: Kintone API Rate Limits**
- **Risk:** Kintone API có rate limits, có thể block sync nếu exceed
- **Impact:** High - Could block order processing
- **Mitigation:**
  - Implement rate limiting trong Rails
  - Queue throttling cho Kintone API calls
  - Monitor API usage
  - Batch updates khi possible
- **Owner:** Backend Developer
- **Status:** Active monitoring

**R2: Webhook Reliability**
- **Risk:** Shopify webhooks có thể fail hoặc duplicate
- **Impact:** Critical - Could cause duplicate orders
- **Mitigation:**
  - Idempotency mechanism với Redis
  - Retry logic với exponential backoff
  - Dead letter queue cho failed webhooks
  - Monitoring và alerting
- **Owner:** Backend Developer
- **Status:** Mitigated với idempotency design

**R3: Integration Complexity**
- **Risk:** Multiple integrations (Kintone, Shopify, Ship&Co, GMO Bank) có thể complex
- **Impact:** Medium - Could delay development
- **Mitigation:**
  - Adapter pattern để abstract APIs
  - Integration tests cho each API
  - Stub APIs cho development
  - Early integration testing
- **Owner:** Backend Developer
- **Status:** Active

### Process Risks

**R4: Scope Creep**
- **Risk:** Stakeholders request thêm features mid-sprint
- **Impact:** High - Could delay timeline
- **Mitigation:**
  - Clear change request process
  - Mid-sprint feature additions not allowed (except critical)
  - Regular sprint reviews để manage expectations
  - Document all scope changes
- **Owner:** PM
- **Status:** Managed via process

**R5: Stakeholder Feedback Delay**
- **Risk:** Stakeholders không respond kịp thời
- **Impact:** Medium - Could block development
- **Mitigation:**
  - Clear communication rules (24-48h response time)
  - Escalation process
  - Regular sync meetings
  - Document all decisions
- **Owner:** PM
- **Status:** Active

**R6: Kintone App Schema Changes**
- **Risk:** Kintone app schemas có thể change, breaking integration
- **Impact:** Medium - Could require rework
- **Mitigation:**
  - Document all Kintone app schemas
  - Version control cho schema changes
  - Test trên sandbox first
  - Communication với Kintone admin
- **Owner:** PM + Backend Developer
- **Status:** Active

### Resource Risks

**R7: Team Availability**
- **Risk:** Developer có thể unavailable (sick, leave)
- **Impact:** Medium - Could delay sprint
- **Mitigation:**
  - Cross-training
  - Documentation
  - Knowledge sharing
  - Backup plan
- **Owner:** PM
- **Status:** Active

## Assumption (giả định)

**A1: Kintone Access**
- **Assumption:** Client sẽ provide Kintone sandbox access before Sprint 1
- **Validation:** Verify access before Sprint 1
- **If False:** Delay Sprint 1, adjust timeline

**A2: API Credentials**
- **Assumption:** Client sẽ provide all API credentials before respective sprints
  - Shopify: Before Sprint 2
  - Ship&Co: Before Sprint 4
  - GMO Bank: Before Sprint 5
- **Validation:** Verify credentials before integration stories
- **If False:** Delay integration stories, adjust timeline

**A3: Kintone App Schemas**
- **Assumption:** Kintone app schemas sẽ remain stable during development
- **Validation:** Document schemas, communicate changes
- **If False:** May require rework, adjust timeline

**A4: Stakeholder Response Time**
- **Assumption:** Stakeholders will respond within 24-48 hours
- **Validation:** Track response times, escalate if needed
- **If False:** May block development, adjust timeline

**A5: Test Data**
- **Assumption:** Client will provide test data for staging before Sprint 2
- **Validation:** Verify test data availability
- **If False:** May delay testing, adjust timeline

**A6: Operations Team Availability**
- **Assumption:** Operations team will be available for UAT in Sprint 6
- **Validation:** Schedule UAT early, confirm availability
- **If False:** May delay production release

**A7: Third-Party API Stability**
- **Assumption:** Third-party APIs (Shopify, Ship&Co, GMO Bank) will be stable
- **Validation:** Monitor API status, have fallback plans
- **If False:** May require workarounds, adjust timeline

**A8: No Major Scope Changes**
- **Assumption:** Scope will remain relatively stable during MVP development
- **Validation:** Regular sprint reviews, change request process
- **If False:** May require timeline adjustment

👉 **Risk & Assumption commitment:** All risks identified và có mitigation plans. All assumptions documented và validated. Team sẽ communicate risks early và adjust plans nếu needed. Client cần provide access, credentials, và feedback timely để avoid risks.

---

# 📦 **9. Deliverable**

## Source code

**GitHub Repository:**
- Full source code (Rails application)
- Test code (RSpec)
- Configuration files
- Documentation trong code

**Code Quality:**
- Test coverage ≥ 80%
- Code reviewed
- Follows best practices
- Well-documented

## API docs

**Kintone Integration Guide:**
- `docs/kintone-integration.md`
- API usage examples
- Error handling guide
- Rate limiting guide

**Webhook Documentation:**
- Webhook format
- Idempotency mechanism
- Error handling

**Internal API Documentation:**
- Service layer documentation
- Adapter documentation
- Code comments

## User guide

**Operations Team Guide:**
- How to use Kintone apps
- How to use Kintone dashboard
- Common workflows
- Troubleshooting

**BtoB Portal User Guide:**
- How to login
- How to place orders
- How to track orders
- How to view order history

**Admin Guide:**
- System administration
- Monitoring và alerting
- Error handling
- Backup và recovery

## Dữ liệu mẫu

**Test Data:**
- Sample orders
- Sample inventory data
- Test user accounts
- Test scenarios

**Production Data:**
- Data migration guide (nếu cần)
- Data validation rules
- Data retention policies

## Deploy bản test

**Staging Environment:**
- Available từ Sprint 2
- Updated after each sprint
- Access cho operations team
- Test data included

**UAT Environment:**
- Available trong Sprint 6
- Full MVP features
- Production-like data
- Access cho operations team

## Deploy bản production

**Production Deployment:**
- After UAT passed
- All critical bugs fixed
- Full documentation
- Monitoring setup

**Deployment Package:**
- Deployment scripts
- Configuration files
- Environment setup guide
- Rollback procedures

## Tài liệu kiến trúc (tuỳ yêu cầu)

**Architecture Documentation:**
- `docs/architecture.md` - System architecture
- `docs/epic-*-context.md` - Epic technical specs
- `docs/kintone-development-process.md` - Kintone process

**Design Documentation:**
- `docs/prd.md` - Product requirements
- `docs/epics.md` - Epic breakdown
- `docs/practical-development-plan.md` - Development process

**Operations Documentation:**
- `docs/kintone-apps/` - Kintone app schemas
- `docs/kintone-dashboards.md` - Dashboard configurations
- Runbook cho operations

## Additional Deliverables

**Development Process:**
- `docs/practical-development-plan.md` - Development process
- `docs/metrics-tracking-guide.md` - Metrics tracking
- `docs/sprint-checklist.md` - Sprint checklist

**Testing:**
- Test plans
- Test results
- Coverage reports

**Training:**
- Training materials (nếu cần)
- Video tutorials (nếu cần)
- FAQ document

👉 **Deliverable commitment:** Team sẽ deliver full source code, documentation, user guides, và production deployment. All deliverables documented và accessible. Staging environment available từ Sprint 2, production deployment after UAT passed.

---

# 👥 **10. Roles & Responsibilities**

## Product Owner / Scrum Master (PM)

**Name:** Potato

**Responsibilities:**
- Define product requirements (PRD)
- Prioritize product backlog
- Facilitate sprint ceremonies (planning, review, retrospective)
- Remove blockers
- Communicate với stakeholders
- Track progress và metrics (velocity, burndown, lead time, bug rate)
- Approve requirements và change requests
- Approve UI/UX designs
- Review Kintone app configurations

**Time Allocation:**
- 30% - Product planning & requirements
- 30% - Sprint management & ceremonies
- 20% - Stakeholder communication
- 20% - Blockers & coordination

## Backend Developer(s)

**Responsibilities:**
- Implement backend features (Rails)
- Write tests (RSpec) - TDD approach
- Code review
- Deploy to staging/production
- Monitor system health
- Implement Kintone integration
- Implement Shopify webhook processing
- Implement Ship&Co integration
- Implement GMO Bank integration

**Skills Required:**
- Ruby on Rails
- MySQL
- Redis
- Sidekiq
- REST APIs
- Testing (RSpec)
- Kintone API integration

**Deliverables:**
- Working code với tests
- Code review feedback
- Technical documentation (nếu cần)
- Deployment scripts

## Frontend Developer (Optional)

**Responsibilities:**
- Implement BtoB Portal (Hotwire)
- UI/UX implementation
- Frontend testing
- Browser compatibility
- Responsive design

**Skills Required:**
- Hotwire (Turbo + Stimulus)
- HTML/CSS
- JavaScript basics
- Rails views

**Deliverables:**
- BtoB Portal UI
- Responsive design
- Frontend tests

## QA Tester (Optional)

**Responsibilities:**
- Test stories after development
- Report bugs với severity/priority
- Validate acceptance criteria
- Maintain test documentation
- Regression testing
- UAT coordination

**Deliverables:**
- Test plans
- Test results
- Bug reports
- Test documentation

## Client Side - Product Owner (PO)

**Responsibilities:**
- Approve product requirements
- Provide business context
- Review và approve UI/UX
- Provide feedback trong sprint reviews
- Approve change requests
- Provide API access và credentials
- Provide test data
- Participate in UAT

**Key Deliverables từ Client:**
- Kintone access (sandbox + production)
- API credentials (Shopify, Ship&Co, GMO Bank)
- Kintone app schemas
- Test data
- Feedback và approvals (24-48h response time)

## Operations Team

**Responsibilities:**
- Test staging environment
- Provide feedback
- Participate in UAT
- Report bugs
- Validate workflows

## Kintone Administrator (Client Side)

**Responsibilities:**
- Setup Kintone apps (nếu cần)
- Configure Kintone permissions
- Setup Kintone dashboard
- Coordinate Kintone changes

👉 **Roles commitment:** Mỗi role có clear responsibilities và deliverables. PM manages process, Developers implement, QA tests, Client provides access và feedback. Clear communication channels để avoid confusion.

---

# 💰 **11. Scope Out-of-bounds (những thứ *không* làm)**

## Explicitly Out of Scope (MVP)

### Multi-Language Support
- ❌ **Not in MVP:** Multi-language support
- ✅ **In Scope:** Japanese only (current requirement)
- 📝 **Future:** Can be added post-MVP nếu needed

### Mobile App
- ❌ **Not in MVP:** Native mobile app (iOS/Android)
- ✅ **In Scope:** BtoB Portal responsive (mobile-friendly web)
- ✅ **In Scope:** Kintone mobile access (native Kintone mobile)
- 📝 **Future:** Native mobile app can be considered post-MVP

### Custom CMS
- ❌ **Not in MVP:** Custom content management system
- ✅ **In Scope:** Kintone apps (existing, no custom CMS needed)
- 📝 **Future:** Not planned, Kintone handles content

### Data Migration từ Hệ Thống Cũ
- ❌ **Not in MVP:** Import historical data từ old systems
- ✅ **In Scope:** Real-time sync từ Shopify/Kintone going forward
- 📝 **Future:** Data migration can be separate project

### Advanced Analytics
- ❌ **Not in MVP:** Advanced analytics dashboard (beyond Kintone native)
- ✅ **In Scope:** Kintone native dashboards
- ✅ **In Scope:** Basic reporting
- 📝 **Future:** Advanced analytics can be added post-MVP

### Multi-Channel Expansion (Phase 1)
- ❌ **Not in MVP:** Rakuten/Amazon integration
- ✅ **In Scope:** Shopify CREDIE shop
- ✅ **In Scope:** Shopify FIS shop
- 📝 **Future:** Rakuten/Amazon integration planned for Phase 2

### Advanced Warehouse Features
- ❌ **Not in MVP:** Inventory count (kiểm kê), multi-warehouse routing advanced
- ✅ **In Scope:** Basic warehouse management (location tracking, LOT, movements)
- 📝 **Future:** Advanced warehouse features in Phase 5

### Custom Payment Gateway
- ❌ **Not in MVP:** Custom payment gateway integration
- ✅ **In Scope:** GMO Bank API integration (existing)
- 📝 **Future:** Additional payment gateways can be added

### Real-time Chat/Support
- ❌ **Not in MVP:** Real-time chat hoặc support system
- ✅ **In Scope:** Email/phone support (existing process)
- 📝 **Future:** Can be added nếu needed

### Advanced Reporting Engine
- ❌ **Not in MVP:** Custom reporting engine
- ✅ **In Scope:** Kintone native reporting
- ✅ **In Scope:** Basic exports (CSV, Excel)
- 📝 **Future:** Advanced reporting can be added

### Custom Email Templates
- ❌ **Not in MVP:** Custom email template builder
- ✅ **In Scope:** Basic email notifications (templates trong code)
- 📝 **Future:** Template builder can be added

### API for Third-Party Integrations
- ❌ **Not in MVP:** Public API for third-party integrations
- ✅ **In Scope:** Internal APIs for integrations (Shopify, Ship&Co, GMO Bank)
- 📝 **Future:** Public API can be considered post-MVP

### Advanced Security Features
- ❌ **Not in MVP:** Advanced security features (2FA, SSO, etc.)
- ✅ **In Scope:** Basic authentication (session-based)
- ✅ **In Scope:** Kintone native security
- 📝 **Future:** Advanced security can be added

### Performance Optimization (Beyond MVP Requirements)
- ❌ **Not in MVP:** Advanced performance optimization (beyond < 2s requirement)
- ✅ **In Scope:** Meet performance requirements (< 2s webhook processing)
- 📝 **Future:** Further optimization can be done post-MVP

## Change Request Process

**If client requests out-of-scope features:**

1. **Evaluate Impact:**
   - Estimate effort (story points)
   - Assess timeline impact
   - Assess cost impact

2. **Decision:**
   - **Accept:** Add to backlog, prioritize, adjust timeline
   - **Defer:** Add to backlog, schedule for post-MVP
   - **Reject:** Explain why out of scope

3. **Document:**
   - All scope changes documented
   - Timeline adjustments communicated
   - Cost implications (nếu có)

👉 **Scope commitment:** MVP focus vào core automation (Order Processing, Fulfillment, Warehouse Core, BtoB Portal, Financial Automation). Advanced features, mobile app, multi-language, data migration, và custom systems are explicitly out of scope for MVP. These can be considered post-MVP based on business needs và priorities.