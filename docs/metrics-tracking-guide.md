# Silica-link - Metrics Tracking Guide

**Author:** Potato (PM & Scrum Master)  
**Date:** 2025-11-20  
**Version:** 1.0

---

## 📊 Overview

Document này hướng dẫn cách tính toán, theo dõi, và áp dụng các metrics quan trọng cho Silica-link project. Mỗi metric có:
- **Công thức tính toán**
- **Cách theo dõi**
- **Tools sử dụng**
- **Người chịu trách nhiệm**
- **Target values**

---

## 1. Burndown Chart

### 1.1 Mục đích

Burndown chart theo dõi **remaining work** trong sprint để:
- Xác định team có on track không
- Phát hiện sớm nếu sprint có risk không hoàn thành
- Điều chỉnh scope nếu cần

### 1.2 Công thức tính toán

**Remaining Story Points = Total Story Points - Completed Story Points**

**Ideal Burndown Line:**
- Start: Total story points selected trong sprint planning
- End: 0 story points (tất cả stories done)
- Slope: Linear decrease từ start đến end

**Actual Burndown Line:**
- Track remaining story points mỗi ngày
- So sánh với ideal line

### 1.3 Cách theo dõi

**Daily Update:**
1. Tính remaining story points:
   ```
   Remaining = Total - (Done Stories Points + In Progress Stories Points * 0.5)
   ```
   - Done stories: 100% complete
   - In Progress stories: 50% complete (estimate)

2. Plot point trên chart:
   - X-axis: Days in sprint (Day 1, 2, 3...)
   - Y-axis: Remaining story points

3. So sánh với ideal line:
   - **Above ideal:** Behind schedule, cần action
   - **Below ideal:** Ahead of schedule, có thể add more work
   - **On ideal:** On track

### 1.4 Example Calculation

**Sprint Planning (Day 1):**
- Total story points: 21
- Stories selected: 5 stories (3, 5, 5, 5, 3 points)

**Day 3:**
- Done: 1 story (3 points) = 3 points
- In Progress: 2 stories (5 + 5 points) = 10 points * 0.5 = 5 points
- Remaining: 21 - (3 + 5) = **13 points**

**Day 5:**
- Done: 3 stories (3 + 5 + 5 points) = 13 points
- In Progress: 1 story (5 points) = 5 points * 0.5 = 2.5 points
- Remaining: 21 - (13 + 2.5) = **5.5 points**

**Day 10 (End of Sprint):**
- Done: 5 stories (all 21 points) = 21 points
- Remaining: 21 - 21 = **0 points** ✅

### 1.5 Tools

**Manual (Excel/Google Sheets):**
```
Day | Total | Done | In Progress | Remaining | Ideal
1   | 21    | 0    | 0           | 21        | 21
2   | 21    | 0    | 10          | 16        | 18.7
3   | 21    | 3    | 10          | 13        | 16.4
...
10  | 21    | 21   | 0           | 0         | 0
```

**Automated Tools:**
- **Jira:** Built-in burndown chart
- **Linear:** Sprint burndown
- **GitHub Projects:** Burndown chart
- **Monday.com:** Burndown widget

### 1.6 Người chịu trách nhiệm

**Primary:** **PM/Scrum Master**
- Update chart daily sau standup
- Analyze trends
- Alert team nếu behind schedule
- Suggest actions (scope adjustment, help needed)

**Support:** **Developers**
- Update story status accurately
- Complete stories on time
- Communicate blockers early

### 1.7 Target & Action Thresholds

**Target:**
- Burndown line should track close to ideal line
- Complete all stories by end of sprint

**Action Thresholds:**
- **Day 3-4:** If > 20% behind ideal → Review scope
- **Day 5-6:** If > 30% behind ideal → Remove low-priority stories
- **Day 7+:** If > 40% behind ideal → Escalate, adjust sprint goal

### 1.8 Template

**Excel/Google Sheets Template:**

| Day | Date | Total SP | Done SP | In Progress SP | Remaining SP | Ideal SP | Variance |
|-----|------|----------|---------|----------------|-------------|----------|----------|
| 1   | Mon  | 21       | 0       | 0              | 21          | 21       | 0        |
| 2   | Tue  | 21       | 0       | 10             | 16          | 18.7     | -2.7     |
| 3   | Wed  | 21       | 3       | 10             | 13          | 16.4     | -3.4     |
| ... | ...  | ...      | ...     | ...            | ...         | ...      | ...      |
| 10  | Fri  | 21       | 21      | 0              | 0           | 0        | 0        |

**Formula:**
- Remaining SP = Total SP - (Done SP + In Progress SP * 0.5)
- Ideal SP = Total SP * (1 - (Day - 1) / Total Days)
- Variance = Remaining SP - Ideal SP

---

## 2. Velocity

### 2.1 Mục đích

Velocity đo **team capacity** (story points completed per sprint) để:
- Predict future sprint capacity
- Plan sprint scope accurately
- Identify trends (improving/declining)

### 2.2 Công thức tính toán

**Sprint Velocity = Total Story Points Completed trong Sprint**

**Average Velocity = Sum of Last 3-5 Sprint Velocities / Number of Sprints**

**Velocity Range = (Min Velocity, Max Velocity) từ last 3-5 sprints**

### 2.3 Cách theo dõi

**Per Sprint:**
1. Track completed story points:
   - Only count stories that are **Done** (Definition of Done met)
   - Don't count partial completion
   - Don't count stories moved to next sprint

2. Record velocity:
   ```
   Sprint 1 Velocity = 18 points
   Sprint 2 Velocity = 21 points
   Sprint 3 Velocity = 19 points
   ```

**Calculate Average:**
```
Average Velocity (last 3 sprints) = (18 + 21 + 19) / 3 = 19.3 points
```

**Predict Next Sprint:**
- Use average velocity: **19-20 points**
- Or use velocity range: **18-21 points** (min-max)

### 2.4 Example Calculation

**Sprint 1:**
- Stories completed: 5 stories (3, 5, 5, 3, 2 points)
- **Velocity = 18 points**

**Sprint 2:**
- Stories completed: 6 stories (3, 5, 5, 5, 2, 1 points)
- **Velocity = 21 points**

**Sprint 3:**
- Stories completed: 4 stories (5, 5, 5, 4 points)
- **Velocity = 19 points**

**Average Velocity:**
```
(18 + 21 + 19) / 3 = 19.3 points
```

**Next Sprint Planning:**
- Select stories totaling **~19-20 points**
- Don't over-commit (select 25 points)
- Don't under-commit (select 15 points)

### 2.5 Tools

**Manual (Excel/Google Sheets):**
```
Sprint | Completed SP | Notes
1      | 18           | First sprint, learning curve
2      | 21           | Team more efficient
3      | 19           | Some complexity
4      | 20           | Stable
5      | 22           | Improving

Average (last 3): 20.3 points
Range: 19-22 points
```

**Automated Tools:**
- **Jira:** Velocity chart (automatic)
- **Linear:** Sprint velocity tracking
- **GitHub Projects:** Velocity report
- **Monday.com:** Velocity widget

### 2.6 Người chịu trách nhiệm

**Primary:** **PM/Scrum Master**
- Calculate velocity after each sprint
- Track velocity trends
- Use velocity để plan next sprint
- Identify velocity changes và root causes

**Support:** **Developers**
- Complete stories to Definition of Done
- Accurate story estimation
- Consistent estimation scale

### 2.7 Target & Analysis

**Target:**
- Stable velocity (variance < 20%)
- Improving velocity over time (team learning)
- Velocity matches team capacity

**Analysis:**
- **Velocity decreasing:** Identify blockers, complexity, team issues
- **Velocity increasing:** Team improving, process working
- **Velocity unstable:** Review estimation accuracy, story sizing

### 2.8 Template

**Velocity Tracking Sheet:**

| Sprint | Start Date | End Date | Completed SP | Notes | Velocity Trend |
|--------|------------|----------|--------------|-------|----------------|
| 1      | 2025-01-06 | 2025-01-17 | 18 | First sprint | Baseline |
| 2      | 2025-01-20 | 2025-01-31 | 21 | Team efficient | ↑ +16% |
| 3      | 2025-02-03 | 2025-02-14 | 19 | Some complexity | ↓ -9% |
| 4      | 2025-02-17 | 2025-02-28 | 20 | Stable | ↑ +5% |
| 5      | 2025-03-03 | 2025-03-14 | 22 | Improving | ↑ +10% |

**Average Velocity (last 3):** 20.3 points  
**Velocity Range:** 19-22 points  
**Trend:** Improving (+10% from baseline)

**Next Sprint Prediction:** 20-21 points

---

## 3. Lead Time

### 3.1 Mục đích

Lead time đo **end-to-end time** từ story creation đến completion để:
- Measure delivery efficiency
- Identify bottlenecks
- Predict delivery dates
- Improve process

### 3.2 Công thức tính toán

**Lead Time = Story Completion Date - Story Creation Date**

**Average Lead Time = Sum of Lead Times / Number of Stories**

**Lead Time by Epic = Average Lead Time for stories in epic**

### 3.3 Cách theo dõi

**Per Story:**
1. Record dates:
   - **Creation Date:** When story created trong backlog
   - **Start Date:** When story moved to "In Progress"
   - **Completion Date:** When story moved to "Done"

2. Calculate lead time:
   ```
   Story 2.1:
   - Created: 2025-01-06
   - Started: 2025-01-08
   - Completed: 2025-01-12
   - Lead Time = 2025-01-12 - 2025-01-06 = 6 days
   ```

**Calculate Average:**
```
Sprint 1 Stories:
- Story 1.1: 5 days
- Story 1.2: 7 days
- Story 1.3: 4 days
- Story 2.1: 6 days
- Story 2.2: 8 days

Average Lead Time = (5 + 7 + 4 + 6 + 8) / 5 = 6 days
```

### 3.4 Example Calculation

**Sprint 1 Stories:**

| Story | Created | Started | Completed | Lead Time (days) |
|-------|---------|---------|-----------|------------------|
| 1.1   | 2025-01-06 | 2025-01-07 | 2025-01-11 | 5 |
| 1.2   | 2025-01-06 | 2025-01-08 | 2025-01-14 | 8 |
| 1.3   | 2025-01-06 | 2025-01-10 | 2025-01-13 | 7 |
| 2.1   | 2025-01-06 | 2025-01-08 | 2025-01-12 | 6 |
| 2.2   | 2025-01-06 | 2025-01-09 | 2025-01-17 | 11 |

**Average Lead Time:**
```
(5 + 8 + 7 + 6 + 11) / 5 = 7.4 days
```

**Analysis:**
- Average: 7.4 days
- Range: 5-11 days
- Longest: Story 2.2 (11 days) - investigate why
- Shortest: Story 1.1 (5 days) - good baseline

### 3.5 Tools

**Manual (Excel/Google Sheets):**
```
Story | Created | Started | Completed | Lead Time | Cycle Time | Wait Time
1.1   | 2025-01-06 | 2025-01-07 | 2025-01-11 | 5 | 4 | 1
1.2   | 2025-01-06 | 2025-01-08 | 2025-01-14 | 8 | 6 | 2
...
```

**Automated Tools:**
- **Jira:** Lead time report (if configured)
- **Linear:** Lead time tracking
- **GitHub Projects:** Lead time metrics
- **Monday.com:** Lead time widget

**Note:** Most tools track **Cycle Time** (start to done) automatically, but **Lead Time** (created to done) may need manual tracking.

### 3.6 Người chịu trách nhiệm

**Primary:** **PM/Scrum Master**
- Track lead time per story
- Calculate averages
- Identify bottlenecks
- Analyze trends
- Report to stakeholders

**Support:** **Developers**
- Update story status accurately với dates
- Complete stories efficiently
- Communicate blockers early

### 3.7 Target & Analysis

**Target:**
- Lead time < 10 days (for 2-week sprints)
- Consistent lead time (low variance)
- Decreasing lead time over time (process improvement)

**Analysis:**
- **Lead time > 10 days:** Investigate bottlenecks (waiting, complexity, blockers)
- **High variance:** Inconsistent process, estimation issues
- **Increasing lead time:** Process degradation, complexity increase

**Breakdown:**
- **Wait Time:** Time from created to started (should be < 2 days)
- **Cycle Time:** Time from started to done (should be < 8 days)
- **Lead Time = Wait Time + Cycle Time**

### 3.8 Template

**Lead Time Tracking Sheet:**

| Story | Epic | Created | Started | Completed | Lead Time | Cycle Time | Wait Time | Notes |
|-------|------|---------|---------|-----------|-----------|------------|-----------|-------|
| 1.1   | Epic 1 | 2025-01-06 | 2025-01-07 | 2025-01-11 | 5 | 4 | 1 | Simple |
| 1.2   | Epic 1 | 2025-01-06 | 2025-01-08 | 2025-01-14 | 8 | 6 | 2 | Complex |
| 1.3   | Epic 1 | 2025-01-06 | 2025-01-10 | 2025-01-13 | 7 | 3 | 4 | Waiting for dependency |
| 2.1   | Epic 2 | 2025-01-06 | 2025-01-08 | 2025-01-12 | 6 | 4 | 2 | Standard |
| 2.2   | Epic 2 | 2025-01-06 | 2025-01-09 | 2025-01-17 | 11 | 8 | 3 | Blocked 2 days |

**Summary:**
- Average Lead Time: 7.4 days
- Average Cycle Time: 5.0 days
- Average Wait Time: 2.4 days
- Target: < 10 days lead time ✅

---

## 4. Số Bug / Số Story Point

### 4.1 Mục đích

Bug rate đo **quality** của delivered work để:
- Track code quality trends
- Identify problematic areas
- Measure testing effectiveness
- Improve development process

### 4.2 Công thức tính toán

**Bug Rate = Total Bugs Found / Total Story Points Completed**

**Bug Rate per Story = Bugs in Story / Story Points**

**Bug Rate by Epic = Total Bugs in Epic / Total Story Points in Epic**

### 4.3 Cách theo dõi

**Per Sprint:**
1. Track bugs:
   - **Bugs found in testing:** During story development
   - **Bugs found in staging:** After deployment
   - **Bugs found in production:** After release
   - **Critical bugs:** P0/P1 severity

2. Calculate bug rate:
   ```
   Sprint 1:
   - Stories completed: 5 stories (18 story points)
   - Bugs found: 6 bugs
   - Bug Rate = 6 / 18 = 0.33 bugs per story point
   ```

**Per Story:**
```
Story 2.1 (5 story points):
- Bugs found: 2 bugs
- Bug Rate = 2 / 5 = 0.4 bugs per story point
```

**Per Epic:**
```
Epic 2 (Order Processing):
- Stories: 10 stories (45 story points)
- Bugs found: 8 bugs
- Bug Rate = 8 / 45 = 0.18 bugs per story point
```

### 4.4 Example Calculation

**Sprint 1:**

| Story | Story Points | Bugs Found | Bug Rate |
|-------|--------------|------------|----------|
| 1.1   | 3 | 1 | 0.33 |
| 1.2   | 5 | 2 | 0.40 |
| 1.3   | 5 | 1 | 0.20 |
| 2.1   | 3 | 1 | 0.33 |
| 2.2   | 2 | 1 | 0.50 |

**Sprint 1 Summary:**
- Total Story Points: 18
- Total Bugs: 6
- **Bug Rate = 6 / 18 = 0.33 bugs per story point**

**Sprint 2:**

| Story | Story Points | Bugs Found | Bug Rate |
|-------|--------------|------------|----------|
| 2.3   | 5 | 0 | 0.00 |
| 2.4   | 5 | 1 | 0.20 |
| 2.5   | 3 | 1 | 0.33 |
| 3.1   | 5 | 2 | 0.40 |
| 3.2   | 3 | 0 | 0.00 |

**Sprint 2 Summary:**
- Total Story Points: 21
- Total Bugs: 4
- **Bug Rate = 4 / 21 = 0.19 bugs per story point** ✅ (improving)

**Trend Analysis:**
- Sprint 1: 0.33 bugs/SP
- Sprint 2: 0.19 bugs/SP
- **Improvement: -42%** ✅

### 4.5 Tools

**Manual (Excel/Google Sheets):**
```
Sprint | Story | SP | Bugs | Bug Rate | Critical Bugs
1      | 1.1   | 3  | 1    | 0.33     | 0
1      | 1.2   | 5  | 2    | 0.40     | 1
...
```

**Automated Tools:**
- **Jira:** Bug tracking + Story points (manual calculation)
- **Linear:** Bug tracking + Story points
- **GitHub Issues:** Bug labels + Story points
- **Sentry:** Production bugs (link to stories)

### 4.6 Người chịu trách nhiệm

**Primary:** **PM/Scrum Master**
- Track bugs per story
- Calculate bug rate
- Analyze trends
- Report to team
- Identify problematic areas

**Support:** **Developers**
- Write tests to prevent bugs
- Fix bugs quickly
- Root cause analysis
- Learn from bugs

**QA Tester (if available):**
- Report bugs accurately
- Categorize bugs (severity, type)
- Track bug resolution

### 4.7 Target & Analysis

**Target:**
- **Bug Rate < 0.25 bugs per story point** (good quality)
- **Critical Bugs = 0** (no P0/P1 bugs)
- **Decreasing bug rate** over time (process improvement)

**Analysis:**
- **Bug Rate > 0.5:** Poor quality, review process
- **Bug Rate 0.25-0.5:** Acceptable, room for improvement
- **Bug Rate < 0.25:** Good quality ✅
- **Critical Bugs > 0:** Serious issue, review immediately

**Breakdown by Bug Type:**
- **Logic bugs:** Review requirements, testing
- **Integration bugs:** Review integration tests
- **UI bugs:** Review frontend testing
- **Performance bugs:** Review performance testing

**Breakdown by Bug Source:**
- **Found in testing:** Good (caught early)
- **Found in staging:** Acceptable (caught before production)
- **Found in production:** Bad (need better testing)

### 4.8 Template

**Bug Rate Tracking Sheet:**

| Sprint | Story | Epic | SP | Bugs | Critical | Bug Rate | Notes |
|--------|-------|------|----|------|----------|----------|-------|
| 1      | 1.1   | Epic 1 | 3 | 1 | 0 | 0.33 | Logic bug |
| 1      | 1.2   | Epic 1 | 5 | 2 | 1 | 0.40 | Integration bug |
| 1      | 1.3   | Epic 1 | 5 | 1 | 0 | 0.20 | UI bug |
| 2      | 2.1   | Epic 2 | 3 | 1 | 0 | 0.33 | Logic bug |
| 2      | 2.2   | Epic 2 | 2 | 1 | 0 | 0.50 | Performance bug |

**Sprint Summary:**

| Sprint | Total SP | Total Bugs | Critical Bugs | Bug Rate | Trend |
|--------|----------|------------|----------------|----------|-------|
| 1      | 18       | 6          | 1              | 0.33     | Baseline |
| 2      | 21       | 4          | 0              | 0.19     | ↓ -42% ✅ |
| 3      | 19       | 3          | 0              | 0.16     | ↓ -16% ✅ |
| 4      | 20       | 2          | 0              | 0.10     | ↓ -38% ✅ |

**Target:** < 0.25 bugs/SP ✅ (achieved from Sprint 2)

---

## 📊 Combined Metrics Dashboard

### Weekly Metrics Report Template

**Sprint {Number} - Week {Number} Metrics**

**Burndown:**
- Remaining SP: {number}
- Ideal SP: {number}
- Variance: {number} ({percentage}%)
- Status: On Track / Behind / Ahead

**Velocity:**
- Current Sprint: {number} SP (projected)
- Average (last 3): {number} SP
- Trend: Improving / Stable / Declining

**Lead Time:**
- Average: {number} days
- Range: {min}-{max} days
- Target: < 10 days ✅ / ❌

**Bug Rate:**
- Current Sprint: {number} bugs / {number} SP = {rate}
- Average (last 3): {rate}
- Target: < 0.25 ✅ / ❌
- Critical Bugs: {number}

**Actions:**
- {Action 1}
- {Action 2}

---

## 🛠️ Tools Setup

### Excel/Google Sheets Template

**Create 4 sheets:**
1. **Burndown:** Daily remaining SP tracking
2. **Velocity:** Sprint velocity history
3. **Lead Time:** Story lead time tracking
4. **Bug Rate:** Bug tracking per story

**Formulas:**
- Burndown: `=Total_SP - (Done_SP + In_Progress_SP * 0.5)`
- Velocity: `=SUM(Completed_SP_Range) / COUNT(Completed_SP_Range)`
- Lead Time: `=Completion_Date - Creation_Date`
- Bug Rate: `=Total_Bugs / Total_SP`

### Automated Tools Setup

**Jira:**
- Enable burndown chart
- Track story points
- Link bugs to stories
- Use velocity report

**Linear:**
- Enable sprint tracking
- Track story points
- Link bugs to stories
- Use analytics

**GitHub Projects:**
- Create sprint board
- Add story points field
- Link issues to stories
- Use insights

---

## 📈 Reporting Schedule

### Daily (PM/Scrum Master)
- Update burndown chart
- Check story status
- Identify blockers

### Weekly (PM/Scrum Master)
- Calculate velocity (if sprint ends)
- Review lead time trends
- Review bug rate
- Prepare metrics report

### Per Sprint (PM/Scrum Master)
- Calculate all metrics
- Analyze trends
- Report to stakeholders
- Plan improvements

---

## 🎯 Success Criteria

**Target Metrics:**
- **Burndown:** On track (variance < 20%)
- **Velocity:** Stable (variance < 20%) hoặc improving
- **Lead Time:** < 10 days average
- **Bug Rate:** < 0.25 bugs per story point

**Continuous Improvement:**
- Track metrics over time
- Identify trends
- Take action based on data
- Improve process

---

**Document Status:** Complete  
**Next Steps:** Setup tracking tools, begin tracking from Sprint 1

