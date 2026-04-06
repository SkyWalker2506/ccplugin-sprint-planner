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

## Features

- **Plan Generation** -- Parse analysis reports or PRDs, extract tasks with priorities and story points, organize into time-boxed sprints
- **Priority Mapping** -- Automatically categorize findings as P0-P3 based on severity (Critical/Must Have/Nice-to-Have)
- **Sprint Organization** -- 5-sprint structure with themed focus areas (Security, Performance, UX, Growth, Monetization)
- **Jira Integration** -- Create epics and stories with labels, story points, and acceptance criteria via Atlassian MCP
- **Story Point Estimation** -- S=1, M=2, L=3, XL=5 scale with 25-35 SP capacity per sprint

## Usage

```
/sprint-plan                    # Full flow: generate plan + create Jira issues
/sprint-plan plan-only          # Generate plan only (no Jira)
/sprint-plan jira-only          # Push existing plan to Jira
```

## Prerequisites

- `analysis/` directory with at least one analysis report (`.md`)
- `analysis/MASTER_ANALYSIS.md` is used as the primary source when present
- Atlassian MCP active for Jira integration (optional for plan-only mode)

## Sprint Structure

| Sprint | Focus                      | SP Capacity |
|--------|----------------------------|-------------|
| 1      | Security & Critical Fixes  | 25-35       |
| 2      | Performance & Architecture | 25-35       |
| 3      | UX & Accessibility         | 25-35       |
| 4      | Analytics & Growth         | 25-35       |
| 5      | Monetization & ASO         | 25-35       |

## License

MIT

## Part of

- [claude-config](https://github.com/SkyWalker2506/claude-config) — Multi-Agent OS for Claude Code (134 agents, local-first routing)
- [Plugin Marketplace](https://github.com/SkyWalker2506/claude-marketplace) — Browse & install all 18 plugins
- [ClaudeHQ](https://github.com/SkyWalker2506/ClaudeHQ) — Claude ecosystem HQ
