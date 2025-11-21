# Silica-link - Sprint Checklist & Quick Reference

**For:** PM/Scrum Master & Development Team  
**Version:** 1.0

---

## 📅 Sprint Calendar

### Week 1
- **Monday:** Sprint Planning (2-4 hours)
- **Tuesday-Friday:** Development + Daily Standups

### Week 2
- **Monday-Thursday:** Development + Daily Standups
- **Friday:** Sprint Review (2 hours) + Retrospective (1 hour)

---

## ✅ Sprint Planning Checklist

### Before Planning
- [ ] Review previous sprint results
- [ ] Update product backlog priorities
- [ ] Prepare epic/story list
- [ ] Check dependencies và blockers
- [ ] Schedule meeting (2-4 hours)

### During Planning
- [ ] Set sprint goal
- [ ] Select stories từ backlog
- [ ] Estimate stories (Story Points)
- [ ] Break down stories into tasks
- [ ] Identify risks và blockers
- [ ] Assign stories to developers
- [ ] Create sprint backlog trong tracking tool

### After Planning
- [ ] Update sprint dashboard
- [ ] Communicate sprint goal to team
- [ ] Share sprint backlog
- [ ] Setup tracking (Jira/Linear/GitHub Projects)

---

## ✅ Daily Standup Checklist

### Before Standup
- [ ] Review yesterday's work
- [ ] Plan today's tasks
- [ ] Identify blockers

### During Standup (15 min)
- [ ] What did I complete yesterday?
- [ ] What will I work on today?
- [ ] What blockers do I have?

### After Standup
- [ ] Update task status trong tracking tool
- [ ] Resolve blockers immediately
- [ ] Schedule follow-up meetings nếu cần

---

## ✅ Story Development Checklist

### Developer - Starting Story
- [ ] Read story description và acceptance criteria
- [ ] Clarify questions với PM
- [ ] Create feature branch: `feature/epic-{id}-story-{id}-{title}`
- [ ] Update status: To Do → In Progress

### Developer - During Development
- [ ] Write tests first (TDD)
- [ ] Implement code
- [ ] Make tests pass
- [ ] Refactor code
- [ ] Run tests locally: `bundle exec rspec`
- [ ] Run linter: `bundle exec rubocop`
- [ ] Commit frequently với clear messages

### Developer - Completing Story
- [ ] All tests pass
- [ ] Code coverage ≥ 80%
- [ ] Documentation updated (nếu cần)
- [ ] Create Pull Request
- [ ] Link PR to story
- [ ] Request code review
- [ ] Update status: In Progress → In Review

### Code Reviewer
- [ ] Review code quality
- [ ] Check test coverage
- [ ] Verify security
- [ ] Check performance
- [ ] Validate acceptance criteria
- [ ] Approve or request changes

### Developer - After Review
- [ ] Address review feedback
- [ ] Push updates
- [ ] Re-request review nếu needed
- [ ] Merge PR after approval
- [ ] Deploy to staging
- [ ] Verify functionality
- [ ] Update status: In Review → Done

---

## ✅ Definition of Done Checklist

Story is **Done** when:
- [ ] Code implemented và tested
- [ ] Unit tests pass (coverage ≥ 80%)
- [ ] Integration tests pass
- [ ] Code review approved
- [ ] Documentation updated (nếu cần)
- [ ] Deployed to staging
- [ ] Acceptance criteria met
- [ ] No critical bugs
- [ ] PM/Stakeholder approved (nếu user-facing)

---

## ✅ Sprint Review Checklist

### Before Review
- [ ] Prepare demo environment
- [ ] Test all completed stories
- [ ] Prepare demo script
- [ ] Invite stakeholders

### During Review (2 hours)
- [ ] Demo each completed story
- [ ] Show working features
- [ ] Answer questions
- [ ] Collect feedback
- [ ] Review sprint goal achievement
- [ ] Discuss next sprint priorities

### After Review
- [ ] Document feedback
- [ ] Update product backlog
- [ ] Communicate updates to stakeholders
- [ ] Plan next sprint

---

## ✅ Retrospective Checklist

### Before Retrospective
- [ ] Review sprint metrics
- [ ] Collect team feedback
- [ ] Prepare retrospective format

### During Retrospective (1 hour)
- [ ] What went well? (15 min)
- [ ] What didn't go well? (15 min)
- [ ] What should we improve? (15 min)
- [ ] Action items (15 min)

### After Retrospective
- [ ] Document action items với owners
- [ ] Update development plan nếu cần
- [ ] Share learnings với team
- [ ] Follow up on action items

---

## 🚨 Blocker Escalation

### Level 1: Developer
- Try to resolve independently
- Ask team trong standup

### Level 2: Scrum Master
- SM helps resolve
- Coordinate với team

### Level 3: Product Owner
- Business decision needed
- Priority change needed

### Level 4: Stakeholders
- Major blocker
- Resource needed

**Action:** Escalate immediately, don't wait!

---

## 📊 Daily Metrics Update

### Update These Daily:
- [ ] Story status (In Progress → Review → Done)
- [ ] Burn-down chart
- [ ] Blocker status
- [ ] Velocity tracking

### Update These Weekly:
- [ ] Sprint progress
- [ ] Test coverage
- [ ] Bug rate
- [ ] Team velocity

---

## 🛠️ Tools Quick Reference

### Development
- **Git:** `git checkout -b feature/epic-{id}-story-{id}`
- **Tests:** `bundle exec rspec`
- **Linter:** `bundle exec rubocop`
- **Coverage:** `bundle exec rspec --format documentation`

### Tracking
- **Jira/Linear/GitHub Projects:** Story status
- **Slack:** Team communication
- **GitHub:** PRs và code review

### Monitoring
- **Sentry:** Error tracking
- **New Relic/DataDog:** Performance
- **Uptime Robot:** Uptime

---

## 📞 Communication Channels

### Daily
- **Standup:** 9:00 AM (15 min)
- **Slack:** Real-time updates

### Weekly
- **Sprint Planning:** Monday (2-4 hours)
- **Sprint Review:** Friday (2 hours)
- **Retrospective:** Friday (1 hour)

### As Needed
- Technical discussions
- Blocker resolution
- Stakeholder updates

---

## 🎯 Story Estimation Guide

| Points | Time | Complexity | Example |
|--------|------|------------|---------|
| 1 | < 4 hours | Simple | Bug fix |
| 2 | 4-8 hours | Small | Small feature |
| 3 | 1-2 days | Medium | Medium feature |
| 5 | 2-3 days | Complex | Complex feature |
| 8 | 3-5 days | Very Complex | Very complex feature |
| 13 | > 5 days | Epic | Should break down |

---

## 📋 Story Template

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
```

---

## 🔄 Git Workflow

### Branch Naming
```
feature/epic-{id}-story-{id}-{title}
bugfix/epic-{id}-story-{id}-{title}
hotfix/{description}
```

### Commit Messages
```
feat(epic-1): Add health check endpoints
fix(epic-2): Fix webhook idempotency issue
test(epic-3): Add integration tests for Ship&Co
docs: Update API documentation
```

### PR Template
```
## Description
{What this PR does}

## Related Story
{Story ID and link}

## Changes
- {Change 1}
- {Change 2}

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing done

## Checklist
- [ ] Code reviewed
- [ ] Tests pass
- [ ] Documentation updated
```

---

## ⚡ Quick Commands

### Development
```bash
# Setup
bundle install
rails db:create db:migrate

# Testing
bundle exec rspec
bundle exec rspec spec/models/order_spec.rb

# Linting
bundle exec rubocop
bundle exec rubocop -a  # Auto-fix

# Coverage
COVERAGE=true bundle exec rspec
open coverage/index.html
```

### Deployment
```bash
# Staging
git push origin main  # Auto-deploys via CI/CD

# Production
# Deploy via CI/CD pipeline during low-traffic hours
```

---

**Keep this checklist handy during sprints!**

