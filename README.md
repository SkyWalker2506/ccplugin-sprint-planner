# sprint-planner — Claude Code Plugin

by [Musab Kara](https://linkedin.com/in/musab-kara-85580612a) · [GitHub](https://github.com/SkyWalker2506)

Claude Code plugin for sprint planning and PRD-to-sprint workflow. Generates epics, stories, and Jira tasks from analysis reports.

## Install

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/SkyWalker2506/claude-marketplace/main/install.sh) sprint-planner
```

Or via Claude Code native marketplace:

```bash
claude plugin install sprint-planner@musabkara-claude-marketplace
```

## Quick Start

```bash
# 1. Run project analysis (generates analysis/MASTER_ANALYSIS.md)
/project-analysis --all

# 2. Preview the sprint plan without writing to Jira
/sprint-plan --dry-run

# 3. Generate plan and create Jira epics + tasks
/sprint-plan

# 4. Check if Jira is in sync with your local plan
/sprint-plan sync
```

## Features

- **Plan Generation** — Parse analysis reports or PRDs, extract tasks with priorities and story points, organize into time-boxed sprints
- **Priority Mapping** — Automatically categorize findings as P0-P3 based on severity (Critical/Must Have/Nice-to-Have)
- **Adaptive Sprint Themes** — Sprint focus areas adapt to project type (mobile app, web, CLI, API, plugin)
- **Jira Integration** — Create epics and stories with labels, story points, and acceptance criteria via Atlassian MCP
- **Idempotent** — Re-running skips Jira epics/tasks that already exist; no duplicates
- **Dry-run mode** — Preview SPRINT_PLAN.md without any Jira writes
- **Dependency graph** — SPRINT_PLAN.md includes wave and depends_on columns for execution ordering
- **Story Point Estimation** — S=1, M=2, L=3, XL=5 scale with configurable capacity per sprint

## Usage

```
/sprint-plan                    # Full flow: generate plan + create Jira issues
/sprint-plan plan-only          # Generate plan only (no Jira)
/sprint-plan --dry-run          # Preview SPRINT_PLAN.md, no Jira writes
/sprint-plan jira-only          # Push existing plan to Jira
/sprint-plan sync               # Sync active Jira sprint with local plan
/sprint-plan --sprints N        # Configure sprint count (default: 5)
/sprint-plan --capacity N       # Configure SP capacity per sprint (default: 30)
```

## Prerequisites

- `analysis/` directory with at least one analysis report (`.md`)
- `analysis/MASTER_ANALYSIS.md` is used as the primary source when present
- Atlassian MCP active for Jira integration (optional for `plan-only` and `--dry-run`)
- Jira project key in CLAUDE.md (auto-detected; fallback: asks user)

## Sprint Structure (Adaptive)

Sprint themes are chosen based on project type detected from CLAUDE.md/README:

| Project Type | Sprint Themes |
|--------------|---------------|
| Mobile app | UX → Performance → Security → Growth → ASO |
| Web app | Security → Performance → UX → SEO → Growth |
| CLI tool | Architecture → Testing → DX → Distribution → Docs |
| API/backend | Security → Performance → Architecture → Testing → Docs |
| Plugin | DX → Reliability → Docs → Examples → Distribution |
| Unknown | Security → Performance → UX → Growth → Monetization |

## License

MIT

## Part of

- [claude-config](https://github.com/SkyWalker2506/claude-config) — Multi-Agent OS for Claude Code (134 agents, local-first routing)
- [Plugin Marketplace](https://github.com/SkyWalker2506/claude-marketplace) — Browse & install all 18 plugins
- [ClaudeHQ](https://github.com/SkyWalker2506/ClaudeHQ) — Claude ecosystem HQ
