# Sprint Planner — Skill Definition

## Trigger

Auto-trigger when the user mentions any of the following:
- sprint planning
- backlog grooming / backlog refinement
- epic creation
- story points / estimation
- sprint capacity
- PRD to sprint / PRD to tasks
- task breakdown / task extraction
- sprint organization
- velocity planning

## Description

Provides sprint planning capabilities: reads analysis reports or PRDs, extracts tasks with priorities and story points, organizes them into time-boxed sprints, and optionally pushes epics and stories to Jira.

## Capabilities

### Plan Generation
- Parse `analysis/` directory for reports (MASTER_ANALYSIS.md preferred)
- Extract findings with priority (P0-P3) and effort (S/M/L/XL)
- Map effort to story points: S=1, M=2, L=3, XL=5
- Organize tasks into 2-week sprints (25-35 SP each)
- Generate `analysis/SPRINT_PLAN.md`

### Priority Mapping
| Source Category              | Priority |
|------------------------------|----------|
| Must Have / Critical Gaps    | P0/P1    |
| Improvement Suggestions      | P1/P2    |
| Nice-to-Have                 | P2/P3    |

### Sprint Structure
| Sprint | Focus                      |
|--------|----------------------------|
| 1      | Security & Critical Fixes  |
| 2      | Performance & Architecture |
| 3      | UX & Accessibility         |
| 4      | Analytics & Growth         |
| 5      | Monetization & ASO         |

### Jira Integration
- Create epics per sprint: `[Sprint N] Focus Area`
- Create stories/tasks under epics with:
  - English summary (max 80 chars)
  - Description with acceptance criteria
  - Labels: security, perf, arch, ui, growth, analytics, data, content, monetization, a11y, seo
  - Priority and story points
- Requires Atlassian MCP

### Labels
Available labels for task categorization:
`security` `perf` `arch` `ui` `growth` `analytics` `data` `content` `monetization` `a11y` `seo`

## Commands

```
/sprint-plan                    # Full flow: plan + Jira
/sprint-plan plan-only          # Plan only
/sprint-plan jira-only          # Push existing plan to Jira
/sprint-plan sync               # Sync with Jira
```

## Prerequisites

- Analysis reports in `analysis/` directory
- Atlassian MCP for Jira integration (optional for plan-only mode)
