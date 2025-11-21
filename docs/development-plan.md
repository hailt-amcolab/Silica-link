# Silica-link - Development Plan & Scrum/Agile Workflow

**Author:** Potato  
**Date:** 2025-11-20  
**Version:** 1.0  
**Project:** Silica-link - Integrated Management Platform for CREDIE

---

## Executive Summary

Development plan này định nghĩa quy trình Scrum/Agile workflow cho Silica-link project, tích hợp với BMad Method workflows để đảm bảo development hiệu quả, chất lượng cao, và delivery đúng hạn.

**Key Principles:**
- **Incremental Value Delivery:** Mỗi sprint deliver working features
- **Quality First:** Test-driven development, code review mandatory
- **Transparency:** Real-time tracking, clear status visibility
- **Continuous Improvement:** Regular retrospectives và process refinement

---

## 1. Scrum Framework Overview

### 1.1 Sprint Structure

**Sprint Duration:** 2 weeks (10 working days)

**Sprint Activities:**
- **Sprint Planning:** Day 1 (2-4 hours)
- **Daily Standups:** 15 minutes mỗi ngày
- **Sprint Review:** Last day (2 hours)
- **Sprint Retrospective:** Last day (1 hour)

### 1.2 Sprint Goals

Mỗi sprint có một **Sprint Goal** rõ ràng:
- Epic-level goal (ví dụ: "Complete Epic 1 Foundation")
- Story-level goals (ví dụ: "Setup Rails project và deployment pipeline")

### 1.3 Definition of Done (DoD)

Story được coi là **Done** khi:
- ✅ Code implemented và tested
- ✅ Unit tests pass (coverage ≥ 80%)
- ✅ Integration tests pass
- ✅ Code review approved
- ✅ Documentation updated (nếu cần)
- ✅ Deployed to staging environment
- ✅ Acceptance criteria met
- ✅ No critical bugs

---

## 2. Team Roles & Responsibilities

### 2.1 Product Owner (PO)

**Responsibilities:**
- Define và prioritize product backlog
- Approve PRD và requirements
- Review sprint deliverables
- Make business decisions
- Communicate với stakeholders

**Key Artifacts:**
- PRD (`docs/prd.md`)
- Product Backlog (Epics trong `docs/epics.md`)

### 2.2 Scrum Master (SM)

**Responsibilities:**
- Facilitate Scrum ceremonies
- Remove impediments
- Maintain sprint artifacts
- Run BMad workflows:
  - `*sprint-planning`
  - `*epic-tech-context`
  - `*create-story`
  - `*story-context`
  - `*epic-retrospective`
  - `*correct-course`

**Key Artifacts:**
- Sprint Status (`docs/sprint-artifacts/sprint-status.yaml`)
- Epic Tech Contexts (`docs/epic-*-context.md`)
- Story Files (`docs/sprint-artifacts/stories/`)

### 2.3 Developer (DEV)

**Responsibilities:**
- Implement stories với tests
- Perform code reviews
- Update story status
- Run BMad workflows:
  - `*develop-story`
  - `*code-review`
  - `*story-done`

**Key Deliverables:**
- Working code với tests
- Code review feedback
- Technical documentation (nếu cần)

### 2.4 Quality Assurance (QA) - Optional

**Responsibilities:**
- Test stories sau khi done
- Report bugs
- Validate acceptance criteria
- Maintain test documentation

---

## 3. Sprint Planning Process

### 3.1 Pre-Sprint Planning

**Inputs:**
- Product Backlog (Epics trong `docs/epics.md`)
- Previous sprint retrospective insights
- Team velocity data
- Dependencies và blockers

**Activities:**
1. Review Epic priorities
2. Identify Epic tech context needs
3. Estimate story complexity
4. Identify dependencies

### 3.2 Sprint Planning Meeting

**Duration:** 2-4 hours

**Agenda:**

1. **Review Sprint Goal** (15 min)
   - PO presents sprint goal
   - Team discusses và aligns

2. **Select Stories** (60-90 min)
   - SM runs `*epic-tech-context` cho epics trong sprint
   - SM runs `*create-story` cho stories từ epic backlog
   - Team estimates stories (Story Points hoặc T-shirt sizing)
   - Team commits to stories based on velocity

3. **Break Down Tasks** (30-60 min)
   - For each story, identify:
     - Technical tasks
     - Testing tasks
     - Documentation tasks
     - Dependencies

4. **Identify Risks & Blockers** (15 min)
   - Discuss potential impediments
   - Plan mitigation strategies

5. **Finalize Sprint Backlog** (15 min)
   - Update `sprint-status.yaml` với selected stories
   - Set story status to `ready-for-dev`
   - Confirm sprint goal

**Outputs:**
- Sprint Backlog (stories trong `sprint-status.yaml`)
- Sprint Goal
- Risk register

### 3.3 BMad Workflow Integration

**Sprint Planning Workflow:**
```
SM: *sprint-planning
  → Initialize sprint-status.yaml
  → Set epic priorities
  → Mark stories as backlog
```

**Epic Tech Context (Per Epic):**
```
SM: *epic-tech-context
  → Generate epic-{id}-context.md
  → Update epic status to "contexted"
```

---

## 4. Daily Standup Process

### 4.1 Standup Format

**Duration:** 15 minutes  
**Time:** 9:00 AM (hoặc agreed time)  
**Format:** Round-robin, mỗi người trả lời 3 questions

**3 Questions:**
1. **What did I complete yesterday?**
   - Stories completed
   - Code reviews done
   - Blockers resolved

2. **What will I work on today?**
   - Stories to start/continue
   - Code reviews to do
   - Tasks planned

3. **What blockers do I have?**
   - Technical blockers
   - Dependencies waiting
   - Questions cần answered

### 4.2 Standup Best Practices

- **Be concise:** Max 2 minutes per person
- **Focus on blockers:** Don't wait, raise immediately
- **Update status:** Update `sprint-status.yaml` sau standup
- **Track metrics:** Note story progress

### 4.3 Post-Standup Actions

**SM Actions:**
- Update `sprint-status.yaml` với progress
- Resolve blockers immediately
- Schedule follow-up meetings nếu cần

**DEV Actions:**
- Update story status:
  - `backlog` → `in-progress` (khi bắt đầu)
  - `in-progress` → `review` (khi complete)
  - `review` → `done` (sau code review)

---

## 5. Story Development Workflow

### 5.1 Story Lifecycle States

```
backlog → drafted → ready-for-dev → in-progress → review → done
```

**State Definitions:**
- **backlog:** Story exists trong epic file, chưa được draft
- **drafted:** Story file created trong `docs/sprint-artifacts/stories/`
- **ready-for-dev:** Story approved, context ready, có thể bắt đầu implement
- **in-progress:** Developer đang actively working
- **review:** Implementation complete, awaiting code review
- **done:** Code review passed, story complete

### 5.2 Story Development Process

#### Step 1: Story Creation (SM)

**Workflow:** `*create-story`

**Activities:**
1. SM selects next story từ epic backlog
2. SM runs `*create-story` workflow
3. Story file created trong `docs/sprint-artifacts/stories/`
4. Story status updated to `drafted`

**Output:**
- Story file: `docs/sprint-artifacts/stories/{epic-id}-{story-id}-{title}.md`

#### Step 2: Story Context (SM) - Optional

**Workflow:** `*story-context`

**Activities:**
1. SM runs `*story-context` để generate dynamic context
2. Context includes:
   - Related code references
   - Architecture decisions
   - Dependencies
   - Test strategy

**Alternative:** SM can mark story `ready-for-dev` directly nếu context không cần

#### Step 3: Development (DEV)

**Workflow:** `*develop-story`

**Activities:**
1. DEV updates story status to `in-progress`
2. DEV reads story file và context
3. DEV implements code với tests:
   - Write failing tests first (TDD)
   - Implement code
   - Make tests pass
   - Refactor
4. DEV runs tests locally
5. DEV updates story status to `review`

**Best Practices:**
- Follow TDD (Test-Driven Development)
- Write clean, maintainable code
- Follow coding standards
- Update documentation nếu cần
- Commit frequently với clear messages

#### Step 4: Code Review (DEV)

**Workflow:** `*code-review`

**Activities:**
1. DEV runs `*code-review` workflow
2. Review includes:
   - Code quality check
   - Test coverage check
   - Architecture alignment
   - Security review
   - Performance check
3. If issues found:
   - DEV fixes issues
   - Re-run `*develop-story`
   - Re-run `*code-review`
4. If approved:
   - DEV runs `*story-done`
   - Story status updated to `done`

**Code Review Checklist:**
- ✅ Code follows project conventions
- ✅ Tests pass và coverage ≥ 80%
- ✅ No security vulnerabilities
- ✅ Performance acceptable
- ✅ Documentation updated
- ✅ Acceptance criteria met

#### Step 5: Story Done (DEV)

**Workflow:** `*story-done`

**Activities:**
1. DEV runs `*story-done` workflow
2. Story status updated to `done`
3. Story removed from active sprint backlog
4. Metrics updated (velocity, burn-down)

### 5.3 BMad Workflow Integration

**Complete Story Flow:**
```
SM: *create-story
  → Story file created
  → Status: drafted

SM: *story-context (optional)
  → Context generated
  → Status: ready-for-dev

DEV: *develop-story
  → Code implemented
  → Tests written
  → Status: review

DEV: *code-review
  → Code reviewed
  → If approved: proceed
  → If issues: back to develop-story

DEV: *story-done
  → Story complete
  → Status: done
  → Metrics updated
```

---

## 6. Code Review Process

### 6.1 Review Criteria

**Code Quality:**
- Follows Ruby/Rails conventions
- Clean code principles (SOLID, DRY)
- Proper error handling
- No code smells

**Testing:**
- Unit tests exist và pass
- Integration tests exist và pass
- Test coverage ≥ 80%
- Edge cases covered

**Architecture:**
- Follows architecture decisions
- Uses correct patterns (Service Layer, Adapter)
- No circular dependencies
- Proper separation of concerns

**Security:**
- No SQL injection risks
- No XSS vulnerabilities
- Proper authentication/authorization
- Secrets not hardcoded

**Performance:**
- No N+1 queries
- Proper caching strategy
- Efficient algorithms
- Resource cleanup

### 6.2 Review Process

1. **Automated Checks:**
   - Linter passes
   - Tests pass
   - Coverage meets threshold

2. **Manual Review:**
   - Code readability
   - Architecture alignment
   - Business logic correctness

3. **Feedback:**
   - Constructive comments
   - Specific suggestions
   - Reference to standards

4. **Approval:**
   - Approve nếu all criteria met
   - Request changes nếu issues found

### 6.3 Review Workflow

**Using BMad `*code-review`:**
- Automated review checks
- Manual review guidance
- Checklist validation
- Approval/rejection decision

---

## 7. Sprint Review Process

### 7.1 Sprint Review Meeting

**Duration:** 2 hours  
**When:** Last day of sprint  
**Attendees:** PO, SM, DEV, Stakeholders

**Agenda:**

1. **Demo Completed Stories** (60 min)
   - DEV demos each completed story
   - Show working features
   - Answer questions

2. **Review Sprint Goal** (15 min)
   - Did we achieve sprint goal?
   - What was delivered?
   - What was not delivered? (và why)

3. **Stakeholder Feedback** (30 min)
   - Collect feedback
   - Discuss priorities
   - Answer questions

4. **Update Product Backlog** (15 min)
   - Update priorities based on feedback
   - Add new requirements nếu cần
   - Plan next sprint

**Outputs:**
- Sprint Review Report
- Updated Product Backlog
- Next Sprint Priorities

### 7.2 Demo Format

**Per Story Demo:**
- Show feature working
- Explain technical implementation
- Show tests passing
- Answer questions

**Demo Environment:**
- Staging environment
- Test data prepared
- All features integrated

---

## 8. Sprint Retrospective Process

### 8.1 Retrospective Meeting

**Duration:** 1 hour  
**When:** After Sprint Review  
**Attendees:** SM, DEV (PO optional)

**Format:** Start-Stop-Continue

**Agenda:**

1. **What Went Well?** (Start) - 15 min
   - Identify successes
   - Practices to continue

2. **What Didn't Go Well?** (Stop) - 15 min
   - Identify problems
   - Practices to stop

3. **What Should We Improve?** (Continue) - 15 min
   - Identify improvements
   - Action items

4. **Action Items** (15 min)
   - Define specific actions
   - Assign owners
   - Set deadlines

### 8.2 Epic Retrospective (BMad)

**Workflow:** `*epic-retrospective`

**When:** After epic complete

**Activities:**
1. SM runs `*epic-retrospective`
2. Review epic completion
3. Extract lessons learned
4. Update process nếu cần

**Output:**
- Epic Retrospective Report
- Process improvements
- Knowledge base updates

### 8.3 Retrospective Outputs

**Document:**
- Retrospective notes
- Action items với owners
- Process improvements

**Update:**
- Development plan (nếu process changes)
- Team practices
- Tools và workflows

---

## 9. Metrics & Tracking

### 9.1 Sprint Metrics

**Velocity:**
- Story points completed per sprint
- Track over time để predict capacity

**Burn-down Chart:**
- Remaining work over time
- Identify if on track

**Cycle Time:**
- Time from story start to done
- Identify bottlenecks

**Lead Time:**
- Time from story created to done
- Measure end-to-end efficiency

### 9.2 Quality Metrics

**Test Coverage:**
- Target: ≥ 80%
- Track per story và overall

**Code Review:**
- Review time
- Issues found per review
- Approval rate

**Bug Rate:**
- Bugs found per story
- Bugs in production
- Bug resolution time

### 9.3 Tracking Tools

**Sprint Status:**
- `docs/sprint-artifacts/sprint-status.yaml`
- Real-time status tracking
- Epic và story progress

**BMad Workflows:**
- Automated status updates
- Metrics collection
- Report generation

**Additional Tools:**
- GitHub Issues (nếu cần)
- CI/CD pipeline metrics
- Monitoring dashboards

---

## 10. Tools & Artifacts

### 10.1 BMad Method Workflows

**SM Workflows:**
- `*sprint-planning` - Initialize sprint
- `*epic-tech-context` - Generate epic tech spec
- `*create-story` - Create story file
- `*story-context` - Generate story context
- `*epic-retrospective` - Epic review

**DEV Workflows:**
- `*develop-story` - Implement story
- `*code-review` - Review code
- `*story-done` - Mark story complete

**Support Workflows:**
- `*correct-course` - Handle mid-sprint changes
- `*workflow-status` - Check current status

### 10.2 Project Artifacts

**Planning Documents:**
- `docs/prd.md` - Product Requirements
- `docs/architecture.md` - System Architecture
- `docs/epics.md` - Epic Breakdown

**Sprint Artifacts:**
- `docs/sprint-artifacts/sprint-status.yaml` - Sprint tracking
- `docs/epic-*-context.md` - Epic tech contexts
- `docs/sprint-artifacts/stories/` - Story files

**Development:**
- Source code trong `app/`, `lib/`, `spec/`
- Tests trong `spec/`
- Documentation trong `docs/`

### 10.3 Communication Tools

**Daily Standups:**
- In-person hoặc video call
- Status updates trong `sprint-status.yaml`

**Sprint Planning/Review:**
- In-person meetings
- Shared documents

**Code Review:**
- BMad `*code-review` workflow
- GitHub Pull Requests (nếu cần)

---

## 11. Risk Management

### 11.1 Common Risks

**Technical Risks:**
- Integration complexity với Kintone
- Webhook reliability issues
- Performance bottlenecks

**Process Risks:**
- Story estimation inaccurate
- Dependencies block progress
- Scope creep

**Team Risks:**
- Knowledge gaps
- Resource availability
- Communication issues

### 11.2 Risk Mitigation

**Prevention:**
- Epic tech context trước khi start
- Story context để clarify requirements
- Regular standups để catch issues early

**Response:**
- `*correct-course` workflow cho mid-sprint changes
- Escalate blockers immediately
- Adjust sprint scope nếu cần

**Tracking:**
- Risk register trong sprint planning
- Update trong daily standups
- Review trong retrospectives

---

## 12. Continuous Improvement

### 12.1 Process Refinement

**After Each Sprint:**
- Retrospective insights
- Update development plan
- Refine workflows

**After Each Epic:**
- Epic retrospective
- Update practices
- Share learnings

### 12.2 Best Practices

**Development:**
- TDD (Test-Driven Development)
- Code review mandatory
- Documentation as code

**Process:**
- Regular retrospectives
- Transparent tracking
- Clear communication

**Quality:**
- Test coverage targets
- Code review standards
- Definition of Done

---

## 13. Sprint Calendar Example

### Sprint 1 (Weeks 1-2)

**Week 1:**
- **Day 1:** Sprint Planning (Epic 1)
- **Day 1-2:** Story 1.1 (Project Setup)
- **Day 3-4:** Story 1.2 (Deployment Pipeline)
- **Day 5:** Story 1.3 (Core Infrastructure)

**Week 2:**
- **Day 6-7:** Complete Story 1.3
- **Day 8-9:** Testing và bug fixes
- **Day 10:** Sprint Review + Retrospective

### Sprint 2 (Weeks 3-4)

**Week 3:**
- **Day 1:** Sprint Planning (Epic 2)
- **Day 1-5:** Epic 2 stories (Order Processing)

**Week 4:**
- **Day 6-9:** Complete Epic 2
- **Day 10:** Sprint Review + Retrospective

---

## 14. Definition of Ready (DoR)

Story được coi là **Ready for Development** khi:
- ✅ Epic tech context exists
- ✅ Story file created
- ✅ Acceptance criteria clear
- ✅ Dependencies identified
- ✅ Technical approach defined
- ✅ No blockers

---

## 15. Escalation Process

### 15.1 Blocker Escalation

**Level 1: Developer**
- Try to resolve independently
- Ask team trong standup

**Level 2: Scrum Master**
- SM helps resolve
- Coordinate với team

**Level 3: Product Owner**
- Business decision needed
- Priority change needed

**Level 4: Stakeholders**
- Major blocker
- Resource needed

### 15.2 Change Management

**Mid-Sprint Changes:**
- Use `*correct-course` workflow
- Assess impact
- Update sprint goal nếu cần
- Communicate với team

---

## 16. Success Criteria

**Sprint Success:**
- Sprint goal achieved
- All committed stories done
- Quality standards met
- Team velocity maintained

**Project Success:**
- MVP delivered on time
- 100% BtoC orders automated
- Operations team satisfied
- Zero duplicate orders

---

## Appendix A: Workflow Quick Reference

### SM Workflows

| Workflow | When | Purpose |
|----------|------|---------|
| `*sprint-planning` | Sprint start | Initialize sprint |
| `*epic-tech-context` | Per epic | Generate tech spec |
| `*create-story` | Per story | Create story file |
| `*story-context` | Per story (optional) | Generate context |
| `*epic-retrospective` | After epic | Review epic |

### DEV Workflows

| Workflow | When | Purpose |
|----------|------|---------|
| `*develop-story` | Per story | Implement code |
| `*code-review` | After development | Review code |
| `*story-done` | After review | Mark complete |

---

## Appendix B: Story Status Flow

```
backlog
  ↓ (SM: *create-story)
drafted
  ↓ (SM: *story-context hoặc *story-ready)
ready-for-dev
  ↓ (DEV: starts work)
in-progress
  ↓ (DEV: completes code)
review
  ↓ (DEV: *code-review)
  ├─→ (if issues) → in-progress
  └─→ (if approved) → done
done
```

---

**Document Status:** Complete  
**Next Steps:** Begin Sprint 1 với Epic 1

**Questions or Updates:** Update this document as process evolves based on team feedback và retrospectives.

