# Risk Register - Silica-link Project (Version 2)

**Date:** 2025-11-20  
**Project:** Silica-link  
**Prepared By:** Potato  
**Framework:** Based on project-kickoff/risk.md (Updated with new analysis criteria)

---

## Phân tích Risk theo 4 Nhóm Chính

### a) Technical Risk
**Đặc điểm:** Công nghệ mới, hệ thống phức tạp, tích hợp third-party, performance, scaling, bảo mật

### b) Product / Requirement Risk  
**Đặc điểm:** Requirement mơ hồ, thay đổi liên tục, không có spec rõ, PO/client không phản hồi kịp

### c) Human / Team Risk
**Đặc điểm:** Thiếu nhân lực, dev chưa đủ skill, chuyển team, communication kém, bus factor

### d) Schedule / Management Risk
**Đặc điểm:** Deadline không thực tế, task ước lượng sai, scope creep, không có workflow rõ ràng

---

## Risk Analysis Summary

**Total Risks Identified:** 25 risks (tăng từ 18)  
**High Priority (Score 6-9):** 8 risks  
**Medium Priority (Score 3-5):** 12 risks  
**Low Priority (Score 1-2):** 5 risks

**Phân loại theo 4 nhóm:**
- **Technical Risk:** 12 risks
- **Product/Requirement Risk:** 4 risks
- **Human/Team Risk:** 5 risks
- **Schedule/Management Risk:** 4 risks

---

## 🔴 HIGH PRIORITY RISKS (Score 6-9)

### RISK-001: Kintone API Rate Limits → Orders Bị Drop → Operations Team Phải Manual
**Nhóm:** Technical Risk  
**Known-Unknown:** ✅ (Biết có rate limits, chưa biết cách handle hiệu quả)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Kintone API có rate limits (10 requests/second). Peak load (1000+ orders/hour) có thể vượt limit |
| **Risk Event** | API rate limit exceeded → orders bị drop → operations team phải nhập manual → mất value proposition |
| **Impact** | **Schedule + Quality:** Không đạt success criteria "zero manual entry". Timeline delay |
| **Probability** | High (3) - Rate limits là constraint thực tế, đặc biệt trong peak hours |
| **Impact** | High (3) - Core value proposition bị mất |
| **Risk Score** | **9** → **HIGH** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Queue throttling với exponential backoff. Circuit breakers. Priority queues. Monitor API call rate |
| **Timing/Trigger** | Epic 2 (Story 2.9, 2.12). Trigger: Rate limit errors > 0.1% |
| **Expected Result** | Zero orders bị drop do rate limits. Queue depth < 100 jobs |
| **POC Required?** | ✅ Yes - Test với 1000+ orders/hour trong staging |

---

### RISK-002: Redis Single Point of Failure → Idempotency Lost → Duplicate Orders
**Nhóm:** Technical Risk  
**Known-Unknown:** ✅ (Biết Redis có thể down, chưa test failover scenario)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Idempotency mechanism phụ thuộc hoàn toàn vào Redis. Redis có thể down do infrastructure issues |
| **Risk Event** | Redis failure → không thể check idempotency → duplicate orders được tạo |
| **Impact** | **Quality:** Vi phạm requirement "zero duplicate orders". Financial loss, customer complaints |
| **Probability** | Medium (2) - Redis có thể down do memory overflow, network problems |
| **Impact** | High (3) - Critical business requirement violation |
| **Risk Score** | **6** → **HIGH** |
| **Owner** | Tech Lead / DevOps |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Database fallback cho idempotency. Dual tracking: Redis (fast) + MySQL (persistent). Auto-switch mechanism |
| **Timing/Trigger** | Epic 1 (Story 1.3). Trigger: Redis connection errors > 1% |
| **Expected Result** | Idempotency vẫn hoạt động khi Redis down. Zero duplicate orders |
| **POC Required?** | ✅ Yes - Test failover scenario với Redis down |

---

### RISK-003: Inventory Sync Lag → Race Conditions → Overselling
**Nhóm:** Technical Risk  
**Known-Unknown:** ✅ (Biết có race condition risk, chưa biết scale của vấn đề)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Physical inventory (MySQL) và logical inventory (Kintone) không sync realtime. Có delay |
| **Risk Event** | 2 orders cùng lúc check inventory → cả 2 thấy có hàng → overselling → customer complaints |
| **Impact** | **Quality:** Overselling → financial loss, reputation damage |
| **Probability** | Medium (2) - Race conditions có thể xảy ra trong peak hours |
| **Impact** | High (3) - Business-critical issue |
| **Risk Score** | **6** → **HIGH** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Inventory reservation/locking mechanism. Event-driven sync. Validation layer. Daily reconciliation |
| **Timing/Trigger** | Epic 2 (Story 2.14). Trigger: Inventory sync lag > 5 seconds |
| **Expected Result** | Inventory sync realtime (< 500ms). Zero overselling. Accuracy 99.5%+ |
| **POC Required?** | ✅ Yes - Test race condition với concurrent orders |

---

### RISK-004: Operations Team Không Trust Hệ Thống → Vẫn Làm Manual Song Song
**Nhóm:** Human/Team Risk  
**Known-Unknown:** ✅ (Biết user adoption là challenge, chưa biết mức độ resistance)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Operations team đã quen với manual process. Nếu hệ thống có bugs, họ sẽ không trust |
| **Risk Event** | Team vẫn nhập orders manual song song → không đạt "zero manual entry" → không đạt ROI |
| **Impact** | **Scope + Quality:** Success criteria không đạt. ROI không đạt. User satisfaction < 70% |
| **Probability** | Medium (2) - User adoption là challenge phổ biến |
| **Impact** | High (3) - Project success criteria bị vi phạm |
| **Risk Score** | **6** → **HIGH** |
| **Owner** | PM / Product Owner |
| **Mitigation Strategy** | **Reduce + Transfer** |
| **Action** | Phased rollout (1 shop trước). Comprehensive training. Support hotline. Track usage metrics. Show value early |
| **Timing/Trigger** | Start từ Epic 1 completion. Trigger: Usage < 80% sau 2 tuần |
| **Expected Result** | 100% orders qua automated system. User satisfaction 90%+ |
| **POC Required?** | ❌ No - Nhưng cần user testing với operations team |

---

### RISK-005: Sidekiq Queue Overflow → Webhooks Timeout → Duplicate Processing
**Nhóm:** Technical Risk  
**Known-Unknown:** ✅ (Biết có queue overflow risk, chưa test với peak load)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Peak load (sales events) → webhook volume tăng đột biến → Sidekiq queue backlog |
| **Risk Event** | Queue depth > 1000 → processing delay > 30s → Shopify timeout và retry → duplicate orders |
| **Impact** | **Quality:** Duplicate orders. Performance degradation |
| **Probability** | Medium (2) - Sales events có thể tạo peak load bất ngờ |
| **Impact** | High (3) - Critical quality issue |
| **Risk Score** | **6** → **HIGH** |
| **Owner** | Tech Lead / DevOps |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Auto-scaling Sidekiq workers. Priority queues. Queue monitoring với alerts. Load testing |
| **Timing/Trigger** | Epic 2 (Story 2.20). Trigger: Queue depth > 500 |
| **Expected Result** | Queue depth < 100. Zero timeouts. Latency < 2s (p95) |
| **POC Required?** | ✅ Yes - Load test với 1000+ orders/hour |

---

### RISK-006: GMO Bank API Integration Chưa Confirm → Payment Reconciliation Delay
**Nhóm:** Product/Requirement Risk + Technical Risk  
**Known-Unknown:** ✅ (Biết cần integrate, chưa có API docs đầy đủ)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | GMO Bank API integration chưa được confirm. API docs chưa đầy đủ. Chưa có test account |
| **Risk Event** | API integration fail hoặc delay → payment reconciliation không hoạt động → không đạt automation goal |
| **Impact** | **Schedule + Quality:** Epic 6 delay. Manual reconciliation vẫn cần |
| **Probability** | Medium (2) - Third-party API có thể có issues hoặc changes |
| **Impact** | High (3) - Business-critical feature |
| **Risk Score** | **6** → **HIGH** |
| **Owner** | Tech Lead / PM |
| **Mitigation Strategy** | **Reduce + Transfer** |
| **Action** | Request API docs và test account sớm. POC integration trước Epic 6. Mock API fallback. Escalate nếu API không available |
| **Timing/Trigger** | Start từ Epic 1. Trigger: API docs chưa có trước Epic 6 start |
| **Expected Result** | API integration hoạt động. Payment reconciliation automated |
| **POC Required?** | ✅ **YES - CRITICAL** - POC 2-3 ngày trước Epic 6 |

---

### RISK-007: Ship&Co API Chưa Confirm → Shipping Labels Không Generate Được
**Nhóm:** Product/Requirement Risk + Technical Risk  
**Known-Unknown:** ✅ (Biết cần integrate, chưa có API details)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Ship&Co API integration chưa được confirm. API docs, authentication, test environment chưa có |
| **Risk Event** | API integration fail → shipping labels không generate → operations team phải manual → không đạt automation |
| **Impact** | **Schedule + Quality:** Epic 3 delay. Manual process vẫn cần |
| **Probability** | Medium (2) - Third-party API có thể có issues |
| **Impact** | High (3) - Core fulfillment feature |
| **Risk Score** | **6** → **HIGH** |
| **Owner** | Tech Lead / PM |
| **Mitigation Strategy** | **Reduce + Transfer** |
| **Action** | Request API docs và test account sớm. POC integration trước Epic 3. Mock API fallback. Escalate nếu API không available |
| **Timing/Trigger** | Start từ Epic 1. Trigger: API docs chưa có trước Epic 3 start |
| **Expected Result** | API integration hoạt động. Shipping labels auto-generated |
| **POC Required?** | ✅ **YES - CRITICAL** - POC 2-3 ngày trước Epic 3 |

---

### RISK-008: Yoom Gateway Chưa Confirm → Webhook Pipeline Không Hoạt Động
**Nhóm:** Product/Requirement Risk + Technical Risk  
**Unknown-Unknown:** ⚠️ (Chưa biết Yoom gateway hoạt động như thế nào)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Yoom gateway là orchestrator cho webhook pipeline. Chưa có documentation đầy đủ về cách Yoom forward webhooks |
| **Risk Event** | Yoom gateway không hoạt động đúng → webhooks không đến Rails → orders không được process |
| **Impact** | **Quality + Schedule:** Core webhook pipeline fail → toàn bộ system không hoạt động |
| **Probability** | Medium (2) - Gateway là critical component, nhưng chưa được test |
| **Impact** | High (3) - System-critical |
| **Risk Score** | **6** → **HIGH** |
| **Owner** | Tech Lead / PM |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Request Yoom gateway documentation sớm. POC với test webhooks. Direct Shopify webhook fallback nếu Yoom fail |
| **Timing/Trigger** | Start từ Epic 1. Trigger: Yoom docs chưa có trước Epic 2 start |
| **Expected Result** | Yoom gateway hoạt động đúng. Webhook pipeline stable |
| **POC Required?** | ✅ **YES - CRITICAL** - POC 2-3 ngày trước Epic 2 |

---

## 🟠 MEDIUM PRIORITY RISKS (Score 3-5)

### RISK-009: Kintone API Downtime → Business Operations Halted
**Nhóm:** Technical Risk  
**Known-Unknown:** ✅ (Biết có downtime risk, chưa test fallback)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Kintone là source of truth. Nếu API down (maintenance, outage), hệ thống không thể process orders |
| **Risk Event** | Kintone API downtime → orders không được sync → business operations halted |
| **Impact** | **Schedule + Quality:** Business operations bị gián đoạn |
| **Probability** | Low (1) - Kintone có uptime 99.9%+, nhưng maintenance windows vẫn có |
| **Impact** | High (3) - Business-critical |
| **Risk Score** | **3** → **MEDIUM** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce + Transfer** |
| **Action** | Circuit breakers. Fallback: Store orders trong MySQL, sync later. Health checks. Manual fallback process |
| **Timing/Trigger** | Epic 2 (Story 2.21). Trigger: API errors > 1% |
| **Expected Result** | System vẫn hoạt động khi Kintone down (degraded mode) |
| **POC Required?** | ✅ Yes - Test fallback scenario |

---

### RISK-010: Webhook Duplicate Processing → Duplicate Orders
**Nhóm:** Technical Risk  
**Known-Unknown:** ✅ (Biết có replay risk, chưa test protection)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Webhook replay attacks hoặc Shopify retry → duplicate webhooks |
| **Risk Event** | Idempotency mechanism fail → duplicate orders được tạo |
| **Impact** | **Quality:** Duplicate orders → financial loss |
| **Probability** | Low (1) - Idempotency đã được design, nhưng có thể fail trong edge cases |
| **Impact** | High (3) - Critical quality issue |
| **Risk Score** | **3** → **MEDIUM** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Action** | HMAC verification. Database fallback. Timestamp validation. Monitor duplicate detection |
| **Timing/Trigger** | Epic 2 (Story 2.22). Trigger: Duplicate orders detected |
| **Expected Result** | Zero duplicate orders |
| **POC Required?** | ✅ Yes - Test replay attack scenarios |

---

### RISK-011: Data Loss (Webhook) → Orders Bị Mất
**Nhóm:** Technical Risk  
**Known-Unknown:** ✅ (Biết có data loss risk, chưa test recovery)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Webhook received nhưng chưa persist → system crash → webhook data lost |
| **Risk Event** | Order không được tạo → customer complaint |
| **Impact** | **Quality:** Lost orders → revenue loss |
| **Probability** | Low (1) - Webhooks được store trong S3, nhưng có thể fail |
| **Impact** | High (3) - Business-critical |
| **Risk Score** | **3** → **MEDIUM** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Store webhook vào S3 TRƯỚC KHI process. Idempotency với S3 backup. Audit trail |
| **Timing/Trigger** | Epic 2 (Story 2.3). Trigger: Webhook processing failures |
| **Expected Result** | Zero webhook data loss |
| **POC Required?** | ✅ Yes - Test crash recovery scenarios |

---

### RISK-012: Security Breach (API Keys) → Unauthorized Access
**Nhóm:** Technical Risk  
**Known-Unknown:** ✅ (Biết có security risk, chưa audit)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | API keys stored in plaintext config → nếu bị leak → unauthorized access |
| **Risk Event** | Data breach → compliance violation, reputation damage |
| **Impact** | **Quality:** Compliance violation, legal issues |
| **Probability** | Low (1) - Security best practices đã được follow |
| **Impact** | High (3) - Critical security issue |
| **Risk Score** | **3** → **MEDIUM** |
| **Owner** | Tech Lead / Security Lead |
| **Mitigation Strategy** | **Avoid + Reduce** |
| **Action** | Encrypt API keys. Environment variables. Rotation policy. Security audit |
| **Timing/Trigger** | Epic 1 (Story 1.1). Trigger: Security audit findings |
| **Expected Result** | Zero API keys in plaintext |
| **POC Required?** | ❌ No - Security best practice |

---

### RISK-013: BtoB Portal Adoption Failure → Đại Lý Không Sử dụng
**Nhóm:** Human/Team Risk  
**Known-Unknown:** ✅ (Biết có adoption risk, chưa test với users)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | BtoB Portal quá phức tạp → đại lý không sử dụng → vẫn gửi email/FAX |
| **Risk Event** | Adoption rate < 50% → operations team vẫn phải nhập manual |
| **Impact** | **Scope:** Business value không đạt |
| **Probability** | Medium (2) - User adoption là challenge |
| **Impact** | Medium (2) - Business value bị giảm |
| **Risk Score** | **4** → **MEDIUM** |
| **Owner** | PM / UX Designer |
| **Mitigation Strategy** | **Reduce** |
| **Action** | UX design focus. User testing với đại lý. Onboarding support. Incentives. Phased rollout |
| **Timing/Trigger** | Epic 5. Trigger: Adoption < 50% sau 1 tháng |
| **Expected Result** | Adoption rate 80%+. 70%+ orders từ portal |
| **POC Required?** | ❌ No - Nhưng cần user testing |

---

### RISK-014: Payment Reconciliation Errors → Financial Discrepancies
**Nhóm:** Technical Risk  
**Known-Unknown:** ✅ (Biết có reconciliation risk, chưa test với real data)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | GMO Bank API integration có bugs → payment reconciliation sai |
| **Risk Event** | Financial discrepancies → accounting team phải manual fix |
| **Impact** | **Quality:** Accounting issues. Manual reconciliation vẫn cần |
| **Probability** | Medium (2) - Payment reconciliation là complex logic |
| **Impact** | Medium (2) - Business-critical nhưng có thể fix |
| **Risk Score** | **4** → **MEDIUM** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Comprehensive testing. Manual review process. Daily reconciliation reports. Alert on discrepancies |
| **Timing/Trigger** | Epic 6 (Story 6.1). Trigger: Payment mismatches > 0.1% |
| **Expected Result** | Reconciliation accuracy 99.9%+ |
| **POC Required?** | ✅ Yes - Test với real payment data (anonymized) |

---

### RISK-015: Performance Degradation → Dashboard Slow
**Nhóm:** Technical Risk  
**Known-Unknown:** ✅ (Biết có performance risk, chưa test với large datasets)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Dashboard không realtime (< 1 second refresh) → operations team không thấy value |
| **Risk Event** | Dashboard refresh > 1 second → user experience kém → adoption failure |
| **Impact** | **Quality:** User experience kém |
| **Probability** | Medium (2) - Performance issues có thể xảy ra với large datasets |
| **Impact** | Low (1) - Có thể optimize |
| **Risk Score** | **2** → **LOW** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Redis caching. Query optimization. Horizontal scaling. Performance monitoring |
| **Timing/Trigger** | Epic 7. Trigger: Dashboard refresh > 1 second |
| **Expected Result** | Dashboard refresh < 1 second |
| **POC Required?** | ✅ Yes - Performance testing với large datasets |

---

### RISK-016: Scope Creep → Timeline Delay → Budget Overrun
**Nhóm:** Schedule/Management Risk  
**Known-Unknown:** ✅ (Biết có scope creep risk, chưa biết mức độ)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | CR nhiều hoặc requirements thay đổi → scope creep |
| **Risk Event** | Timeline delay → budget overrun |
| **Impact** | **Schedule + Cost:** Timeline delay, budget overrun |
| **Probability** | Medium (2) - Scope creep là common |
| **Impact** | Medium (2) - Có thể manage |
| **Risk Score** | **4** → **MEDIUM** |
| **Owner** | PM |
| **Mitigation Strategy** | **Avoid + Reduce** |
| **Action** | Freeze scope trong mỗi sprint. CR process rõ ràng. Baseline requirements. Change approval process |
| **Timing/Trigger** | Start từ Epic 1. Trigger: CR > 3 per sprint |
| **Expected Result** | Scope được control. Timeline on track |
| **POC Required?** | ❌ No - Process management |

---

### RISK-017: Resource Unavailability → Development Delay
**Nhóm:** Human/Team Risk  
**Known-Unknown:** ✅ (Biết có resource risk, chưa có backup plan)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Developer chính nghỉ phép hoặc bị assign dự án khác |
| **Risk Event** | Critical tasks bị delay → timeline delay |
| **Impact** | **Schedule:** Timeline delay |
| **Probability** | Low (1) - Resource planning đã được consider |
| **Impact** | Medium (2) - Có thể manage với backup |
| **Risk Score** | **2** → **LOW** |
| **Owner** | PM |
| **Mitigation Strategy** | **Reduce + Transfer** |
| **Action** | Knowledge sharing. Documentation. Backup developer. Cross-training |
| **Timing/Trigger** | Start từ Epic 1. Trigger: Key developer unavailable > 3 days |
| **Expected Result** | Development không bị delay |
| **POC Required?** | ❌ No - Resource management |

---

### RISK-018: Integration API Changes → Breaking Changes → System Failure
**Nhóm:** Technical Risk  
**Known-Unknown:** ✅ (Biết có API change risk, chưa có versioning strategy)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | External APIs (Shopify, Ship&Co, GMO Bank) có breaking changes |
| **Risk Event** | Integration code fail → system không hoạt động |
| **Impact** | **Quality + Schedule:** System failure → business operations halted |
| **Probability** | Low (1) - APIs thường có versioning |
| **Impact** | High (3) - Business-critical |
| **Risk Score** | **3** → **MEDIUM** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce + Transfer** |
| **Action** | API versioning strategy. Abstraction layer (Adapter Pattern). Monitor API changes. Version pinning |
| **Timing/Trigger** | Epic 2 (Story 2.24). Trigger: API breaking changes detected |
| **Expected Result** | System vẫn hoạt động khi API có changes |
| **POC Required?** | ❌ No - Architecture pattern |

---

### RISK-019: Hotwire (Turbo + Stimulus) Chưa Quen → Development Delay
**Nhóm:** Human/Team Risk + Technical Risk  
**Known-Unknown:** ✅ (Biết team chưa quen Hotwire, chưa biết learning curve)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Team chưa có kinh nghiệm với Hotwire (Turbo + Stimulus). Learning curve có thể chậm |
| **Risk Event** | Development delay do phải học Hotwire → Epic 5 (BtoB Portal) delay |
| **Impact** | **Schedule:** Timeline delay cho Epic 5 |
| **Probability** | Medium (2) - Learning curve là thực tế |
| **Impact** | Medium (2) - Có thể manage với training |
| **Risk Score** | **4** → **MEDIUM** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Training sessions trước Epic 5. POC với Hotwire. Pair programming. Documentation |
| **Timing/Trigger** | Start từ Epic 1. Trigger: Team chưa quen Hotwire trước Epic 5 |
| **Expected Result** | Team có đủ skill với Hotwire. Epic 5 on track |
| **POC Required?** | ✅ Yes - POC 1-2 ngày với Hotwire trước Epic 5 |

---

### RISK-020: Communication Delay → Feedback Chậm → Development Blocked
**Nhóm:** Human/Team Risk  
**Known-Unknown:** ✅ (Biết có communication risk, chưa biết mức độ delay)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Stakeholders (CREDIE, MediaBirth) không online hoặc feedback chậm |
| **Risk Event** | Feedback delay > 3 days → development blocked → timeline delay |
| **Impact** | **Schedule:** Timeline delay |
| **Probability** | Low (1) - Communication channels đã được establish |
| **Impact** | Low (1) - Có thể manage |
| **Risk Score** | **1** → **LOW** |
| **Owner** | PM |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Regular sync meetings. Clear communication channels. Auto-reminders. Escalation process |
| **Timing/Trigger** | Start từ Epic 1. Trigger: Feedback delay > 3 days |
| **Expected Result** | Feedback trong 24-48 hours |
| **POC Required?** | ❌ No - Process management |

---

### RISK-021: Test Coverage Insufficient → Bugs Leak to Production
**Nhóm:** Technical Risk + Schedule/Management Risk  
**Known-Unknown:** ✅ (Biết có test coverage risk, chưa measure)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Test coverage không đủ → bugs leak to production |
| **Risk Event** | Critical bugs không được catch → production issues → customer complaints |
| **Impact** | **Quality:** Production bugs → reputation damage |
| **Probability** | Low (1) - Test strategy đã được plan |
| **Impact** | Medium (2) - Có thể fix |
| **Risk Score** | **2** → **LOW** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Test coverage target 80%+. Automated testing. Code review. QA process |
| **Timing/Trigger** | Start từ Epic 1. Trigger: Test coverage < 70% |
| **Expected Result** | Test coverage 80%+. Zero critical bugs |
| **POC Required?** | ❌ No - Testing process |

---

### RISK-022: Documentation Insufficient → Knowledge Loss
**Nhóm:** Human/Team Risk  
**Known-Unknown:** ✅ (Biết có documentation risk, chưa audit)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Documentation không đủ → knowledge loss khi developer rời dự án |
| **Risk Event** | New developer không hiểu system → maintenance issues → timeline delay |
| **Impact** | **Schedule + Quality:** Maintenance issues |
| **Probability** | Low (1) - Documentation đã được plan |
| **Impact** | Low (1) - Có thể manage |
| **Risk Score** | **1** → **LOW** |
| **Owner** | Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Comprehensive documentation. Code comments. Architecture docs. Knowledge sharing |
| **Timing/Trigger** | Start từ Epic 1. Trigger: Documentation gaps identified |
| **Expected Result** | Documentation đầy đủ. New developer onboard trong 1 tuần |
| **POC Required?** | ❌ No - Documentation process |

---

## 🟡 NEW RISKS IDENTIFIED (Theo Checklist Nhận Diện Nhanh)

### RISK-023: Business Rules Chưa Confirm → Implementation Sai → Rework
**Nhóm:** Product/Requirement Risk  
**Known-Unknown:** ✅ (Biết có business rules, chưa confirm chi tiết)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Business rules cho rebate calculation (MWATER: 2円/本, SILICA: 5円→3円 khi ≥ 5,000万) chưa được confirm chi tiết |
| **Risk Event** | Implementation sai → rework → Epic 6 delay |
| **Impact** | **Schedule + Quality:** Rework, timeline delay |
| **Probability** | Medium (2) - Business rules có thể có edge cases |
| **Impact** | Medium (2) - Có thể fix |
| **Risk Score** | **4** → **MEDIUM** |
| **Owner** | PM / Product Owner |
| **Mitigation Strategy** | **Avoid + Reduce** |
| **Action** | Confirm business rules với CREDIE trước Epic 6. Document edge cases. Review với accounting team |
| **Timing/Trigger** | Start từ Epic 1. Trigger: Business rules chưa confirm trước Epic 6 |
| **Expected Result** | Business rules confirmed. Implementation đúng |
| **POC Required?** | ❌ No - Requirement confirmation |

---

### RISK-024: Multi-Warehouse Logic Chưa Rõ → Implementation Phức Tạp
**Nhóm:** Product/Requirement Risk + Technical Risk  
**Known-Unknown:** ✅ (Biết có multi-warehouse, chưa biết logic chi tiết)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Multi-warehouse routing logic (kho A 尼崎, kho B 岐阜) chưa được define rõ. Route rules chưa confirm |
| **Risk Event** | Implementation phức tạp hơn expected → Epic 4 delay |
| **Impact** | **Schedule:** Timeline delay cho Epic 4 |
| **Probability** | Medium (2) - Multi-warehouse logic có thể phức tạp |
| **Impact** | Medium (2) - Có thể manage |
| **Risk Score** | **4** → **MEDIUM** |
| **Owner** | PM / Tech Lead |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Confirm routing logic với operations team trước Epic 4. POC với simple routing rules. Iterate |
| **Timing/Trigger** | Start từ Epic 2. Trigger: Routing logic chưa confirm trước Epic 4 |
| **Expected Result** | Routing logic confirmed. Implementation on track |
| **POC Required?** | ✅ Yes - POC với simple routing rules |

---

### RISK-025: Kintone Apps Structure Chưa Confirm → Data Mapping Sai
**Nhóm:** Product/Requirement Risk + Technical Risk  
**Unknown-Unknown:** ⚠️ (Chưa biết structure của Kintone Apps)

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation** | Kintone Apps structure (Orders App, Stock App, Rewards App) chưa được confirm. Field mappings chưa rõ |
| **Risk Event** | Data mapping sai → orders không sync đúng → rework |
| **Impact** | **Schedule + Quality:** Rework, timeline delay |
| **Probability** | Medium (2) - Kintone Apps structure có thể khác expected |
| **Impact** | Medium (2) - Có thể fix |
| **Risk Score** | **4** → **MEDIUM** |
| **Owner** | Tech Lead / PM |
| **Mitigation Strategy** | **Reduce** |
| **Action** | Request Kintone Apps structure và field mappings sớm. POC với test data. Review với operations team |
| **Timing/Trigger** | Start từ Epic 1. Trigger: Apps structure chưa confirm trước Epic 2 |
| **Expected Result** | Apps structure confirmed. Data mapping đúng |
| **POC Required?** | ✅ **YES - CRITICAL** - POC 1-2 ngày với Kintone Apps trước Epic 2 |

---

## Phân tích theo Module (Epic)

### Epic 1: Foundation & Infrastructure
**Risks:** RISK-002, RISK-012, RISK-019, RISK-020, RISK-021, RISK-022  
**Module khó nhất:** Core Infrastructure Services (Redis, Sidekiq setup)  
**Module chưa chắc chắn:** Initial configuration với Kintone  
**Module phụ thuộc:** Tất cả epics phụ thuộc Epic 1  
**Module ảnh hưởng user:** Không trực tiếp, nhưng là foundation

### Epic 2: Order Processing Automation
**Risks:** RISK-001, RISK-002, RISK-003, RISK-005, RISK-008, RISK-009, RISK-010, RISK-011, RISK-018, RISK-025  
**Module khó nhất:** Webhook pipeline với idempotency  
**Module chưa chắc chắn:** Yoom gateway integration, Kintone Apps structure  
**Module phụ thuộc:** Epic 1 (infrastructure)  
**Module ảnh hưởng user:** Trực tiếp - operations team

### Epic 3: Fulfillment Automation
**Risks:** RISK-007  
**Module khó nhất:** Ship&Co API integration  
**Module chưa chắc chắn:** Ship&Co API details  
**Module phụ thuộc:** Epic 2 (order processing)  
**Module ảnh hưởng user:** Trực tiếp - warehouse team

### Epic 4: Warehouse Management Core
**Risks:** RISK-024  
**Module khó nhất:** Multi-warehouse routing logic  
**Module chưa chắc chắn:** Routing rules, location tracking logic  
**Module phụ thuộc:** Epic 2 (inventory sync)  
**Module ảnh hưởng user:** Trực tiếp - warehouse team

### Epic 5: BtoB Portal & Self-Service
**Risks:** RISK-004, RISK-013, RISK-019  
**Module khó nhất:** User adoption  
**Module chưa chắc chắn:** Hotwire learning curve, UX design  
**Module phụ thuộc:** Epic 2 (order processing)  
**Module ảnh hưởng user:** Trực tiếp - đại lý

### Epic 6: Payment & Financial Automation
**Risks:** RISK-006, RISK-014, RISK-023  
**Module khó nhất:** GMO Bank API integration, rebate calculation logic  
**Module chưa chắc chắn:** GMO Bank API, business rules  
**Module phụ thuộc:** Epic 2 (order processing)  
**Module ảnh hưởng user:** Trực tiếp - accounting team

### Epic 7: Dashboard & Visibility
**Risks:** RISK-015  
**Module khó nhất:** Performance với large datasets  
**Module chưa chắc chắn:** Kintone dashboard capabilities  
**Module phụ thuộc:** Epic 2, 3, 4 (data sources)  
**Module ảnh hưởng user:** Trực tiếp - operations team, stakeholders

---

## Phân loại theo Known-Unknown vs Unknown-Unknown

### Known-Unknown (Biết chưa rõ - Cần POC)
- RISK-001: Kintone API Rate Limits → POC: Load test 1000+ orders/hour
- RISK-002: Redis Failover → POC: Test failover scenario
- RISK-003: Inventory Race Conditions → POC: Test concurrent orders
- RISK-005: Queue Overflow → POC: Load test với peak load
- RISK-006: GMO Bank API → **POC CRITICAL** - 2-3 ngày trước Epic 6
- RISK-007: Ship&Co API → **POC CRITICAL** - 2-3 ngày trước Epic 3
- RISK-009: Kintone Downtime → POC: Test fallback scenario
- RISK-010: Webhook Replay → POC: Test replay attacks
- RISK-011: Data Loss Recovery → POC: Test crash recovery
- RISK-014: Payment Reconciliation → POC: Test với real data
- RISK-015: Performance → POC: Performance testing
- RISK-019: Hotwire Learning → POC: 1-2 ngày với Hotwire
- RISK-024: Multi-Warehouse Routing → POC: Simple routing rules
- RISK-025: Kintone Apps Structure → **POC CRITICAL** - 1-2 ngày trước Epic 2

### Unknown-Unknown (Chưa biết là không biết - Cần POC sớm)
- RISK-008: Yoom Gateway → **POC CRITICAL** - 2-3 ngày trước Epic 2 (chưa biết cách Yoom hoạt động)

---

## Checklist Nhận Diện Nhanh - Kết Quả

### Technical Checklist
- ✅ Có tech mới? → Hotwire (RISK-019)
- ✅ Có API chưa confirm? → GMO Bank (RISK-006), Ship&Co (RISK-007), Yoom (RISK-008)
- ✅ Có phần cần POC trước? → 14 risks cần POC
- ✅ Có dependency giữa module? → Epic 2-7 phụ thuộc Epic 1-2
- ✅ Có phần chưa được test production? → Tất cả integrations

### Product Checklist
- ✅ AC rõ chưa? → Business rules chưa confirm (RISK-023)
- ✅ Mockup đầy đủ chưa? → BtoB Portal (RISK-013)
- ✅ Flow có conflict không? → Multi-warehouse routing (RISK-024)
- ✅ Business rule đã confirm chưa? → Rebate calculation (RISK-023)

### Team Checklist
- ✅ Ai phụ trách module A? Có backup không? → Resource risk (RISK-017)
- ✅ Dev có kinh nghiệm đúng không? → Hotwire learning (RISK-019)
- ✅ Team đang overload không? → N/A (single team)
- ✅ QA đủ người không? → Test coverage (RISK-021)

### Timeline Checklist
- ✅ Có milestone khó khăn? → Epic 2 (webhook pipeline)
- ✅ Có task critical bị phụ thuộc? → Epic 2 phụ thuộc Epic 1
- ✅ Ước lượng có nghi ngờ không? → Scope creep (RISK-016)
- ✅ Có nhiều release gấp không? → Phased rollout plan

---

## Top 5 Risks Cần Address Ngay (Updated)

1. **RISK-001: Kintone API Rate Limits** (Score: 9) - HIGH
2. **RISK-008: Yoom Gateway Chưa Confirm** (Score: 6) - HIGH - **Unknown-Unknown**
3. **RISK-006: GMO Bank API Chưa Confirm** (Score: 6) - HIGH - **POC CRITICAL**
4. **RISK-007: Ship&Co API Chưa Confirm** (Score: 6) - HIGH - **POC CRITICAL**
5. **RISK-025: Kintone Apps Structure Chưa Confirm** (Score: 4) - MEDIUM - **POC CRITICAL**

---

## POC Priority List

### POC CRITICAL (Phải làm trước khi start epic)
1. **Yoom Gateway POC** (2-3 ngày) - Trước Epic 2
2. **Kintone Apps Structure POC** (1-2 ngày) - Trước Epic 2
3. **Ship&Co API POC** (2-3 ngày) - Trước Epic 3
4. **GMO Bank API POC** (2-3 ngày) - Trước Epic 6

### POC Important (Nên làm sớm)
5. **Redis Failover POC** (1 ngày) - Trong Epic 1
6. **Inventory Race Condition POC** (1 ngày) - Trong Epic 2
7. **Load Test POC** (1-2 ngày) - Trong Epic 2
8. **Hotwire Learning POC** (1-2 ngày) - Trước Epic 5

---

## Risk Monitoring Schedule

- **Weekly Review:** Tất cả High và Medium priority risks
- **Sprint Review:** Risk status update trong sprint retrospective
- **POC Review:** Review POC results trước mỗi epic start
- **Monthly Review:** Full risk register review và update
- **Quarterly Review:** Risk library update và lessons learned

---

**Document Status:** Risk Register Version 2 (Updated with new analysis criteria)  
**Next Review Date:** 2025-11-27 (Weekly)  
**Owner:** PM (Potato)

