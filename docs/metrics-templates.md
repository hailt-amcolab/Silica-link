# Silica-link - Metrics Tracking Templates

**Quick reference templates cho Excel/Google Sheets**

---

## 1. Burndown Chart Template

### Sheet: "Burndown"

| Day | Date | Total SP | Done SP | In Progress SP | Remaining SP | Ideal SP | Variance |
|-----|------|----------|---------|----------------|-------------|----------|----------|
| 1   | Mon  | 21       | 0       | 0              | 21          | 21.0     | 0.0      |
| 2   | Tue  | 21       | 0       | 10             | 16          | 18.7     | -2.7     |
| 3   | Wed  | 21       | 3       | 10             | 13          | 16.4     | -3.4     |
| 4   | Thu  | 21       | 8       | 8              | 9           | 14.1     | -5.1     |
| 5   | Fri  | 21       | 13      | 5              | 5.5         | 11.8     | -6.3     |
| 6   | Mon  | 21       | 16      | 3              | 2.5         | 9.5      | -7.0     |
| 7   | Tue  | 21       | 18      | 2              | 1           | 7.2      | -6.2     |
| 8   | Wed  | 21       | 19      | 1              | 0.5         | 4.9      | -4.4     |
| 9   | Thu  | 21       | 20      | 0              | 0           | 2.6      | -2.6     |
| 10  | Fri  | 21       | 21      | 0              | 0           | 0.0      | 0.0      |

**Formulas:**
- Remaining SP: `=Total_SP - (Done_SP + In_Progress_SP * 0.5)`
- Ideal SP: `=Total_SP * (1 - (Day - 1) / 10)`
- Variance: `=Remaining_SP - Ideal_SP`

---

## 2. Velocity Tracking Template

### Sheet: "Velocity"

| Sprint | Start Date | End Date | Completed SP | Notes | Trend |
|--------|------------|----------|-------------|-------|-------|
| 1      | 2025-01-06 | 2025-01-17 | 18 | First sprint | Baseline |
| 2      | 2025-01-20 | 2025-01-31 | 21 | Team efficient | ↑ +16% |
| 3      | 2025-02-03 | 2025-02-14 | 19 | Some complexity | ↓ -9% |
| 4      | 2025-02-17 | 2025-02-28 | 20 | Stable | ↑ +5% |
| 5      | 2025-03-03 | 2025-03-14 | 22 | Improving | ↑ +10% |

**Summary:**
- Average (last 3): `=AVERAGE(C4:C6)` = 20.3
- Min: `=MIN(C2:C6)` = 18
- Max: `=MAX(C2:C6)` = 22
- Range: 18-22 SP
- Next Sprint Prediction: 20-21 SP

---

## 3. Lead Time Tracking Template

### Sheet: "Lead Time"

| Story | Epic | Created | Started | Completed | Lead Time | Cycle Time | Wait Time |
|-------|------|---------|---------|-----------|-----------|------------|-----------|
| 1.1   | Epic 1 | 2025-01-06 | 2025-01-07 | 2025-01-11 | 5 | 4 | 1 |
| 1.2   | Epic 1 | 2025-01-06 | 2025-01-08 | 2025-01-14 | 8 | 6 | 2 |
| 1.3   | Epic 1 | 2025-01-06 | 2025-01-10 | 2025-01-13 | 7 | 3 | 4 |
| 2.1   | Epic 2 | 2025-01-06 | 2025-01-08 | 2025-01-12 | 6 | 4 | 2 |
| 2.2   | Epic 2 | 2025-01-06 | 2025-01-09 | 2025-01-17 | 11 | 8 | 3 |

**Formulas:**
- Lead Time: `=Completed - Created`
- Cycle Time: `=Completed - Started`
- Wait Time: `=Started - Created`

**Summary:**
- Average Lead Time: `=AVERAGE(E2:E6)` = 7.4 days
- Average Cycle Time: `=AVERAGE(F2:F6)` = 5.0 days
- Average Wait Time: `=AVERAGE(G2:G6)` = 2.4 days
- Target: < 10 days ✅

---

## 4. Bug Rate Tracking Template

### Sheet: "Bug Rate"

| Sprint | Story | Epic | SP | Bugs | Critical | Bug Rate | Notes |
|--------|-------|------|----|------|----------|----------|-------|
| 1      | 1.1   | Epic 1 | 3 | 1 | 0 | 0.33 | Logic bug |
| 1      | 1.2   | Epic 1 | 5 | 2 | 1 | 0.40 | Integration bug |
| 1      | 1.3   | Epic 1 | 5 | 1 | 0 | 0.20 | UI bug |
| 2      | 2.1   | Epic 2 | 3 | 1 | 0 | 0.33 | Logic bug |
| 2      | 2.2   | Epic 2 | 2 | 1 | 0 | 0.50 | Performance bug |

**Formulas:**
- Bug Rate: `=Bugs / SP`

**Summary by Sprint:**

| Sprint | Total SP | Total Bugs | Critical | Bug Rate | Status |
|--------|----------|------------|----------|----------|--------|
| 1      | 18       | 6          | 1        | 0.33     | ⚠️ Above target |
| 2      | 21       | 4          | 0        | 0.19     | ✅ Good |
| 3      | 19       | 3          | 0        | 0.16     | ✅ Good |
| 4      | 20       | 2          | 0        | 0.10     | ✅ Excellent |

**Target:** < 0.25 bugs/SP

---

## 5. Combined Dashboard Template

### Sheet: "Dashboard"

**Sprint {Number} - Week {Number}**

| Metric | Value | Target | Status |
|--------|-------|--------|--------|
| **Burndown** | | | |
| Remaining SP | 13 | - | On Track |
| Variance | -3.4 | < 20% | ⚠️ Behind |
| **Velocity** | | | |
| Current Sprint | 18 (projected) | - | - |
| Average (last 3) | 19.3 | - | Stable |
| **Lead Time** | | | |
| Average | 7.4 days | < 10 days | ✅ Good |
| Range | 5-11 days | - | - |
| **Bug Rate** | | | |
| Current Sprint | 0.33 | < 0.25 | ⚠️ Above target |
| Average (last 3) | 0.23 | < 0.25 | ✅ Good |
| Critical Bugs | 1 | 0 | ❌ Issue |

**Actions:**
- Review scope for burndown variance
- Investigate critical bug
- Continue monitoring bug rate

---

## Quick Formulas Reference

### Burndown
```
Remaining SP = Total SP - (Done SP + In Progress SP * 0.5)
Ideal SP = Total SP * (1 - (Day - 1) / Total Days)
Variance = Remaining SP - Ideal SP
```

### Velocity
```
Sprint Velocity = SUM(Completed SP)
Average Velocity = AVERAGE(Last 3 Sprint Velocities)
```

### Lead Time
```
Lead Time = Completed Date - Created Date
Cycle Time = Completed Date - Started Date
Wait Time = Started Date - Created Date
```

### Bug Rate
```
Bug Rate = Total Bugs / Total Story Points
Bug Rate per Story = Bugs in Story / Story Points
```

---

**Copy these templates vào Excel/Google Sheets để bắt đầu tracking!**

