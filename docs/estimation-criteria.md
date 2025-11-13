# Tiêu chí Đánh giá Estimate - Silica-link

**Date:** 2025-11-12
**Version:** 1.0

---

## Tổng quan

Tài liệu này mô tả các tiêu chí và assumptions được sử dụng để đánh giá estimate thời gian cho dự án Silica-link.

---

## 1. Developer Profile (Cấp độ Developer)

### Tiêu chí chính:
- **Level:** Entry-level / Beginner Ruby Developer
- **Kinh nghiệm:** 
  - Có kiến thức cơ bản về Ruby on Rails (đã học qua tutorials, có làm vài projects nhỏ)
  - Chưa có nhiều kinh nghiệm thực tế với Rails ecosystem
  - Chưa có kinh nghiệm với third-party APIs
  - Cần thời gian để research và học hỏi
- **Năng suất:** 
  - 0.5-1 epic/tuần (4-8 stories) với AI assistance
  - 0.5-1 story points/ngày (typical)
  - Cần nhiều thời gian cho research, debugging, learning

### So sánh với các level khác:
- **Junior dev + AI:** 1-2 epics/tuần (8-15 stories) → Giảm 30-40% thời gian
- **Mid-level dev + AI:** 2-3 epics/tuần (15-25 stories) → Giảm 50-60% thời gian
- **Senior dev + AI:** 3-5 epics/tuần (25-40 stories) → Giảm 60-70% thời gian

**Note:** Estimate hiện tại dựa trên Entry-level/Beginner, sẽ tăng thời gian đáng kể so với Junior level.

---

## 2. Technology Stack Assumptions (Phân loại theo mức độ quen thuộc)

### Core Technologies - Phân loại:

#### Level 1: Đã quen / Cơ bản (Basic Familiarity)
- **Ruby on Rails 7.x:** 
  - Entry-level: Đã học qua tutorials, có làm vài projects nhỏ
  - Learning curve: +30-40% thời gian (cần research best practices, patterns)
  - Research time: 10-15% của development time

#### Level 2: Mới / Cần học (New Technology)
- **ReactJS 18.x:** 
  - Entry-level: Chưa có kinh nghiệm
  - Learning curve: +50-70% thời gian (cần học từ đầu)
  - Research time: 20-30% của development time
- **Redis + Sidekiq:** 
  - Entry-level: Chưa có kinh nghiệm
  - Learning curve: +40-60% thời gian
  - Research time: 15-25% của development time
- **PostgreSQL:** 
  - Entry-level: Có kiến thức cơ bản SQL
  - Learning curve: +20-30% thời gian (Rails-specific patterns)
  - Research time: 10-15% của development time

#### Level 3: Mới / Phức tạp (New & Complex)
- **AWS Services (Lambda, S3, CloudWatch, Secrets Manager):**
  - Entry-level: Chưa có kinh nghiệm
  - Learning curve: +60-80% thời gian
  - Research time: 25-35% của development time
  - Setup/Configuration: +20-30% thời gian

#### Level 4: Mới / Rất phức tạp (New & Very Complex)
- **Kintone API:**
  - Entry-level: Chưa có kinh nghiệm
  - Learning curve: +50-70% thời gian
  - Research time: 20-30% của development time
  - API documentation research: 15-20% của integration time

- **Third-party APIs (Mới, chưa quen):**
  - **Yoom API:** 
    - Entry-level: Chưa có kinh nghiệm
    - Learning curve: +70-90% thời gian
    - Research time: 30-40% của integration time
    - API documentation research: 20-25% của integration time
    - Testing/Integration: +30-40% thời gian
  
  - **Ship&Co API:**
    - Entry-level: Chưa có kinh nghiệm
    - Learning curve: +70-90% thời gian
    - Research time: 30-40% của integration time
    - API documentation research: 20-25% của integration time
    - Testing/Integration: +30-40% thời gian
  
  - **GMO Bank API:**
    - Entry-level: Chưa có kinh nghiệm
    - Learning curve: +100-150% thời gian (rất phức tạp)
    - Research time: 40-50% của integration time
    - Security research (signature auth, IP whitelist): 20-30% của integration time
    - API documentation research: 25-30% của integration time
    - Testing/Integration: +50-70% thời gian (financial accuracy critical)
  
  - **WMS API:**
    - Entry-level: Chưa có kinh nghiệm
    - Learning curve: +60-80% thời gian
    - Research time: 25-35% của integration time
    - API documentation research: 20-25% của integration time

### Learning Curve Impact Summary:
- **Level 1 (Đã quen cơ bản):** +30-40% thời gian
- **Level 2 (Mới):** +50-70% thời gian
- **Level 3 (Mới & Phức tạp):** +60-80% thời gian
- **Level 4 (Mới & Rất phức tạp):** +70-150% thời gian

### Research Time Breakdown:
- **API Documentation:** 20-30% của integration time
- **Best Practices:** 10-15% của development time
- **Security/Error Handling:** 15-25% của integration time
- **Testing/Integration:** 20-30% của integration time
- **Debugging/Troubleshooting:** 15-20% của development time

---

## 3. Project Level & Complexity

### Project Level: Level 4 (Enterprise Platform)
- **Stories:** 40+ stories
- **Complexity:** Multiple integrations, enterprise architecture
- **Documentation:** PRD + Architecture + JIT tech specs

### Complexity Factors:
- **Multiple System Integrations:** Yoom, Ship&Co, WMS, GMO, Kintone
- **Financial Transactions:** GMO API (AR/AP) - yêu cầu accuracy cao
- **Real-time Processing:** Webhooks, async jobs, queue processing
- **Dual Stock Management:** Logic Stock + Physical Stock (complexity cao)
- **Error Handling:** Comprehensive error handling, retry, DLQ

### Impact on Estimate:
- **High complexity features:** +20-30% buffer
- **Financial/critical features:** +30-50% buffer (testing, validation)
- **Integration features:** +20-40% buffer (API research, error handling)

---

## 4. Phạm vi Estimate (Scope)

### ✅ Bao gồm trong Estimate:
- **Development:** Code implementation
- **Basic Testing:** Unit tests, basic integration tests (developer tự test)
- **Basic Error Handling:** Retry, logging, basic error handling
- **Basic Documentation:** Code comments, basic README

### ❌ KHÔNG bao gồm trong Estimate:
- **QA Testing:** E2E testing, comprehensive test suite, UAT
- **Documentation:** User documentation, technical documentation chi tiết
- **Training:** User training, team training
- **Deployment:** Production deployment, infrastructure setup chi tiết
- **Monitoring Setup:** Advanced CloudWatch dashboards, alerting rules
- **Performance Optimization:** Profiling, optimization (sẽ làm sau nếu cần)
- **Security Audit:** Security review, penetration testing

### Buffer cho các phần không bao gồm:
- **QA Testing:** +20-30% của development time
- **Documentation:** +10-15% của development time
- **Training:** +5-10% của development time
- **Deployment:** +10-15% của development time

---

## 5. Story Point to Hours Conversion

### Conversion Rate (Entry-level Developer):
- **1 story point** = 4-8 hours (tăng từ 2-4h do learning curve)
- **2 story points** = 2-3 days (16-24 hours, tăng từ 1-2 days)
- **3 story points** = 3-5 days (24-40 hours, tăng từ 2-3 days)
- **5 story points** = 5-8 days (40-64 hours, tăng từ 3-5 days)

### Breakdown by Task Type (Entry-level):
- **Research & Learning:** 25-35% của story time
- **Setup/Configuration:** 15-20% của story time
- **Core Implementation:** 30-40% của story time
- **Error Handling:** 10-15% của story time
- **Testing (Basic):** 10-15% của story time
- **Debugging/Troubleshooting:** 10-15% của story time
- **Code Review/Refactor:** 5-10% của story time

### Typical Story Sizes:
- **Small (1 point):** Simple CRUD, basic API endpoint, simple UI component
  - Entry-level: 4-8 hours (bao gồm research 1-2h)
- **Medium (2-3 points):** Integration với external API, complex business logic, UI với state management
  - Entry-level: 2-5 days (bao gồm research 0.5-1.5 days)
- **Large (5 points):** Complex integration, multiple components, security-critical features
  - Entry-level: 5-8 days (bao gồm research 1.5-3 days)

---

## 6. Risk & Buffer Assumptions

### Buffer cho Unexpected Issues (Entry-level):
- **Standard Buffer:** 30-40% cho unexpected issues (tăng từ 20% do entry-level)
- **High-risk Features:** +50-80% buffer (financial transactions, critical integrations)
- **New Technology:** +50-70% buffer (APIs chưa quen, entry-level developer)
- **Learning Curve Issues:** +30-50% buffer (có thể gặp vấn đề không lường trước)

### Common Risks được tính vào Buffer:
- **API Changes:** Third-party APIs có thể thay đổi
- **Integration Issues:** Unexpected integration problems (thường gặp với entry-level)
- **Performance Issues:** Cần optimize sau khi implement
- **Security Issues:** Cần thêm security measures (entry-level có thể thiếu kinh nghiệm)
- **Business Logic Changes:** Requirements có thể thay đổi
- **Learning Curve:** Có thể mất nhiều thời gian hơn dự kiến để hiểu và implement
- **Debugging Time:** Entry-level có thể mất nhiều thời gian debug hơn
- **Code Quality:** Có thể cần refactor nhiều hơn do thiếu kinh nghiệm

---

## 7. Velocity Adjustments

### Factors có thể giảm Estimate:
- **Senior Developer:** -60-70% thời gian (so với Entry-level)
- **Mid-level Developer:** -50-60% thời gian (so với Entry-level)
- **Junior Developer:** -30-40% thời gian (so với Entry-level)
- **AI Assistance (Claude/Cursor):** -20-30% thời gian
- **Team đã quen với APIs:** -30-40% thời gian (sau lần đầu, với entry-level)
- **Existing Code/Patterns:** -10-20% thời gian (reuse)

### Factors có thể tăng Estimate:
- **Entry-level Developer:** +0% (baseline mới)
- **New Technology:** +50-70% thời gian (với entry-level)
- **Complex Requirements:** +30-50% thời gian (với entry-level)
- **Strict Security Requirements:** +50-80% thời gian (với entry-level)
- **First-time Integration:** +40-60% thời gian (với entry-level)

### Combined Impact:
- **Senior Dev + AI:** Có thể giảm 70-80% thời gian so với Entry-level baseline
- **Mid-level Dev + AI:** Có thể giảm 60-70% thời gian so với Entry-level baseline
- **Junior Dev + AI:** Có thể giảm 40-50% thời gian so với Entry-level baseline
- **Entry-level Dev + AI:** Có thể giảm 20-30% thời gian (vẫn cao hơn Junior baseline)

---

## 8. Estimate Breakdown by Task Type (Entry-level)

### Development Tasks:
- **Research & Learning:** 25-35% của story time (tăng đáng kể)
- **Setup/Configuration:** 15-20% của story time (tăng từ 10-20%)
- **Core Implementation:** 30-40% của story time (giảm từ 50-60% do có research)
- **Error Handling:** 10-15% của story time
- **Testing (Basic):** 10-15% của story time
- **Debugging/Troubleshooting:** 10-15% của story time (tăng do entry-level)
- **Code Review/Refactor:** 5-10% của story time

### Integration Tasks (Entry-level):
- **API Documentation Research:** 25-35% của integration time (tăng từ 20-30%)
- **Best Practices Research:** 10-15% của integration time (mới thêm)
- **API Client Implementation:** 25-35% của integration time (giảm từ 30-40%)
- **Error Handling/Retry:** 15-25% của integration time
- **Security Research (nếu cần):** 10-20% của integration time (mới thêm)
- **Testing/Integration:** 15-25% của integration time (tăng từ 10-20%)
- **Debugging Integration Issues:** 10-15% của integration time (mới thêm)

### Research Time Breakdown (Chi tiết):
- **API Documentation:** 20-30% của integration time
- **Code Examples/Tutorials:** 10-15% của development time
- **Best Practices/Patterns:** 10-15% của development time
- **Security Best Practices:** 10-20% của integration time (cho GMO, sensitive APIs)
- **Error Handling Patterns:** 5-10% của development time
- **Testing Strategies:** 5-10% của development time

---

## 9. Epic-Specific Assumptions

### Epic 1 (Foundation):
- **Assumption:** Chỉ làm Project Setup, không làm core services
- **Core services:** Sẽ implement trong các epics sau khi cần

### Epic 2 (EC Order Intake):
- **Assumption:** Yoom API documentation đầy đủ
- **Risk:** Webhook reliability → Có batch fallback

### Epic 3 (Inventory):
- **Assumption:** Kintone Stock App đã setup sẵn
- **Complexity:** Dual stock management (Logic + Physical)

### Epic 4 (Warehouse Scan UI):
- **Assumption:** Barcode scanner hardware available
- **Complexity:** Real-time sync, offline capability (nếu cần)

### Epic 5 (Shipping):
- **Assumption:** Ship&Co API documentation đầy đủ
- **Risk:** Rate limiting, API reliability

### Epic 6 (Payment AR):
- **Assumption:** GMO API credentials available
- **Complexity:** Signature authentication, IP whitelist, financial accuracy
- **Risk:** Highest risk epic (financial transactions)

---

## 10. Timeline Assumptions

### Working Hours:
- **Per Day:** 8 hours (standard)
- **Per Week:** 40 hours (5 days)
- **Per Month:** ~160 hours (20 days)

### Team Size Impact:
- **1 Developer:** Baseline estimate
- **2 Developers:** ~50% thời gian (có thể parallelize)
- **3+ Developers:** ~40-45% thời gian (coordination overhead)

### Parallelization:
- **Epics độc lập:** Có thể làm song song
- **Stories trong epic:** Có thể parallelize một số stories
- **Dependencies:** Phải tuân thủ dependencies

---

## 11. Quality Assumptions

### Code Quality:
- **Standard:** Clean code, basic error handling, basic tests
- **Not Included:** Advanced patterns, comprehensive test coverage, performance optimization

### Testing Level:
- **Included:** Unit tests (basic), integration tests (basic), manual testing
- **Not Included:** E2E tests, comprehensive test suite, load testing, security testing

---

## 12. Documentation Assumptions

### Included:
- **Code Comments:** Basic comments cho complex logic
- **README:** Basic setup instructions
- **API Documentation:** Basic endpoint documentation

### Not Included:
- **User Documentation:** User guides, training materials
- **Technical Documentation:** Detailed architecture docs, API specs chi tiết
- **Operational Documentation:** Runbooks, deployment guides chi tiết

---

## Tổng kết

### Baseline Estimate Criteria:
1. **Developer:** Entry-level / Beginner Ruby Developer
2. **Technology:** Ruby on Rails (cơ bản), ReactJS (mới), AWS (mới), Third-party APIs (mới)
3. **Scope:** Development only (không bao gồm QA, docs, training, deployment)
4. **Buffer:** 30-40% cho unexpected issues (tăng từ 20%)
5. **Project Level:** Level 4 (Enterprise Platform)
6. **Complexity:** High (multiple integrations, financial transactions)
7. **Research Time:** 25-35% của development time (tăng đáng kể)

### Adjustment Factors:
- **Senior Dev + AI:** -60-70% thời gian (so với Entry-level)
- **Mid-level Dev + AI:** -50-60% thời gian (so với Entry-level)
- **Junior Dev + AI:** -30-40% thời gian (so với Entry-level)
- **New Technology (Level 2):** +50-70% thời gian
- **New & Complex Technology (Level 3):** +60-80% thời gian
- **New & Very Complex Technology (Level 4):** +70-150% thời gian
- **High-risk Features:** +50-80% buffer
- **First-time Integration:** +40-60% thời gian

### Final Estimate Formula:
```
Base Estimate (Entry-level Dev)
× Technology Factor (1.3-2.5) - tăng đáng kể cho new tech
× Complexity Factor (1.0-1.5)
× Learning Curve Factor (1.3-1.8) - mới thêm
× Risk Factor (1.0-1.8)
+ Research Time (25-35% của base)
+ Buffer (30-40%)
= Final Estimate
```

### Technology Factor Details:
- **Level 1 (Đã quen cơ bản):** 1.3-1.4
- **Level 2 (Mới):** 1.5-1.7
- **Level 3 (Mới & Phức tạp):** 1.6-1.8
- **Level 4 (Mới & Rất phức tạp):** 1.7-2.5

---

**Note:** Các tiêu chí này có thể được điều chỉnh dựa trên:
- Team composition thực tế
- Kinh nghiệm với các APIs cụ thể
- Business priorities và constraints
- Lessons learned từ các epics trước

