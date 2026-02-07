# GATE 2: Validation Team Workflow

Orchestrates 3-teammate parallel validation for pre-planning quality gate.

## Team Architecture

```
Lead (sdd-coordinator, delegate mode, opus)
  ├── backend-validator (opus) → sdd-api/data-layer/architecture-validator subagents
  ├── frontend-validator (opus) → sdd-frontend-validator + UX checker subagents
  └── security-validator (opus) → sdd-auth/testing-validator subagents
```

**Team Name:** `sdd-validation-{feature}`

## Prerequisites

- `specs/$FEATURE/spec.md` exists
- GATE 1 passed (0 `[NEEDS CLARIFICATION]` markers)
- TeamCreate tool is available

## Step 1: Create Team

```
TeamCreate({ team_name: "sdd-validation-{feature}", description: "GATE 2 validation for {feature}" })
```

## Step 2: Create Tasks

| ID | Owner | Subject | BlockedBy |
|----|-------|---------|-----------|
| 1 | backend-validator | Validate API routes and server actions | — |
| 2 | backend-validator | Validate data layer schema | — |
| 3 | backend-validator | Validate architecture patterns | — |
| 4 | backend-validator | Validate error handling strategy | — |
| 5 | backend-validator | Validate query strategy and performance | — |
| 6 | backend-validator | Produce backend validation report | 1-5 |
| 7 | frontend-validator | Validate UI components and design spec | — |
| 8 | frontend-validator | Validate accessibility requirements | — |
| 9 | frontend-validator | Validate responsive design spec | — |
| 10 | frontend-validator | Validate UX test scenarios | — |
| 11 | frontend-validator | Produce frontend validation report | 7-10 |
| 12 | security-validator | Validate authentication flow | — |
| 13 | security-validator | Validate RLS and permissions | — |
| 14 | security-validator | Validate security headers and CSRF | — |
| 15 | security-validator | Validate test coverage strategy | — |
| 16 | security-validator | Produce security validation report | 12-15 |

Generate task descriptions dynamically from spec. Each must include: spec path, focus area, severity format (CRITICAL/HIGH/MEDIUM/LOW), report location (`.sdd/validation-reports/{domain}-report.md`).

## Step 3: Spawn Teammates

| Name | Spawn Prompt | Tasks | MCPs |
|------|-------------|-------|------|
| backend-validator | @workflows/spawn-prompts/backend-validator.md | 1-6 | Supabase, Ref |
| frontend-validator | @workflows/spawn-prompts/frontend-validator.md | 7-11 | Shadcn, Ref |
| security-validator | @workflows/spawn-prompts/security-validator.md | 12-16 | Supabase, Ref |

Spawn config: `subagent_type="general-purpose"`, `model="opus"`, `mode="bypassPermissions"`.
Context to include in spawn: `spec_path`, `feature_name`, `team_name`, `teammate_names`.
See @references/tool-patterns.md for spawn call syntax.

## Step 4: Assign Tasks

Assign tasks by owner per the table above using `TaskUpdate({ taskId, owner })`.

## Step 5: Monitor and Coordinate

- Check progress via `TaskList()`
- Respond to cross-domain findings as teammates message each other
- **Wait for:** Tasks 6, 11, and 16 to complete (all three report tasks)

## Step 6: Synthesize Reports

Read individual reports from `.sdd/validation-reports/`. Produce unified synthesis at `.sdd/validation-reports/validation-synthesis.md` using @templates/validation-synthesis.md format.

Key sections: findings summary table (severity x domain), CRITICAL/HIGH findings detail, cross-domain issues, overall PASS/FAIL recommendation, links to individual reports.

## Step 7: Gate Decision

```
IF total_critical == 0 AND total_high == 0:
  → GATE 2 PASS → Update .sdd/sdd-state.md → Log to audit-trail.md → Proceed to Phase 5
ELSE:
  → GATE 2 FAIL → Report findings → Route back to Phase 3 (/feature) or Phase 4 (/clarify)
```

## Step 8: Cleanup

Send `shutdown_request` to all 3 teammates. After confirmation, run `TeamDelete()`.

**Recovery:** If teammate crashes, re-spawn with same name and reassign remaining tasks. See SKILL.md Known Constraints.

## Performance Expectations

| Metric | Target |
|--------|--------|
| Total validation time | 8-15 minutes |
| Per-teammate tasks | 5-6 |
| Subagent spawns per teammate | 2-4 |
| Cross-domain messages | 2-5 |
| Final synthesis | 2-3 minutes |
