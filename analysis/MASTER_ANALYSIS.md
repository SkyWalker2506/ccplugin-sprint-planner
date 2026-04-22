# Master Analysis — ccplugin-sprint-planner

**Date:** 2026-04-22  
**Forge Run:** 1  
**Analyst:** Sonnet 4.6 (Jarvis)

---

## Executive Summary

`ccplugin-sprint-planner` is a Claude Code plugin providing sprint planning via `/sprint-plan`. It converts analysis reports and PRDs into structured sprints with Jira integration. The plugin is functionally complete but has UX, reliability, and completeness gaps.

**Overall Score: 6.5/10**

---

## 1. Architecture & Code Quality

**Score: 7/10**

### Findings
- Single command (`/sprint-plan`) with 4 modes (default, plan-only, jira-only, sync) — good separation
- Skill definition is well-structured with clear trigger patterns
- No error handling defined for Jira MCP failures (most common failure mode)
- `plan-only` mode still requires `analysis/` directory — should document fallback
- `/sprint-plan sync` is sophisticated but poorly documented — users may not know it exists
- No definition of "fuzzy match" tolerance in sync mode (case-insensitive + how many chars?)

---

## 2. DX & Usability

**Score: 6/10**

### Findings
- No `--dry-run` flag — users can't preview sprint structure without Jira write access
- Interactive Jira project key question blocker in automated contexts — should read from CLAUDE.md
- No progress indicator during Jira creation (5 sprints × many tasks = slow)
- `jira-only` mode requires existing `SPRINT_PLAN.md` but this isn't validated before starting
- Sprint capacity (25-35 SP) is hardcoded — not configurable per team

---

## 3. Completeness & Feature Gaps

**Score: 6/10**

### Findings
- No support for custom sprint count (hardcoded 5 sprints)
- Sprint focus areas are fixed (Security → Performance → UX → Growth → Monetization) — doesn't adapt to project type (e.g., a CLI tool doesn't need ASO/monetization sprints)
- No velocity tracking — sprint plan doesn't account for previous sprint completion rate
- Missing: rollover handling — tasks not completed in Sprint N should roll to Sprint N+1
- No `--sprint N` flag to generate only a specific sprint
- `sync` mode only works with active sprints — no support for future sprint preview
- Missing: task dependency graph in SPRINT_PLAN.md output (which tasks block which)

---

## 4. Integration Reliability

**Score: 6/10**

### Findings
- Jira creation can partially fail (some epics created, some not) with no cleanup/resume logic
- No idempotency — running twice creates duplicate Jira epics
- No Jira dry-run API call to validate project key before creation loop
- `sync` mode fuzzy match not specified — implementation will vary by agent
- Parallel sprint creation (5 parallel agents) may hit Jira API rate limits

---

## 5. Documentation

**Score: 7/10**

### Findings
- Command file is detailed and well-organized
- Missing: example `SPRINT_PLAN.md` output showing expected format
- Missing: SKILL.md trigger patterns don't include common phrasings ("plan my sprints", "break down tasks")
- README lacks a quick-start section showing a real example

---

## Top 20 Action Items

| # | Title | Category | Priority | Effort | Impact |
|---|-------|----------|----------|--------|--------|
| 1 | Auto-detect Jira project key from CLAUDE.md (avoid blocking question) | DX | P0 | S | High |
| 2 | Add `--dry-run` flag: generate SPRINT_PLAN.md without Jira write | DX | P0 | M | High |
| 3 | Validate `SPRINT_PLAN.md` exists before running `jira-only` mode | Bug | P0 | S | High |
| 4 | Add Jira project key validation (dry-run API call) before creation loop | Reliability | P1 | S | High |
| 5 | Add idempotency check: skip creating epics/tasks that already exist in Jira | Reliability | P1 | M | High |
| 6 | Adapt sprint focus areas based on project type (CLI vs app vs API) | Feature | P1 | M | High |
| 7 | Add task dependency graph to SPRINT_PLAN.md (wave/depends_on columns) | Feature | P1 | M | High |
| 8 | Add `--sprints N` flag to configure sprint count (default 5) | Feature | P1 | S | Medium |
| 9 | Define fuzzy match algorithm in sync mode (Levenshtein ≤ 3 chars) | Quality | P1 | S | Medium |
| 10 | Add progress indicator during Jira creation (sprint N/5, task N/M) | UX | P2 | S | Medium |
| 11 | Add sprint capacity configuration (`--capacity 30`) | Feature | P2 | S | Medium |
| 12 | Add rollover task handling to sync mode | Feature | P2 | M | Medium |
| 13 | Add `--sprint N` flag to generate/push only a specific sprint | Feature | P2 | S | Medium |
| 14 | Add example SPRINT_PLAN.md to README or command docs | Docs | P2 | M | Medium |
| 15 | Expand SKILL.md trigger patterns with more natural phrasings | Quality | P2 | S | Low |
| 16 | Add Jira API rate limit handling with backoff | Reliability | P2 | M | Low |
| 17 | Add `--format` flag (markdown, json, csv) for SPRINT_PLAN output | Feature | P3 | M | Low |
| 18 | Add quick-start section to README | Docs | P3 | S | Low |
| 19 | Add velocity tracking field to SPRINT_PLAN.md | Feature | P3 | M | Low |
| 20 | Add `--rollover` flag for incomplete task handling | Feature | P3 | M | Low |

---

## Cross-Cutting Insights

1. **Automation blocker** — Jira project key question blocks CI/forge contexts; CLAUDE.md auto-detection is critical
2. **No idempotency** — double-run creates duplicate Jira issues; serious UX bug
3. **Sprint structure too rigid** — 5 fixed sprints with fixed themes doesn't fit all project types
4. **Dependency tracking missing** — SPRINT_PLAN.md has no depends_on/wave columns; can't determine execution order

---

## Recommended Sprint Structure

- **Sprint 1:** P0 blockers (auto-detect Jira key, dry-run, jira-only validation)
- **Sprint 2:** Reliability (idempotency, key validation, rate limiting)
- **Sprint 3:** Features + Docs (adaptive sprints, dependency graph, example output)
