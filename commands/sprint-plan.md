# /sprint-plan — Generate Sprint Plan from Analysis/PRD + Jira Entry

## Usage

```
/sprint-plan                    # Full flow: generate plan + create Jira issues
/sprint-plan plan-only          # Generate plan only (no Jira)
/sprint-plan --dry-run          # Generate SPRINT_PLAN.md locally, no Jira writes
/sprint-plan jira-only          # Push existing plan to Jira
/sprint-plan sync               # Sync active Jira sprint tasks with local plan
/sprint-plan --sprints N        # Configure sprint count (default: 5)
/sprint-plan --capacity N       # Configure SP capacity per sprint (default: 30)
```

## Prerequisites

- `analysis/` directory with at least 1 analysis report (markdown)
- `analysis/MASTER_ANALYSIS.md` is used as the primary source when present
- Atlassian MCP must be active for Jira integration (not required for `--dry-run` or `plan-only`)

## Workflow

### 0. Mode Detection

| Argument | Behavior |
|----------|----------|
| _(none)_ | Full flow: generate plan + Jira creation |
| `plan-only` | Generate SPRINT_PLAN.md only, no Jira |
| `--dry-run` | Same as plan-only, clearly labeled [DRY RUN] |
| `jira-only` | Push existing SPRINT_PLAN.md to Jira (validate file exists first) |
| `sync` | Diff active Jira sprint against local plan |

**jira-only validation:** Before starting jira-only mode, check:
```
[ ] analysis/SPRINT_PLAN.md exists
If not found → abort with: "No SPRINT_PLAN.md found. Run /sprint-plan first to generate one."
```

### 1. Detect Project Info

Read CLAUDE.md first — look for a line matching:
- `Jira:`, `jira_key:`, `Jira project:`, or a standalone uppercase key like `KEY:` in context
- Common patterns: `Jira: CHQ`, `jira: "CHQ"`, `## Jira\n\nKey: CHQ`

If found → use that key automatically, log: `Detected Jira key: {KEY} from CLAUDE.md`
If not found → ask the user: "What is the Jira project key? (e.g., AC, VOC)"

**Key validation (before Jira creation):** Make one lightweight Jira API call to verify the project key exists before starting the creation loop. If invalid → abort early with: "Project key '{KEY}' not found in Jira. Check your key and try again."

### 2. Read Analysis Reports

```
Read analysis/*.md files (including MASTER_ANALYSIS.md)
Extract from each report:
  - "Must Have" / "Critical Gaps"      → P0/P1
  - "Improvement Suggestions"          → P1/P2
  - "Nice-to-Have"                     → P2/P3
  - Impact (High/Med/Low) and Effort (S/M/L/XL)
```

### 3. Detect Project Type (for adaptive sprint themes)

Read CLAUDE.md and README to determine project type:
- **Mobile app** (Flutter, React Native, Swift, Kotlin) → themes: UX, Performance, Security, Growth, ASO
- **Web app** (Next.js, React, Django, Rails) → themes: Security, Performance, UX, SEO, Growth
- **CLI tool** (bash, Python CLI, Node CLI) → themes: Architecture, Testing, DX, Distribution, Docs
- **API / backend** (FastAPI, Express, Go) → themes: Security, Performance, Architecture, Testing, Docs
- **Plugin / extension** (Claude plugin, VS Code ext) → themes: DX, Reliability, Docs, Examples, Distribution
- **Unknown** → use default: Security, Performance, UX, Growth, Monetization

Log: `Detected project type: {type} → using {themes} sprint themes`

### 4. Task Extraction

For each finding/recommendation, create:
- **Short title** (Jira summary — max 80 characters, English)
- **Description** (what/why/how + acceptance criteria as checkbox list)
- **Label** (security, perf, arch, ui, growth, analytics, data, content, monetization, a11y, seo)
- **Priority** (P0/P1/P2/P3)
- **Effort** S=1, M=2, L=3, XL=5 story points
- **Wave** (W1 = no dependencies, W2 = depends on W1, etc.)
- **depends_on** (task IDs this task blocks on)

### 5. Sprint Organization

| Sprint | Focus (adaptive — see Step 3) | SP Capacity |
|--------|-------------------------------|-------------|
| 1      | [theme-1]                     | 25-35       |
| 2      | [theme-2]                     | 25-35       |
| 3      | [theme-3]                     | 25-35       |
| 4      | [theme-4]                     | 25-35       |
| 5      | [theme-5]                     | 25-35       |

Sprint count is configurable via `--sprints N`. Default: 5.
SP capacity configurable via `--capacity N`. Default: 30.

**Rules:**
- Sprint 1 always starts with security + urgent P0 fixes
- P0 tasks must be included in their assigned sprint
- Each sprint is 2 weeks
- Total capacity per sprint: 25-35 story points (or `--capacity` value)

### 6. Generate Document

Output: `analysis/SPRINT_PLAN.md`

The plan document contains:
- Summary table of all sprints with task counts and total SP
- Per-sprint breakdown with epics and individual tasks (including `wave` and `depends_on` columns)
- Risk assessment and dependencies
- Velocity assumptions

### 7. Jira Entry (skip in --dry-run / plan-only)

**Idempotency check:** Before creating each epic, search Jira:
```
searchJiraIssuesUsingJql: project = {KEY} AND issuetype = Epic AND summary ~ "[Sprint N]"
```
If found → skip creation, log: `Sprint N epic already exists (KEY-123) — skipping`

1. Create an **Epic** for each sprint: `[Sprint N] Focus Area`
2. Create issues for each task under the appropriate epic:
   - Summary: English (max 80 characters)
   - Description: Detailed (what/why/how + acceptance criteria)
   - Priority: P0 → Highest, P1 → High, P2 → Medium, P3 → Low
   - Labels + Story Points
3. Link related tasks with "blocks" / "is blocked by" relationships

**Progress indicator:** Log each step:
```
[Sprint 1/5] Creating epic... ✓
[Sprint 1/5] Creating task 1/7: Add rate limiting... ✓
[Sprint 1/5] Creating task 2/7: Fix SQL injection... ✓
```

**Parallel execution:** 5 sprints can be created via 5 parallel agent calls (after idempotency check).

## Sync Workflow (`/sprint-plan sync`)

Compares the active Jira sprint with the local `analysis/SPRINT_PLAN.md` and surfaces the diff.

### Steps

#### 1. Validate Prerequisites

Check that `analysis/SPRINT_PLAN.md` exists. If not:
> "No local plan found. Run `/sprint-plan` first to generate one."

#### 2. Fetch Active Jira Sprint

```
searchJiraIssuesUsingJql: project = <KEY> AND sprint in openSprints()
Fields: summary, status, assignee, priority, story_points, labels
```

#### 3. Read Local Plan

Parse `analysis/SPRINT_PLAN.md` — extract all tasks with their expected status, priority, and sprint assignment.

#### 4. Diff & Classify

Compare Jira tasks vs local plan tasks by summary using **fuzzy match**:
- Case-insensitive
- Trim whitespace
- Match if Levenshtein distance ≤ 3 characters OR one string contains the other (substring match)

| Category | Condition |
|----------|-----------|
| **In Sync** | Task exists in both; status matches |
| **Status Drift** | Task exists in both; Jira status differs from plan expectation |
| **Jira Only** | Task exists in Jira sprint but not in local plan |
| **Plan Only** | Task in local plan assigned to active sprint but missing from Jira |

#### 5. Display Diff Report

```
## /sprint-plan sync — <Sprint Name> (<date>)

### Status Drift (N)
| Key      | Summary               | Plan Status | Jira Status |
|----------|-----------------------|-------------|-------------|
| PROJ-42  | Fix login crash       | In Progress | Done        |

### Jira Only (N)
| Key      | Summary               | Status      |
|----------|-----------------------|-------------|
| PROJ-55  | Hotfix payment error  | In Progress |

### Plan Only (N)
| Title                        | Priority | Sprint |
|------------------------------|----------|--------|
| Add onboarding analytics     | P1       | 2      |

### In Sync (N)
All N matching tasks are aligned.
```

#### 6. Optional Actions

After displaying the diff, offer:
- `[P]ush Plan Only tasks to Jira` — create missing issues
- `[U]pdate local plan` — write Jira status back to `SPRINT_PLAN.md`
- `[S]kip` — exit without changes

Only execute the chosen action; do not auto-apply both.

## Output

- `analysis/SPRINT_PLAN.md` — full sprint plan document (includes wave + depends_on columns)
- Jira epics and tasks (when Jira integration is active)
- Console summary with task counts per sprint

## Notes

- Task titles: **English**
- Task descriptions: project language or as configured
- Story point scale: S=1, M=2, L=3, XL=5
- Sprint capacity: 25-35 SP (configurable with `--capacity`)
- Sprint count: 5 (configurable with `--sprints`)
