# Audit Team Workflow (MapReduce)

Orchestrates 3-teammate MapReduce audit of existing specs vs implementation.

## When to Use

Audits an EXISTING project with specs AND code to find divergence. Answers: which specs are outdated, which code has no spec, which specs lack implementation, where spec and code diverged.

**Not the same as Phase 7 (/audit)** which audits a single feature's plan. This is project-wide.

## Team Architecture

```
Lead (sdd-coordinator, delegate mode, opus)
  ├── spec-analyst (opus) ─── Phase A (MAP) → spec-map.md
  ├── implementation-analyst (opus) ─── Phase A (MAP) → impl-map.md
  └── gap-resolver (opus) ─── Phase B (REDUCE) + Phase C (REMEDIATE) → gap-analysis.md + audit-synthesis.md
```

**Team Name:** `sdd-audit-{project}`

## Prerequisites

- Project has `specs/` directory with at least 1 spec
- Project has implementation code (`src/` or equivalent)
- TeamCreate tool is available

## MapReduce Execution Model

1. **Phase A: MAP** (parallel, zero dependency) — spec-analyst and implementation-analyst run simultaneously
2. **Phase B: REDUCE** (blocked until both maps complete) — gap-resolver cross-references both maps, categorizes as ALIGNED / SPEC_AHEAD / CODE_AHEAD / DIVERGED
3. **Phase C: REMEDIATE** (after gap analysis) — gap-resolver spawns subagents to update/create/archive specs

## Step 1: Create Team

```
TeamCreate({ team_name: "sdd-audit-{project}", description: "MapReduce audit for {project}" })
```

## Step 2: Create Tasks

### Phase A: MAP (parallel)

| ID | Owner | Subject | BlockedBy |
|----|-------|---------|-----------|
| 1 | spec-analyst | Inventory all spec files | — |
| 2 | spec-analyst | Analyze spec completeness | — |
| 3 | spec-analyst | Extract requirement-to-AC mapping | — |
| 4 | spec-analyst | Map inter-spec dependencies | — |
| 5 | spec-analyst | Produce spec map report | 1-4 |
| 6 | implementation-analyst | Map codebase by feature | — |
| 7 | implementation-analyst | Identify code without spec | — |
| 8 | implementation-analyst | Verify implementation matches spec | — |
| 9 | implementation-analyst | Check test existence and coverage | — |
| 10 | implementation-analyst | Check database state matches spec | — |
| 11 | implementation-analyst | Produce implementation map report | 6-10 |

### Phase B+C: REDUCE + REMEDIATE (blocked)

| ID | Owner | Subject | BlockedBy |
|----|-------|---------|-----------|
| 12 | gap-resolver | Cross-reference spec map with implementation map | 5, 11 |
| 13 | gap-resolver | Investigate root causes of divergence | 12 |
| 14 | gap-resolver | Propose remediation plan | 13 |
| 15 | gap-resolver | Execute automated remediations | 14 |
| 16 | gap-resolver | Produce final audit report | 15 |

Generate task descriptions from project context. Include: scan paths, output locations (`.sdd/audit-reports/`), gap categories, remediation safety rules.

## Step 3: Spawn Teammates

| Name | Spawn Prompt | Tasks | MCPs |
|------|-------------|-------|------|
| spec-analyst | @workflows/spawn-prompts/spec-analyst.md | 1-5 | Firecrawl, Ref |
| implementation-analyst | @workflows/spawn-prompts/implementation-analyst.md | 6-11 | Supabase, Chrome DevTools |
| gap-resolver | @workflows/spawn-prompts/gap-resolver.md | 12-16 | Supabase, Chrome DevTools |

Spawn config: `subagent_type="general-purpose"`, `model="opus"`, `mode="bypassPermissions"`.
See @references/tool-patterns.md for spawn call syntax.

## Step 4: Assign Tasks

Assign tasks by owner per the tables above using `TaskUpdate({ taskId, owner })`.

## Step 5: Monitor Phase A (MAP)

- Check progress via `TaskList()`
- Phase A complete when tasks 5 and 11 are both completed
- gap-resolver tasks auto-unblock
- **Expected duration:** 10-20 minutes

## Step 6: Monitor Phase B+C (REDUCE + REMEDIATE)

- gap-resolver auto-starts on tasks 12-16
- Monitor for user decision requests (see User Decision Relay Protocol below)
- **Expected duration:** 10-20 minutes

### User Decision Relay Protocol

Gap-resolver CANNOT use AskUserQuestion directly. When it encounters ambiguous divergence:

1. Gap-resolver messages lead: "Need user decision: Feature X spec says Y, code does Z"
2. Lead uses `AskUserQuestion` with options: Intentional / Bug / Evolution
3. Lead relays user answer back to gap-resolver via `SendMessage`

## Step 7: Final Report

After task 16 completes:
1. Read `.sdd/audit-reports/audit-synthesis.md`
2. Update `.sdd/sdd-state.md` with audit results
3. Log to `.sdd/audit-trail.md`
4. Present summary to user: features audited, alignment %, remediations performed, items needing attention

## Step 8: Cleanup

Send `shutdown_request` to all 3 teammates. After confirmation, run `TeamDelete()`.

**Recovery:** If teammate crashes, re-spawn with same name + "RECOVERY MODE: Check .sdd/audit-reports/ for partial outputs." See SKILL.md Known Constraints.

## Iterative Audit Design

This audit is designed to run **repeatedly**. Each run:
- Reads previous audit state from `.sdd/audit-reports/`
- Focuses on changes since last audit
- Updates gap matrix incrementally
- Reports: "N new gaps found, M previous gaps resolved"

## Performance Expectations

| Metric | Target |
|--------|--------|
| Phase A (parallel mapping) | 10-20 minutes |
| Phase B (cross-reference) | 5-10 minutes |
| Phase C (remediation) | 5-15 minutes |
| Total audit time | 20-45 minutes |
| Subagent spawns (spec-analyst) | 1 per spec + 1 dependency mapper |
| Subagent spawns (impl-analyst) | 1 per feature area + 2 (DB, UI) |
| Subagent spawns (gap-resolver) | 2-5 (karen, validators, spec writers) |
| User interaction points | 0-5 (for ambiguous divergences) |
