# Sprint Plan — ccplugin-sprint-planner

**Generated:** 2026-04-22  
**Source:** analysis/MASTER_ANALYSIS.md  
**Total Sprints:** 3

---

## Summary

| Sprint | Focus | Tasks | SP | Status |
|--------|-------|-------|----|--------|
| 1 | Critical DX & Bug Fixes | 5 | 8 | Planned |
| 2 | Reliability & Idempotency | 4 | 7 | Planned |
| 3 | Features & Documentation | 5 | 8 | Planned |

**Total:** 14 tasks, 23 SP

---

## Sprint 1 — Critical DX & Bug Fixes (SP: 8)

**Goal:** Remove automation blockers, fix critical UX bugs.

| Task | Title | Priority | Effort | SP | wave |
|------|-------|----------|--------|----|------|
| SP-S1-01 | Auto-detect Jira project key from CLAUDE.md | P0 | S | 1 | W1 |
| SP-S1-02 | Add `--dry-run` flag: generate SPRINT_PLAN.md without Jira write | P0 | M | 2 | W1 |
| SP-S1-03 | Validate SPRINT_PLAN.md exists before running jira-only mode | P0 | S | 1 | W1 |
| SP-S1-04 | Add Jira project key validation before creation loop | P1 | S | 2 | W2 |
| SP-S1-05 | Add progress indicator during Jira creation (sprint N/5, task M) | P2 | M | 2 | W2 |

**Verify:** grep `CLAUDE.md` in sprint-plan.md; grep `dry-run` in sprint-plan.md; grep `jira-only` validation in sprint-plan.md

---

## Sprint 2 — Reliability & Idempotency (SP: 7)

**Goal:** Prevent duplicate Jira issues, handle API failures gracefully.

| Task | Title | Priority | Effort | SP | wave |
|------|-------|----------|--------|----|------|
| SP-S2-01 | Add idempotency check — skip Jira issues that already exist | P1 | M | 2 | W1 |
| SP-S2-02 | Define fuzzy match algorithm for sync mode (Levenshtein ≤ 3) | P1 | S | 1 | W1 |
| SP-S2-03 | Add `--sprints N` flag to configure sprint count | P1 | S | 2 | W2 |
| SP-S2-04 | Add sprint capacity configuration (`--capacity N`) | P2 | S | 2 | W2 |

**Verify:** sprint-plan.md mentions idempotency; sprint-plan.md has fuzzy match definition; `--sprints` flag documented

---

## Sprint 3 — Features & Documentation (SP: 8)

**Goal:** Adaptive sprint themes, dependency graph, better docs.

| Task | Title | Priority | Effort | SP | wave |
|------|-------|----------|--------|----|------|
| SP-S3-01 | Add adaptive sprint focus areas based on project type | P1 | M | 2 | W1 |
| SP-S3-02 | Add task dependency graph (wave/depends_on columns) to SPRINT_PLAN output | P1 | M | 2 | W1 |
| SP-S3-03 | Expand SKILL.md trigger patterns with more natural phrasings | P2 | S | 1 | W1 |
| SP-S3-04 | Add example SPRINT_PLAN.md output to command docs | P2 | M | 2 | W2 |
| SP-S3-05 | Add quick-start section to README | P3 | S | 1 | W1 |

**Verify:** sprint-plan.md has project type detection; SPRINT_PLAN output includes wave column; SKILL.md has expanded patterns

---

## Risk Assessment

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Auto-detect finds wrong Jira key | Medium | High | Fall back to asking if CLAUDE.md key not found |
| Idempotency check adds latency | Low | Low | Check only epic names, not all tasks |
| Adaptive sprint themes may not fit all cases | Medium | Medium | Allow `--themes` override flag |
