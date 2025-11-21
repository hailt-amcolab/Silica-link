# Silica-link - Practical Development Plan

**Author:** Potato (PM & Scrum Master)  
**Date:** 2025-11-20  
**Version:** 1.0  
**Project:** Silica-link - Integrated Management Platform for CREDIE

---

## 📋 Executive Summary

Development plan này định nghĩa quy trình thực tế cho team development Silica-link, tập trung vào:
- **Practical processes** mà team có thể follow ngay
- **Real tools** (GitHub, Slack, Jira/Linear)
- **Clear responsibilities** cho từng role
- **Measurable outcomes** và tracking

**Target Team:**
- 1-2 Backend Developers (Rails)
- 1 Frontend Developer (Hotwire) - optional
- 1 PM/Scrum Master (Product Owner + Scrum Master)
- 1 QA Tester - optional

**Timeline:** 3 months to MVP

---

## 🎯 Project Goals & Success Criteria

### Primary Goals (3 months)

1. **100% BtoC Orders Automated**
   - Zero manual order entry
   - 99.9%+ webhook processing success rate
   - Zero duplicate orders

2. **Operations Team Satisfaction**
   - 80% time savings (2-3 hours/day → <30 min/day)
   - 90%+ user satisfaction score
   - Smooth adoption, minimal training needed

3. **Technical Excellence**
   - < 2 seconds webhook processing (p95)
   - 99.9%+ uptime
   - 80%+ test coverage

### Success Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Order Processing Time | < 2 seconds | Monitoring dashboard |
| System Uptime | 99.9% | Uptime monitoring |
| Test Coverage | ≥ 80% | Code coverage tool |
| Bug Rate | < 1 bug/story | Bug tracking |
| User Satisfaction | ≥ 90% | Survey after 1 month |

---

## 👥 Team Structure & Roles

### Product Owner / Scrum Master (PM)

**Responsibilities:**
- Define product requirements (PRD)
- Prioritize backlog
- Facilitate sprint ceremonies
- Remove blockers
- Communicate với stakeholders
- Track progress và metrics

**Time Allocation:**
- 30% - Product planning & requirements
- 30% - Sprint management & ceremonies
- 20% - Stakeholder communication
- 20% - Blockers & coordination

### Backend Developer(s)

**Responsibilities:**
- Implement backend features (Rails)
- Write tests (RSpec)
- Code review
- Deploy to staging/production
- Monitor system health

**Skills Required:**
- Ruby on Rails
- MySQL
- Redis
- Sidekiq
- REST APIs
- Testing (RSpec)

### Frontend Developer (Optional)

**Responsibilities:**
- Implement BtoB Portal (Hotwire)
- UI/UX implementation
- Frontend testing
- Browser compatibility

**Skills Required:**
- Hotwire (Turbo + Stimulus)
- HTML/CSS
- JavaScript basics

### QA Tester (Optional)

**Responsibilities:**
- Test stories after development
- Report bugs
- Validate acceptance criteria
- Maintain test documentation

---

## 📅 Sprint Structure

### Sprint Duration: 2 Weeks

**Sprint Calendar:**
- **Week 1:**
  - Monday: Sprint Planning (2-4 hours)
  - Tuesday-Friday: Development
- **Week 2:**
  - Monday-Thursday: Development
  - Friday: Sprint Review (2 hours) + Retrospective (1 hour)

### Sprint Ceremonies

#### 1. Sprint Planning (Day 1, 2-4 hours)

**Attendees:** PM, Developers, QA (if available)

**Agenda:**

**Part 1: Review & Goal Setting (30 min)**
- Review previous sprint results
- Review product backlog priorities
- Set sprint goal
- Discuss dependencies và blockers

**Part 2: Story Selection (90-120 min)**
- Review epic priorities
- Select stories từ backlog
- Break down stories into tasks (nếu cần)
- Estimate stories (Story Points hoặc hours)
- Commit to sprint backlog

**Part 3: Task Breakdown (30-60 min)**
- For each story, identify:
  - Technical tasks
  - Testing tasks
  - Documentation tasks
  - Dependencies

**Part 4: Risk & Blocker Discussion (15 min)**
- Identify potential risks
- Plan mitigation
- Assign owners

**Outputs:**
- Sprint Backlog (stories selected)
- Sprint Goal
- Task breakdown
- Risk register

**Tools:**
- Jira/Linear/GitHub Projects để track
- Whiteboard/FigJam để visualize

#### 2. Daily Standup (15 minutes, every day)

**Time:** 9:00 AM (hoặc agreed time)  
**Format:** Round-robin, mỗi người 2-3 minutes

**3 Questions:**
1. **What did I complete yesterday?**
   - Stories/tasks completed
   - Code reviews done
   - Blockers resolved

2. **What will I work on today?**
   - Stories/tasks planned
   - Code reviews to do
   - Goals for the day

3. **What blockers do I have?**
   - Technical blockers
   - Dependencies waiting
   - Questions cần answered

**Best Practices:**
- Be concise (max 2-3 min per person)
- Focus on blockers immediately
- Update task status after standup
- Don't deep dive (schedule separate meeting)

**Tools:**
- Slack channel hoặc in-person
- Update Jira/Linear status after standup

#### 3. Sprint Review (Last day, 2 hours)

**Attendees:** PM, Developers, Stakeholders, Operations team

**Agenda:**

**Part 1: Demo Completed Stories (90 min)**
- Each developer demos their stories
- Show working features
- Answer questions
- Collect feedback

**Part 2: Review Sprint Goal (15 min)**
- Did we achieve sprint goal?
- What was delivered?
- What was not delivered? (và why)

**Part 3: Stakeholder Feedback (15 min)**
- Collect feedback
- Discuss priorities
- Answer questions

**Outputs:**
- Sprint Review Report
- Updated Product Backlog
- Next Sprint Priorities

**Demo Environment:**
- Staging environment
- Test data prepared
- All features integrated

#### 4. Sprint Retrospective (Last day, 1 hour)

**Attendees:** PM, Developers, QA

**Format:** Start-Stop-Continue

**Agenda:**

**Part 1: What Went Well? (15 min)**
- Identify successes
- Practices to continue
- Positive feedback

**Part 2: What Didn't Go Well? (15 min)**
- Identify problems
- Practices to stop
- Pain points

**Part 3: What Should We Improve? (15 min)**
- Identify improvements
- Action items
- Process changes

**Part 4: Action Items (15 min)**
- Define specific actions
- Assign owners
- Set deadlines
- Update development plan nếu cần

**Outputs:**
- Retrospective Notes
- Action Items với owners
- Process Improvements

**Tools:**
- Whiteboard/FigJam
- Retrospective templates

---

## 📝 Story Management

### Story Format

**User Story Template:**
```
As a [user type]
I want [functionality]
So that [benefit]

Acceptance Criteria:
- Given [context]
- When [action]
- Then [expected result]

Technical Notes:
- [Implementation details]
- [Dependencies]
- [Testing requirements]
```

### Story States

```
Backlog → To Do → In Progress → In Review → Done
```

**State Definitions:**
- **Backlog:** Story exists, not yet planned
- **To Do:** Story selected for sprint, ready to start
- **In Progress:** Developer actively working
- **In Review:** Code complete, awaiting review
- **Done:** Code reviewed, tested, deployed

### Story Estimation

**Method:** Story Points (Fibonacci: 1, 2, 3, 5, 8, 13)

**Factors:**
- Complexity
- Effort
- Risk
- Dependencies

**Guidelines:**
- 1 point: < 4 hours (simple bug fix)
- 2 points: 4-8 hours (small feature)
- 3 points: 1-2 days (medium feature)
- 5 points: 2-3 days (complex feature)
- 8 points: 3-5 days (very complex)
- 13 points: > 5 days (should be broken down)

### Story Prioritization

**Priority Levels:**
1. **P0 - Critical:** Blocking other work, security issues
2. **P1 - High:** Core MVP features, user-facing
3. **P2 - Medium:** Important but not blocking
4. **P3 - Low:** Nice to have, can defer

**Prioritization Factors:**
- Business value
- User impact
- Dependencies
- Risk
- Technical debt

---

## 🔄 Development Workflow

### Story Development Process

#### Step 1: Story Selection

**PM Actions:**
- Select story từ sprint backlog
- Ensure story is clear và ready
- Assign to developer
- Update status: Backlog → To Do

#### Step 2: Development

**Developer Actions:**
1. **Understand Story:**
   - Read story description
   - Review acceptance criteria
   - **Review Kintone app schemas (nếu liên quan)**
   - **Check Kintone API documentation (nếu cần)**
   - Clarify questions với PM

2. **Create Branch:**
   ```bash
   git checkout -b feature/epic-{id}-story-{id}-{title}
   ```

3. **Implement:**
   - Write tests first (TDD)
   - **Write Kintone integration tests (nếu cần)**
   - Implement code
   - **Implement Kintone adapter/service (nếu cần)**
   - Make tests pass
   - Refactor
   - Update documentation
   - **Document Kintone app changes (nếu có)**

4. **Test Locally:**
   ```bash
   bundle exec rspec
   bundle exec rubocop
   ```

5. **Test Kintone Integration (nếu story liên quan Kintone):**
   - Test trên Kintone sandbox environment
   - Verify data sync Rails → Kintone
   - Test Kintone API error handling
   - Verify Kintone app configurations
   - Test với Kintone API rate limits

6. **Update Status:**
   - To Do → In Progress (when starting)
   - In Progress → In Review (when complete)

#### Step 3: Code Review

**Process:**
1. **Create Pull Request:**
   - Push branch to GitHub
   - Create PR với description
   - Link to story
   - Request review

2. **Review Checklist:**
   - ✅ Code follows conventions
   - ✅ Tests pass và coverage ≥ 80%
   - ✅ No security issues
   - ✅ Performance acceptable
   - ✅ Documentation updated
   - ✅ Acceptance criteria met
   - ✅ **Kintone integration tested (nếu story liên quan Kintone)**
   - ✅ **Kintone app configurations reviewed (nếu có changes)**

3. **Review Feedback:**
   - Comment on specific lines
   - Request changes nếu needed
   - Approve when ready

4. **Address Feedback:**
   - Developer fixes issues
   - Push updates
   - Re-request review

**Kintone-Specific Review (nếu story liên quan Kintone):**
- ✅ Kintone API calls properly handled (rate limiting, error handling)
- ✅ Data sync logic correct (Rails → Kintone)
- ✅ Kintone app schema changes documented
- ✅ Kintone sandbox testing completed
- ✅ Kintone app configurations reviewed (fields, permissions, workflows)

#### Step 4: Merge & Deploy

**Process:**
1. **Merge to Main:**
   - After approval, merge PR
   - Delete feature branch

2. **Deploy to Staging:**
   - CI/CD pipeline auto-deploys
   - Run smoke tests
   - Verify functionality
   - **Test Kintone integration trên staging (nếu có)**
   - **Verify Kintone sandbox data sync**

3. **Deploy Kintone Changes (nếu có app schema changes):**
   - **Review Kintone app changes với PM**
   - **Deploy to Kintone sandbox first**
   - **Test Kintone app configurations**
   - **Verify Kintone dashboard updates (nếu có)**

4. **Deploy to Production:**
   - After staging verification
   - Deploy during low-traffic hours
   - **Deploy Kintone changes to production (nếu có)**
   - Monitor for issues
   - **Monitor Kintone API usage và rate limits**

5. **Update Status:**
   - In Review → Done
   - Update story với deployment info
   - **Document Kintone changes (nếu có)**

### Definition of Done (DoD)

Story is **Done** when:
- ✅ Code implemented và tested
- ✅ Unit tests pass (coverage ≥ 80%)
- ✅ Integration tests pass
- ✅ **Kintone integration tested (nếu story liên quan Kintone)**
  - ✅ Tested trên Kintone sandbox
  - ✅ Data sync verified (Rails → Kintone)
  - ✅ Kintone app configurations verified
  - ✅ Kintone dashboard updates verified (nếu có)
- ✅ Code review approved
- ✅ Documentation updated (nếu cần)
- ✅ **Kintone app schema changes documented (nếu có)**
- ✅ Deployed to staging
- ✅ **Kintone staging environment verified**
- ✅ Acceptance criteria met
- ✅ No critical bugs
- ✅ PM/Stakeholder approved (nếu user-facing)

---

## 🛠️ Tools & Infrastructure

### Development Tools

**Code Management:**
- **GitHub:** Source code, PRs, issues
- **Git:** Version control
- **GitHub Actions:** CI/CD pipeline

**Project Management:**
- **Jira/Linear/GitHub Projects:** Story tracking
- **Slack:** Team communication
- **Google Docs/Notion:** Documentation

**Development:**
- **VS Code/Cursor:** IDE
- **Rails:** Framework
- **RSpec:** Testing
- **Rubocop:** Linting
- **SimpleCov:** Coverage

**Kintone:**
- **Kintone Sandbox:** Testing environment
- **Kintone Production:** Production environment
- **Kintone REST API:** Integration API
- **krewDashboard Plugin:** Dashboard visualization

**Monitoring:**
- **Sentry:** Error tracking
- **New Relic/DataDog:** Performance monitoring
- **Uptime Robot:** Uptime monitoring
- **Kintone API Monitoring:** Rate limit tracking

### Infrastructure

**Staging Environment:**
- AWS EC2 instance
- MySQL database
- Redis server
- Sidekiq workers
- **Kintone Sandbox:** Testing Kintone integration

**Production Environment:**
- AWS EC2 instance (separate)
- MySQL database (separate)
- Redis server (separate)
- Sidekiq workers
- Load balancer (nếu cần)
- **Kintone Production:** Production Kintone apps

**CI/CD Pipeline:**
- GitHub Actions
- Automated tests
- Automated deployment
- Smoke tests
- **Kintone API tests (sandbox only)**

**Kintone Environments:**
- **Sandbox:** For development và testing
- **Production:** For live data
- **Access Control:** Separate credentials cho mỗi environment

---

## 📊 Tracking & Metrics

### Sprint Metrics

**Velocity:**
- Story points completed per sprint
- Track over 3+ sprints để predict capacity
- Adjust estimates based on actuals

**Burn-down Chart:**
- Remaining story points over time
- Update daily
- Identify if on track

**Cycle Time:**
- Time from story start to done
- Track per story
- Identify bottlenecks

**Lead Time:**
- Time from story created to done
- Measure end-to-end efficiency

### Quality Metrics

**Test Coverage:**
- Target: ≥ 80%
- Track per story và overall
- Use SimpleCov

**Code Review:**
- Review time
- Issues found per review
- Approval rate

**Bug Rate:**
- Bugs found per story
- Bugs in production
- Bug resolution time

**Kintone Integration Metrics:**
- Kintone API call success rate
- Kintone sync latency (Rails → Kintone)
- Kintone API rate limit usage
- Kintone app configuration errors

### Tracking Tools

**Sprint Dashboard:**
- Jira/Linear/GitHub Projects
- Real-time status
- Burn-down chart
- Velocity chart

**Code Quality:**
- GitHub PR reviews
- Code coverage reports
- Linter reports

**Monitoring:**
- Error tracking (Sentry)
- Performance monitoring
- Uptime monitoring

---

## 🚨 Risk Management

### Common Risks

**Technical Risks:**
- Integration complexity với Kintone
- Webhook reliability issues
- Performance bottlenecks
- Security vulnerabilities

**Process Risks:**
- Story estimation inaccurate
- Dependencies block progress
- Scope creep
- Resource availability

**Team Risks:**
- Knowledge gaps
- Communication issues
- Burnout

### Risk Mitigation

**Prevention:**
- Technical spikes cho complex stories
- Clear acceptance criteria
- Regular communication
- Knowledge sharing

**Response:**
- Escalate blockers immediately
- Adjust sprint scope nếu cần
- Re-prioritize backlog
- Get help from team/stakeholders

**Tracking:**
- Risk register trong sprint planning
- Update trong daily standups
- Review trong retrospectives

---

## 📅 3-Month Development Roadmap

### Month 1: Foundation & Core Automation

**Sprint 1 (Weeks 1-2):**
- Epic 1: Foundation & Infrastructure
  - Story 1.1: Project Setup
  - Story 1.2: Deployment Pipeline
  - Story 1.3: Core Infrastructure Services

**Sprint 2 (Weeks 3-4):**
- Epic 2: Order Processing Automation (Part 1)
  - Story 2.1: Webhook Reception
  - Story 2.2: Idempotency Implementation
  - Story 2.3: Kintone Integration Layer

### Month 2: Automation & Portal

**Sprint 3 (Weeks 5-6):**
- Epic 2: Order Processing Automation (Part 2)
  - Story 2.4: Order Sync to Kintone
  - Story 2.5: Inventory Check
  - Story 2.6: Multi-channel Routing

**Sprint 4 (Weeks 7-8):**
- Epic 3: Fulfillment Automation
  - Story 3.1: Picking Task Creation
  - Story 3.2: Ship&Co Integration
  - Story 3.3: Tracking Sync

- Epic 5: BtoB Portal (Part 1)
  - Story 5.1: Portal Setup
  - Story 5.2: Authentication

### Month 3: Completion & Polish

**Sprint 5 (Weeks 9-10):**
- Epic 5: BtoB Portal (Part 2)
  - Story 5.3: Order Placement
  - Story 5.4: Order Tracking

- Epic 6: Payment & Financial Automation
  - Story 6.1: GMO Bank Integration
  - Story 6.2: Payment Reconciliation

**Sprint 6 (Weeks 11-12):**
- Epic 7: Dashboard & Visibility
- Epic 9: Error Handling & Reliability
- Bug fixes và polish
- User acceptance testing
- Production deployment

---

## 📋 Daily/Weekly Checklist

### Daily Checklist (Developer)

**Morning:**
- [ ] Attend daily standup
- [ ] Update task status
- [ ] Review today's goals

**Development:**
- [ ] Work on assigned story
- [ ] Write tests first (TDD)
- [ ] Commit frequently với clear messages
- [ ] Update status when complete

**End of Day:**
- [ ] Push code to branch
- [ ] Update task status
- [ ] Note blockers for tomorrow

### Daily Checklist (PM/Scrum Master)

**Morning:**
- [ ] Facilitate daily standup
- [ ] Update sprint dashboard
- [ ] Resolve blockers

**During Day:**
- [ ] Review PRs
- [ ] Answer questions
- [ ] Coordinate với stakeholders
- [ ] Update backlog priorities

**End of Day:**
- [ ] Update sprint metrics
- [ ] Plan tomorrow's priorities
- [ ] Communicate updates

### Weekly Checklist (PM/Scrum Master)

**Monday:**
- [ ] Review previous sprint results
- [ ] Prepare sprint planning
- [ ] Update product backlog

**Friday:**
- [ ] Prepare sprint review
- [ ] Facilitate retrospective
- [ ] Update roadmap
- [ ] Communicate với stakeholders

---

## 🎯 Success Criteria per Epic

### Epic 1: Foundation & Infrastructure
- ✅ Rails project setup và running
- ✅ CI/CD pipeline working
- ✅ Health check endpoints respond
- ✅ All services (Redis, Sidekiq, MySQL) connected

### Epic 2: Order Processing Automation
- ✅ Webhook received từ Shopify
- ✅ Zero duplicate orders
- ✅ Orders synced to Kintone
- ✅ Inventory checked realtime

### Epic 3: Fulfillment Automation
- ✅ Picking tasks auto-created
- ✅ Shipping labels auto-generated
- ✅ Tracking synced to Shopify

### Epic 5: BtoB Portal
- ✅ Đại lý có thể login
- ✅ Đại lý có thể đặt hàng
- ✅ Đại lý có thể track orders

### Epic 6: Payment & Financial Automation
- ✅ Payments reconciled automatically
- ✅ Rebate calculated automatically
- ✅ Reports generated

---

## 📞 Communication Plan

### Team Communication

**Daily:**
- Standup: 9:00 AM (15 min)
- Slack channel: Real-time updates

**Weekly:**
- Sprint Planning: Monday (2-4 hours)
- Sprint Review: Friday (2 hours)
- Retrospective: Friday (1 hour)

**As Needed:**
- Technical discussions
- Blocker resolution
- Stakeholder updates

### Stakeholder Communication

**Weekly:**
- Status update email (Friday)
- Progress summary
- Upcoming priorities

**Monthly:**
- Demo session
- Feedback collection
- Roadmap review

**As Needed:**
- Critical issues
- Major decisions
- Change requests

---

## 🔧 Process Improvements

### Continuous Improvement

**After Each Sprint:**
- Retrospective insights
- Update development plan
- Refine processes
- Share learnings

**Monthly:**
- Review metrics
- Adjust estimates
- Update roadmap
- Team feedback

### Best Practices

**Development:**
- TDD (Test-Driven Development)
- Code review mandatory
- Documentation as code
- Frequent commits

**Process:**
- Regular retrospectives
- Transparent tracking
- Clear communication
- Blocker escalation

**Quality:**
- Test coverage targets
- Code review standards
- Definition of Done
- Staging verification

---

## 📚 Documentation

### Required Documents

**Planning:**
- PRD (`docs/prd.md`)
- Architecture (`docs/architecture.md`)
- Epics (`docs/epics.md`)
- This Development Plan

**Development:**
- API Documentation
- Setup Guide
- Deployment Guide
- Runbook

**Operations:**
- User Guide
- Troubleshooting Guide
- FAQ

### Documentation Standards

- Keep documentation up-to-date
- Write for target audience
- Include examples
- Review regularly

---

## ✅ Getting Started

### First Sprint Setup

1. **Setup Tools:**
   - Create Jira/Linear/GitHub Projects
   - Setup Slack channel
   - Setup CI/CD pipeline

2. **Import Stories:**
   - Import từ `docs/epics.md`
   - Create stories trong tracking tool
   - Set priorities

3. **Sprint Planning:**
   - Select Epic 1 stories
   - Estimate stories
   - Set sprint goal

4. **Start Development:**
   - Assign stories
   - Begin development
   - Track progress

### Team Onboarding

**New Developer:**
- Review PRD và Architecture
- Setup development environment
- Pair with experienced developer
- Review code standards

**New PM/Scrum Master:**
- Review all documentation
- Understand product goals
- Meet với stakeholders
- Review backlog

---

## 📝 Appendix: Templates

### Story Template

```
**Story ID:** {Epic-ID}-{Story-ID}
**Title:** {Story Title}
**Priority:** P0/P1/P2/P3
**Estimate:** {Story Points}

**Description:**
As a {user type}
I want {functionality}
So that {benefit}

**Acceptance Criteria:**
- Given {context}
- When {action}
- Then {expected result}

**Technical Notes:**
- {Implementation details}
- {Dependencies}
- {Testing requirements}

**Definition of Done:**
- [ ] Code implemented
- [ ] Tests written và pass
- [ ] Code reviewed
- [ ] Deployed to staging
- [ ] Acceptance criteria met
```

### Sprint Planning Template

```
**Sprint {Number}**
**Duration:** {Start Date} - {End Date}
**Sprint Goal:** {Goal}

**Stories Selected:**
- {Story 1} - {Estimate}
- {Story 2} - {Estimate}
- ...

**Total Story Points:** {Total}

**Risks & Blockers:**
- {Risk 1}
- {Risk 2}

**Action Items:**
- {Action 1} - Owner: {Name}
- {Action 2} - Owner: {Name}
```

### Retrospective Template

```
**Sprint {Number} Retrospective**
**Date:** {Date}

**What Went Well:**
- {Success 1}
- {Success 2}

**What Didn't Go Well:**
- {Problem 1}
- {Problem 2}

**What Should We Improve:**
- {Improvement 1}
- {Improvement 2}

**Action Items:**
- {Action 1} - Owner: {Name} - Due: {Date}
- {Action 2} - Owner: {Name} - Due: {Date}
```

---

**Document Status:** Complete  
**Next Steps:** Setup tools, import stories, begin Sprint 1

**Questions or Updates:** Update this document as process evolves based on team feedback và retrospectives.

