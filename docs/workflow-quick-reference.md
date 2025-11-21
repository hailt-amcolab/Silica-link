# Silica-link - Workflow Quick Reference

**Author:** Potato  
**Date:** 2025-11-20  
**Version:** 1.0

---

## 🎯 Sprint Overview

**Sprint Duration:** 2 weeks (10 working days)

**Sprint Activities:**
- **Day 1:** Sprint Planning (2-4 hours)
- **Daily:** Standup (15 min)
- **Last Day:** Sprint Review (2 hours) + Retrospective (1 hour)

---

## 📋 Story Lifecycle

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

## 🔄 Daily Workflow

### Morning (9:00 AM) - Daily Standup

**3 Questions:**
1. What did I complete yesterday?
2. What will I work on today?
3. What blockers do I have?

**Actions:**
- Update `sprint-status.yaml` với progress
- Resolve blockers immediately

### Development Day

**SM Tasks:**
- Run `*create-story` cho next story
- Run `*story-context` nếu cần
- Update story status

**DEV Tasks:**
- Run `*develop-story` để implement
- Run `*code-review` sau khi complete
- Run `*story-done` sau khi approved

---

## 🛠️ BMad Workflows

### SM (Scrum Master) Workflows

| Workflow | When | Purpose | Output |
|----------|------|---------|--------|
| `*sprint-planning` | Sprint start | Initialize sprint | `sprint-status.yaml` |
| `*epic-tech-context` | Per epic | Generate tech spec | `epic-{id}-context.md` |
| `*create-story` | Per story | Create story file | `stories/{epic-id}-{story-id}.md` |
| `*story-context` | Per story (optional) | Generate context | Story context XML |
| `*epic-retrospective` | After epic | Review epic | Retrospective report |

### DEV (Developer) Workflows

| Workflow | When | Purpose | Output |
|----------|------|---------|--------|
| `*develop-story` | Per story | Implement code | Working code + tests |
| `*code-review` | After development | Review code | Review feedback |
| `*story-done` | After review | Mark complete | Status updated |

---

## ✅ Definition of Done (DoD)

Story is **Done** when:
- ✅ Code implemented và tested
- ✅ Unit tests pass (coverage ≥ 80%)
- ✅ Integration tests pass
- ✅ Code review approved
- ✅ Documentation updated (nếu cần)
- ✅ Deployed to staging
- ✅ Acceptance criteria met
- ✅ No critical bugs

---

## 📊 Sprint Metrics

**Track These:**
- **Velocity:** Story points completed per sprint
- **Burn-down:** Remaining work over time
- **Cycle Time:** Story start to done
- **Test Coverage:** ≥ 80% target
- **Bug Rate:** Bugs per story

**Tracking File:**
- `docs/sprint-artifacts/sprint-status.yaml`

---

## 🚨 Blocker Escalation

1. **Developer:** Try to resolve independently
2. **Scrum Master:** SM helps resolve
3. **Product Owner:** Business decision needed
4. **Stakeholders:** Major blocker

**Use `*correct-course` workflow for mid-sprint changes**

---

## 📅 Sprint Calendar Example

### Week 1
- **Day 1:** Sprint Planning
- **Day 1-5:** Story development

### Week 2
- **Day 6-9:** Complete stories
- **Day 10:** Sprint Review + Retrospective

---

## 📚 Key Documents

- **Development Plan:** `docs/development-plan.md`
- **PRD:** `docs/prd.md`
- **Architecture:** `docs/architecture.md`
- **Epics:** `docs/epics.md`
- **Sprint Status:** `docs/sprint-artifacts/sprint-status.yaml`

---

**Quick Tips:**
- Update `sprint-status.yaml` after every action
- Run workflows in sequence
- Communicate blockers immediately
- Review code before marking done

