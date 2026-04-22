# Sprint Planner — Skill Definition

## Trigger

Auto-trigger when the user mentions any of the following:

**Direct phrasings:**
- sprint planning, sprint plan, plan my sprints, create sprint plan
- backlog grooming, backlog refinement, groom backlog
- epic creation, create epics, make jira issues
- story points, estimation, point these tasks, estimate effort
- sprint capacity, how many tasks per sprint
- PRD to sprint, PRD to tasks, convert PRD to jira
- task breakdown, task extraction, break down tasks, split into tasks
- sprint organization, organize into sprints
- velocity planning, plan velocity

**Contextual phrasings (trigger when intent is clearly sprint planning):**
- "turn this analysis into tasks"
- "create jira issues from the analysis"
- "how should I prioritize these"
- "what should we do first" (when analysis files exist)
- "plan the next sprint"
- "push tasks to jira"
- "sync my sprint"

## Description

Provides sprint planning capabilities: reads analysis reports or PRDs, extracts tasks with priorities and story points, organizes them into time-boxed sprints, and optionally pushes epics and stories to Jira.

## Capabilities

### Plan Generation
- Parse `analysis/` directory for reports (MASTER_ANALYSIS.md preferred)
- Extract findings with priority (P0-P3) and effort (S/M/L/XL)
- Map effort to story points: S=1, M=2, L=3, XL=5
- Organize tasks into 2-week sprints (configurable with --sprints)
- Generate `analysis/SPRINT_PLAN.md` with wave/depends_on columns

### Priority Mapping
| Source Category              | Priority |
|------------------------------|----------|
| Must Have / Critical Gaps    | P0/P1    |
| Improvement Suggestions      | P1/P2    |
| Nice-to-Have                 | P2/P3    |

### Adaptive Sprint Structure
Sprint themes adapt to project type (mobile/web/CLI/API/plugin). Default:
| Sprint | Focus                      |
|--------|----------------------------|
| 1      | Security & Critical Fixes  |
| 2      | Performance & Architecture |
| 3      | UX & Accessibility         |
| 4      | Analytics & Growth         |
| 5      | Monetization & ASO         |

### Jira Integration
- Auto-detect project key from CLAUDE.md
- Idempotent: skips existing epics/tasks on re-run
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
/sprint-plan --dry-run          # Preview plan, no Jira writes
/sprint-plan jira-only          # Push existing plan to Jira
/sprint-plan sync               # Sync with Jira
/sprint-plan --sprints N        # Configure sprint count
/sprint-plan --capacity N       # Configure SP capacity per sprint
```

## Prerequisites

- Analysis reports in `analysis/` directory
- Atlassian MCP for Jira integration (optional for plan-only and --dry-run modes)
