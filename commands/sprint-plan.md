# /sprint-plan — Generate Sprint Plan from Analysis/PRD + Jira Entry

## Usage

```
/sprint-plan                    # Full flow: generate plan + create Jira issues
/sprint-plan plan-only          # Generate plan only (no Jira)
/sprint-plan jira-only          # Push existing plan to Jira
/sprint-plan sync               # Sync Jira issues with current plan
```

## Prerequisites

- `analysis/` directory with at least 1 analysis report (markdown)
- `analysis/MASTER_ANALYSIS.md` is used as the primary source when present
- Atlassian MCP must be active for Jira integration

## Workflow

### 1. Detect Project Info

Read CLAUDE.md or existing Jira tasks to find the project key (e.g., AC, VOC).
If not found, ask the user: "What is the Jira project key? (e.g., AC, VOC)"

### 2. Read Analysis Reports

```
Read analysis/*.md files (including MASTER_ANALYSIS.md)
Extract from each report:
  - "Must Have" / "Critical Gaps"      → P0/P1
  - "Improvement Suggestions"          → P1/P2
  - "Nice-to-Have"                     → P2/P3
  - Impact (High/Med/Low) and Effort (S/M/L/XL)
```

### 3. Task Extraction

For each finding/recommendation, create:
- **Short title** (Jira summary — max 80 characters, English)
- **Description** (what/why/how + acceptance criteria as checkbox list)
- **Label** (security, perf, arch, ui, growth, analytics, data, content, monetization, a11y, seo)
- **Priority** (P0/P1/P2/P3)
- **Effort** S=1, M=2, L=3, XL=5 story points

### 4. Sprint Organization

| Sprint | Focus                        | SP Capacity |
|--------|------------------------------|-------------|
| 1      | Security & Critical Fixes    | 25-35       |
| 2      | Performance & Architecture   | 25-35       |
| 3      | UX & Accessibility           | 25-35       |
| 4      | Analytics & Growth           | 25-35       |
| 5      | Monetization & ASO           | 25-35       |

**Rules:**
- Sprint 1 always starts with security + urgent P0 fixes
- P0 tasks must be included in their assigned sprint
- Each sprint is 2 weeks
- Total capacity per sprint: 25-35 story points

### 5. Generate Document

Output: `analysis/SPRINT_PLAN.md`

The plan document contains:
- Summary table of all sprints with task counts and total SP
- Per-sprint breakdown with epics and individual tasks
- Risk assessment and dependencies
- Velocity assumptions

### 6. Jira Entry

1. Create an **Epic** for each sprint: `[Sprint N] Focus Area`
2. Create issues for each task under the appropriate epic:
   - Summary: English (max 80 characters)
   - Description: Detailed (what/why/how + acceptance criteria)
   - Priority: P0 → Highest, P1 → High, P2 → Medium, P3 → Low
   - Labels + Story Points
3. Link related tasks with "blocks" / "is blocked by" relationships

**Parallel execution:** 5 sprints can be created via 5 parallel agent calls.

## Output

- `analysis/SPRINT_PLAN.md` — full sprint plan document
- Jira epics and tasks (when Jira integration is active)
- Console summary with task counts per sprint

## Notes

- Task titles: **English**
- Task descriptions: project language or as configured
- Story point scale: S=1, M=2, L=3, XL=5
- Sprint capacity: 25-35 SP (adjustable based on team size)
