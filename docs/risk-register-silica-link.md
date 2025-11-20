# Risk Register - Silica-link Project

**Date:** 2025-11-20  
**Project:** Silica-link  
**Prepared By:** Potato  
**Framework:** Based on project-kickoff/risk.md

---

## Risk Analysis Summary

**Total Risks Identified:** 18 risks  
**High Priority (Score 9-12):** 6 risks  
**Medium Priority (Score 6-8):** 9 risks  
**Low Priority (Score 3-5):** 3 risks

---

## 🔴 HIGH PRIORITY RISKS (Score 9-12)

### RISK-001: Redis Single Point of Failure → Idempotency Lost → Duplicate Orders

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Idempotency mechanism phụ thuộc hoàn toàn vào Redis. Nếu Redis down hoặc connection lost, idempotency keys bị mất |
| **Risk Event** | Redis failure trong quá trình process webhook → không thể check idempotency → duplicate orders được tạo trong Kintone |
| **Impact** | **Quality + Scope:** Zero duplicate orders là requirement không thể thỏa hiệp. Duplicate orders → financial loss, customer complaints, operations team mất trust |
| **Probability** | Medium (2) - Redis có thể down do infrastructure issues, memory overflow, hoặc network problems |
| **Impact** | High (3) - Critical business requirement violation |
| **Risk Score** | **6** → **HIGH** (sau khi xem xét lại: Medium-High) |
| **Owner** | Tech Lead / DevOps |
| **Mitigation Strategy** | **Reduce** |
| **Mitigation Plan** | |
| **Action** | Implement database fallback cho idempotency tracking. Dual tracking: Redis (fast path) + MySQL (persistent). Monitor Redis availability và auto-switch to database fallback |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Implement ngay trong Epic 1 (Story 1.3: Core Infrastructure Services). Trigger: Redis connection errors > 1% of requests |
| **Expected Result** | Idempotency vẫn hoạt động khi Redis down. Zero duplicate orders trong production |
| **Deadline/Review** | Review trong Story 1.3 implementation. Test failover scenario trước khi deploy |

---

### RISK-002: Kintone API Rate Limits → Orders Bị Drop → Operations Team Phải Manual

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Kintone API có rate limits (thường 10 requests/second). Nếu không handle đúng, API calls bị reject → orders không được sync vào Kintone |
| **Risk Event** | Peak load (1000+ orders/hour) → API rate limit exceeded → orders bị drop → operations team phải nhập manual → mất value proposition |
| **Impact** | **Schedule + Quality:** Operations team vẫn phải làm manual, không đạt success criteria "zero manual entry". Timeline bị delay do phải fix integration issues |
| **Probability** | High (3) - Rate limits là constraint thực tế của Kintone API, đặc biệt trong peak hours |
| **Impact** | High (3) - Core value proposition bị mất |
| **Risk Score** | **9** → **HIGH** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Mitigation Plan** | |
| **Action** | Implement queue throttling với exponential backoff. Circuit breakers cho Kintone API. Priority queues cho critical operations. Monitor API call rate và queue depth |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Implement trong Epic 2 (Story 2.9: Integration Layer - Kintone Adapter). Trigger: Kintone API rate limit errors > 0.1% of requests |
| **Expected Result** | API calls được throttle properly. Zero orders bị drop do rate limits. Queue depth < 100 jobs |
| **Deadline/Review** | Review trong Story 2.9. Load test với 1000+ orders/hour trước khi deploy |

---

### RISK-003: Inventory Sync Lag → Overselling → Customer Complaints

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Physical inventory (MySQL) và logical inventory (Kintone) không sync realtime. Có delay giữa khi inventory thay đổi và khi được sync |
| **Risk Event** | 2 orders cùng lúc check inventory → cả 2 đều thấy có hàng → overselling → customer complaints, financial loss |
| **Impact** | **Quality + Scope:** Overselling vi phạm business requirement. Customer complaints → reputation damage. Financial loss từ refunds |
| **Probability** | Medium (2) - Race conditions có thể xảy ra trong peak hours, đặc biệt với high-volume SKUs |
| **Impact** | High (3) - Business-critical issue |
| **Risk Score** | **6** → **HIGH** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Mitigation Plan** | |
| **Action** | Implement inventory reservation/locking mechanism. Event-driven sync với immediate updates (không batch). Validation layer giữa physical và logical inventory. Daily reconciliation job |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Implement trong Epic 2 (Story 2.14: Inventory Reservation/Locking Mechanism). Trigger: Inventory sync lag > 5 seconds |
| **Expected Result** | Inventory sync realtime (< 500ms). Zero overselling incidents. Inventory accuracy 99.5%+ |
| **Deadline/Review** | Review trong Story 2.14. Test race condition scenarios trước khi deploy |

---

### RISK-004: Operations Team Không Trust Hệ Thống → Vẫn Làm Manual Song Song → Không Đạt Success Criteria

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Operations team đã quen với manual process. Nếu hệ thống có bugs hoặc không reliable, họ sẽ không trust và tiếp tục làm manual song song |
| **Risk Event** | Operations team vẫn nhập orders manual vào Kintone song song với automated system → không đạt success criteria "zero manual entry" → không đạt ROI |
| **Impact** | **Scope + Quality:** Success criteria không đạt. ROI không đạt. Operations team không hài lòng (user satisfaction < 70%) |
| **Probability** | Medium (2) - User adoption là challenge phổ biến trong automation projects |
| **Impact** | High (3) - Project success criteria bị vi phạm |
| **Risk Score** | **6** → **HIGH** |
| **Owner** | PM / Product Owner |
| **Mitigation Strategy** | **Reduce + Transfer** |
| **Mitigation Plan** | |
| **Action** | Phased rollout: Start với 1 shop (CREDIE shop), validate reliability, rồi mở rộng. Comprehensive training cho operations team. Support hotline. Track usage metrics. Show value early (dashboard realtime) |
| **Owner** | PM |
| **Timing/Trigger** | Start từ Epic 1 completion. Trigger: Usage metrics < 80% sau 2 tuần rollout |
| **Expected Result** | Operations team trust hệ thống. 100% orders qua automated system. User satisfaction 90%+ |
| **Deadline/Review** | Review mỗi tuần trong first month. Escalate nếu usage < 80% sau 2 tuần |

---

### RISK-005: Sidekiq Queue Overflow → Webhooks Timeout → Shopify Retry → Duplicate Processing

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Peak load (sales events) → webhook volume tăng đột biến → Sidekiq queue backlog → webhooks timeout → Shopify retry → duplicate webhooks |
| **Risk Event** | Queue depth > 1000 jobs → processing delay > 30 seconds → Shopify timeout và retry → duplicate webhooks → duplicate orders (nếu idempotency fail) |
| **Impact** | **Quality + Schedule:** Duplicate orders. System performance degradation. Operations team phải manual fix |
| **Probability** | Medium (2) - Sales events có thể tạo peak load bất ngờ |
| **Impact** | High (3) - Critical quality issue |
| **Risk Score** | **6** → **HIGH** |
| **Owner** | Tech Lead / DevOps |
| **Mitigation Strategy** | **Reduce** |
| **Mitigation Plan** | |
| **Action** | Auto-scaling Sidekiq workers based on queue depth. Priority queues cho critical operations. Queue monitoring với alerts. Load testing với 1000+ orders/hour |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Implement trong Epic 2 (Story 2.20: Webhook Queue Monitoring & Auto-scaling). Trigger: Queue depth > 500 jobs |
| **Expected Result** | Queue depth luôn < 100 jobs. Zero timeouts. Webhook processing latency < 2 seconds (p95) |
| **Deadline/Review** | Review trong Story 2.20. Load test trước mỗi major release |

---

### RISK-006: Kintone API Downtime → Không Thể Process Orders → Business Operations Halted

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Kintone là source of truth cho business data. Nếu Kintone API down (maintenance, outage), hệ thống không thể create orders hoặc sync data |
| **Risk Event** | Kintone API downtime → orders không được sync → operations team không thể process orders → business operations halted |
| **Impact** | **Schedule + Quality:** Business operations bị gián đoạn. Orders bị delay. Customer complaints |
| **Probability** | Low (1) - Kintone có uptime 99.9%+, nhưng maintenance windows và occasional outages vẫn có thể xảy ra |
| **Impact** | High (3) - Business-critical |
| **Risk Score** | **3** → **MEDIUM** (nhưng cần monitor) |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce + Transfer** |
| **Mitigation Plan** | |
| **Action** | Circuit breakers cho Kintone API. Fallback mechanism: Store orders trong MySQL khi Kintone down, sync later. Health checks với alerts. Manual fallback process cho operations team |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Implement trong Epic 2 (Story 2.21: Kintone API Health Check & Fallback). Trigger: Kintone API errors > 1% of requests |
| **Expected Result** | System vẫn hoạt động khi Kintone down (với degraded mode). Orders được queue và sync khi Kintone recover |
| **Deadline/Review** | Review trong Story 2.21. Test fallback scenario trước khi deploy |

---

## 🟠 MEDIUM PRIORITY RISKS (Score 6-8)

### RISK-007: Webhook Duplicate Processing → Duplicate Orders

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Webhook replay attacks hoặc Shopify retry → duplicate webhooks → nếu idempotency mechanism fail → duplicate orders |
| **Risk Event** | Attacker replay old webhooks hoặc Shopify retry do timeout → idempotency check fail → duplicate orders được tạo |
| **Impact** | **Quality:** Duplicate orders → financial loss, customer complaints |
| **Probability** | Low (1) - Idempotency mechanism đã được design, nhưng có thể fail trong edge cases |
| **Impact** | High (3) - Critical quality issue |
| **Risk Score** | **3** → **MEDIUM** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Mitigation Plan** | |
| **Action** | HMAC verification mandatory. Database fallback cho idempotency. Webhook timestamp validation. Monitor duplicate detection |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Implement trong Epic 2 (Story 2.22: Webhook Replay Protection). Trigger: Duplicate orders detected |
| **Expected Result** | Zero duplicate orders trong production |
| **Deadline/Review** | Review trong Story 2.22. Test replay attack scenarios |

---

### RISK-008: Data Loss (Webhook) → Orders Bị Mất → Không Thể Recover

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Webhook được receive nhưng chưa được process → system crash hoặc queue loss → webhook data bị mất → orders không được tạo |
| **Risk Event** | Webhook received nhưng chưa persist → system crash → webhook data lost → order không được tạo → customer complaint |
| **Impact** | **Quality:** Lost orders → customer complaints, revenue loss |
| **Probability** | Low (1) - Webhooks được store trong S3, nhưng có thể fail trong edge cases |
| **Impact** | High (3) - Business-critical |
| **Risk Score** | **3** → **MEDIUM** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Mitigation Plan** | |
| **Action** | Store webhook payload vào S3 TRƯỚC KHI process. Idempotency check với S3 backup. Audit trail cho mọi webhook events |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Implement trong Epic 2 (Story 2.3: Webhook Event Logging to S3). Trigger: Webhook processing failures |
| **Expected Result** | Zero webhook data loss. All webhooks có audit trail trong S3 |
| **Deadline/Review** | Review trong Story 2.3. Test crash recovery scenarios |

---

### RISK-009: Security Breach (API Keys) → Unauthorized Access → Data Leak

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | API keys (Kintone, Shopify, GMO Bank) stored in plaintext config → nếu bị leak → attacker có thể access sensitive data |
| **Risk Event** | API keys bị leak (git commit, config file exposed) → attacker access Kintone data → data breach → compliance violation |
| **Impact** | **Quality + Scope:** Data breach → compliance violation, reputation damage, legal issues |
| **Probability** | Low (1) - Security best practices đã được follow, nhưng human error vẫn có thể xảy ra |
| **Impact** | High (3) - Critical security issue |
| **Risk Score** | **3** → **MEDIUM** |
| **Owner** | Tech Lead / Security Lead |
| **Mitigation Strategy** | **Avoid + Reduce** |
| **Mitigation Plan** | |
| **Action** | Encrypt API keys. Use environment variables (không commit vào git). API key rotation policy. Audit trail cho mọi API calls. Security review cho mọi config changes |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Implement trong Epic 1 (Story 1.1: Project Setup). Trigger: Security audit findings |
| **Expected Result** | Zero API keys in plaintext. All API keys encrypted và rotated regularly |
| **Deadline/Review** | Review trong Story 1.1. Security audit mỗi quarter |

---

### RISK-010: BtoB Portal Adoption Failure → Đại Lý Không Sử dụng → Không Đạt Business Value

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | BtoB Portal quá phức tạp hoặc không user-friendly → đại lý không sử dụng → vẫn gửi email/FAX → không đạt business value |
| **Risk Event** | BtoB Portal adoption rate < 50% → đại lý vẫn prefer email/FAX → operations team vẫn phải nhập manual → không đạt success criteria |
| **Impact** | **Scope + Quality:** Business value không đạt. Operations team vẫn phải nhập manual cho BtoB orders |
| **Probability** | Medium (2) - User adoption là challenge, đặc biệt với non-tech users |
| **Impact** | Medium (2) - Business value bị giảm |
| **Risk Score** | **4** → **MEDIUM** |
| **Owner** | PM / UX Designer |
| **Mitigation Strategy** | **Reduce** |
| **Mitigation Plan** | |
| **Action** | UX design focus: Simple, mobile-friendly, intuitive. User testing với đại lý trước khi launch. Onboarding support. Incentives cho early adopters. Phased rollout |
| **Owner** | PM |
| **Timing/Trigger** | Start từ Epic 5 (BtoB Portal). Trigger: Adoption rate < 50% sau 1 tháng |
| **Expected Result** | BtoB Portal adoption rate 80%+. 70%+ orders từ portal |
| **Deadline/Review** | Review mỗi tuần trong first month. User feedback survey |

---

### RISK-011: Payment Reconciliation Errors → Financial Discrepancies → Accounting Issues

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | GMO Bank API integration có bugs hoặc data mismatch → payment reconciliation sai → financial discrepancies → accounting issues |
| **Risk Event** | Payment reconciliation algorithm có bug → payments không match với orders → financial discrepancies → accounting team phải manual fix → không đạt automation goal |
| **Impact** | **Quality + Schedule:** Financial discrepancies → accounting issues. Manual reconciliation vẫn cần → không đạt success criteria |
| **Probability** | Medium (2) - Payment reconciliation là complex logic, có thể có edge cases |
| **Impact** | Medium (2) - Business-critical nhưng có thể fix |
| **Risk Score** | **4** → **MEDIUM** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Mitigation Plan** | |
| **Action** | Comprehensive testing cho payment reconciliation logic. Manual review process cho first month. Daily reconciliation reports. Alert on discrepancies |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Implement trong Epic 6 (Story 6.1: Auto Payment Reconciliation). Trigger: Payment mismatches > 0.1% |
| **Expected Result** | Payment reconciliation accuracy 99.9%+. Zero financial discrepancies |
| **Deadline/Review** | Review trong Story 6.1. Daily reconciliation reports trong first month |

---

### RISK-012: Performance Degradation → Dashboard Slow → Operations Team Không Thấy Value

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Dashboard không realtime (< 1 second refresh) → operations team không thấy value → không trust hệ thống |
| **Risk Event** | Dashboard refresh time > 1 second → operations team không thấy realtime updates → không trust hệ thống → không sử dụng |
| **Impact** | **Quality:** User experience kém → adoption failure |
| **Probability** | Medium (2) - Performance issues có thể xảy ra với large datasets |
| **Impact** | Low (1) - Có thể optimize |
| **Risk Score** | **2** → **LOW** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Mitigation Plan** | |
| **Action** | Redis caching cho dashboard data. Database query optimization. Horizontal scaling. Performance monitoring |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Implement trong Epic 7 (Dashboard). Trigger: Dashboard refresh time > 1 second |
| **Expected Result** | Dashboard refresh time < 1 second. Realtime updates |
| **Deadline/Review** | Review trong Epic 7. Performance testing trước khi deploy |

---

### RISK-013: Scope Creep → Timeline Delay → Budget Overrun

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | CR nhiều hoặc requirements thay đổi → scope creep → timeline delay → budget overrun |
| **Risk Event** | Operations team request thêm features hoặc change requirements → scope creep → timeline delay → không đạt deadline |
| **Impact** | **Schedule + Cost:** Timeline delay, budget overrun |
| **Probability** | Medium (2) - Scope creep là common trong software projects |
| **Impact** | Medium (2) - Có thể manage |
| **Risk Score** | **4** → **MEDIUM** |
| **Owner** | PM |
| **Mitigation Strategy** | **Avoid + Reduce** |
| **Mitigation Plan** | |
| **Action** | Freeze scope trong mỗi sprint. CR process rõ ràng. Baseline requirements trong PRD. Change request approval process |
| **Owner** | PM |
| **Timing/Trigger** | Start từ Epic 1. Trigger: CR requests > 3 per sprint |
| **Expected Result** | Scope được control. Timeline on track |
| **Deadline/Review** | Review mỗi sprint. Escalate nếu CR > 3 per sprint |

---

### RISK-014: Resource Unavailability → Development Delay

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Developer chính nghỉ phép hoặc bị assign dự án khác → development delay → timeline delay |
| **Risk Event** | Key developer unavailable → critical tasks bị delay → timeline delay |
| **Impact** | **Schedule:** Timeline delay |
| **Probability** | Low (1) - Resource planning đã được consider |
| **Impact** | Medium (2) - Có thể manage với backup |
| **Risk Score** | **2** → **LOW** |
| **Owner** | PM |
| **Mitigation Strategy** | **Reduce + Transfer** |
| **Mitigation Plan** | |
| **Action** | Knowledge sharing sessions. Documentation đầy đủ. Backup developer cho critical tasks. Cross-training |
| **Owner** | PM |
| **Timing/Trigger** | Start từ Epic 1. Trigger: Key developer unavailable > 3 days |
| **Expected Result** | Development không bị delay khi có resource unavailability |
| **Deadline/Review** | Review mỗi sprint. Update resource plan |

---

### RISK-015: Integration API Changes → Breaking Changes → System Failure

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | External APIs (Shopify, Ship&Co, GMO Bank) có breaking changes → integration fail → system failure |
| **Risk Event** | API vendor release breaking changes → integration code fail → system không hoạt động → business operations halted |
| **Impact** | **Quality + Schedule:** System failure → business operations halted → timeline delay để fix |
| **Probability** | Low (1) - APIs thường có versioning, nhưng breaking changes vẫn có thể xảy ra |
| **Impact** | High (3) - Business-critical |
| **Risk Score** | **3** → **MEDIUM** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce + Transfer** |
| **Mitigation Plan** | |
| **Action** | API versioning strategy. Abstraction layer (Adapter Pattern). Monitor API changes. Version pinning. Test với API updates |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Implement trong Epic 2 (Story 2.24: Kintone API Versioning). Trigger: API breaking changes detected |
| **Expected Result** | System vẫn hoạt động khi API có changes. Quick recovery time |
| **Deadline/Review** | Review trong Story 2.24. Monitor API changelogs |

---

## 🟡 LOW PRIORITY RISKS (Score 3-5)

### RISK-016: Communication Delay → Feedback Chậm → Development Delay

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Stakeholders (CREDIE, MediaBirth) không online hoặc feedback chậm → development delay |
| **Risk Event** | Stakeholder feedback delay > 3 days → development blocked → timeline delay |
| **Impact** | **Schedule:** Timeline delay |
| **Probability** | Low (1) - Communication channels đã được establish |
| **Impact** | Low (1) - Có thể manage |
| **Risk Score** | **1** → **LOW** |
| **Owner** | PM |
| **Mitigation Strategy** | **Reduce** |
| **Mitigation Plan** | |
| **Action** | Regular sync meetings. Clear communication channels. Auto-reminders. Escalation process |
| **Owner** | PM |
| **Timing/Trigger** | Start từ Epic 1. Trigger: Feedback delay > 3 days |
| **Expected Result** | Feedback được provide trong 24-48 hours |
| **Deadline/Review** | Review mỗi sprint |

---

### RISK-017: Test Coverage Insufficient → Bugs Leak to Production → Quality Issues

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Test coverage không đủ → bugs leak to production → quality issues → customer complaints |
| **Risk Event** | Test coverage < 70% → critical bugs không được catch → production issues → customer complaints |
| **Impact** | **Quality:** Production bugs → customer complaints, reputation damage |
| **Probability** | Low (1) - Test strategy đã được plan |
| **Impact** | Medium (2) - Có thể fix |
| **Risk Score** | **2** → **LOW** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Mitigation Plan** | |
| **Action** | Test coverage target 80%+. Automated testing. Code review. QA process |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Start từ Epic 1. Trigger: Test coverage < 70% |
| **Expected Result** | Test coverage 80%+. Zero critical bugs in production |
| **Deadline/Review** | Review mỗi sprint. Test coverage reports |

---

### RISK-018: Documentation Insufficient → Knowledge Loss → Maintenance Issues

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Documentation không đủ → knowledge loss khi developer rời dự án → maintenance issues |
| **Risk Event** | Key developer rời dự án → documentation không đủ → new developer không hiểu system → maintenance issues → timeline delay |
| **Impact** | **Schedule + Quality:** Maintenance issues → timeline delay |
| **Probability** | Low (1) - Documentation đã được plan |
| **Impact** | Low (1) - Có thể manage |
| **Risk Score** | **1** → **LOW** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Mitigation Plan** | |
| **Action** | Comprehensive documentation. Code comments. Architecture documentation. Knowledge sharing sessions |
| **Owner** | Tech Lead |
| **Timing/Trigger** | Start từ Epic 1. Trigger: Documentation gaps identified |
| **Expected Result** | Documentation đầy đủ. New developer có thể onboard trong 1 tuần |
| **Deadline/Review** | Review mỗi sprint. Documentation audit |

---

## Risk Summary by Category

### Technical Risks (8 risks)
- RISK-001: Redis Single Point of Failure
- RISK-002: Kintone API Rate Limits
- RISK-003: Inventory Sync Lag
- RISK-005: Sidekiq Queue Overflow
- RISK-006: Kintone API Downtime
- RISK-007: Webhook Duplicate Processing
- RISK-008: Data Loss (Webhook)
- RISK-015: Integration API Changes

### Quality Risks (5 risks)
- RISK-001: Redis Single Point of Failure
- RISK-003: Inventory Sync Lag
- RISK-004: Operations Team Adoption Failure
- RISK-007: Webhook Duplicate Processing
- RISK-011: Payment Reconciliation Errors

### Schedule Risks (4 risks)
- RISK-002: Kintone API Rate Limits
- RISK-004: Operations Team Adoption Failure
- RISK-013: Scope Creep
- RISK-014: Resource Unavailability

### Scope Risks (2 risks)
- RISK-004: Operations Team Adoption Failure
- RISK-010: BtoB Portal Adoption Failure
- RISK-013: Scope Creep

### Resource Risks (1 risk)
- RISK-014: Resource Unavailability

### Communication Risks (1 risk)
- RISK-016: Communication Delay

---

## Top 5 Risks Cần Address Ngay

1. **RISK-002: Kintone API Rate Limits** (Score: 9) - HIGH
2. **RISK-001: Redis Single Point of Failure** (Score: 6) - HIGH
3. **RISK-003: Inventory Sync Lag** (Score: 6) - HIGH
4. **RISK-004: Operations Team Adoption Failure** (Score: 6) - HIGH
5. **RISK-005: Sidekiq Queue Overflow** (Score: 6) - HIGH

---

## Risk Monitoring Schedule

- **Weekly Review:** Tất cả High và Medium priority risks
- **Sprint Review:** Risk status update trong sprint retrospective
- **Monthly Review:** Full risk register review và update
- **Quarterly Review:** Risk library update và lessons learned

---

**Document Status:** Initial Risk Register  
**Next Review Date:** 2025-11-27 (Weekly)  
**Owner:** PM (Potato)

