# Phase 8: Implementation Team Workflow

Orchestrates 3-teammate TDD pipeline for story-by-story implementation.

## Team Architecture

```
Lead (sdd-coordinator, delegate mode, opus)
  ├── quality-lead (opus) → test-writer, E2E runner, verifier subagents
  ├── frontend-lead (opus) → Explore, Research, Review subagents
  └── backend-lead (opus) → Explore, Research, DB check subagents
```

**Team Name:** `sdd-impl-{feature}`

## Prerequisites

- `specs/$FEATURE/spec.md`, `plan.md`, and `tasks.md` exist
- GATE 3 passed (`audit-report.md` shows PASS)
- TeamCreate tool is available

## File Ownership Enforcement

**CRITICAL:** Each teammate ONLY modifies files in their domain. Violations cause merge conflicts.

| Domain | Owner | File Patterns |
|--------|-------|---------------|
| Frontend | frontend-lead | `src/app/**`, `src/components/**`, `src/features/**/components/**`, `src/hooks/**`, `src/styles/**` |
| Backend | backend-lead | `src/lib/**`, `src/services/**`, `src/api/**`, `supabase/migrations/**`, `src/features/**/actions/**`, `src/features/**/services/**` |
| Quality | quality-lead | `__tests__/**`, `*.test.*`, `*.spec.*`, `e2e/**`, `tests/**`, `cypress/**`, `playwright/**` |
| Shared | LEAD ONLY | `src/types/**`, `package.json`, `tsconfig.json`, `.env*`, `next.config.*` |

**Shared file protocol:** Teammate messages lead with type definition needed → lead creates → lead notifies both teammates → teammates import.

See @workflows/file-ownership.md for full enforcement rules and conflict resolution.

## Step 1: Parse Stories from tasks.md

Extract user stories in priority order: `US-1: {title} (P1)` with ACs and tasks. Each story goes through the full TDD pipeline before the next starts.

## Step 2: Create Team

```
TeamCreate({ team_name: "sdd-impl-{feature}", description: "Phase 8 implementation for {feature}" })
```

## Step 3: Create Tasks (per story)

For EACH user story US-{N}, create this task set:

| ID | Owner | Subject | BlockedBy |
|----|-------|---------|-----------|
| 1 | quality-lead | US-{N}: Write failing unit tests | — |
| 2 | quality-lead | US-{N}: Write failing integration tests | — |
| 3 | frontend-lead | US-{N}: Implement UI components | 1 |
| 4 | frontend-lead | US-{N}: Implement client-side state | 1 |
| 5 | backend-lead | US-{N}: Implement API routes and server actions | 1 |
| 6 | backend-lead | US-{N}: Implement database schema and migrations | 1 |
| 7 | quality-lead | US-{N}: Run tests and verify all pass | 3,4,5,6 |
| 8 | quality-lead | US-{N}: E2E verification | 7 |

Generate task descriptions from spec ACs. Include: spec path, acceptance criteria, file ownership boundaries, subagent suggestions.

## Step 4: Spawn Teammates

| Name | Spawn Prompt | Tasks | MCPs |
|------|-------------|-------|------|
| quality-lead | @workflows/spawn-prompts/quality-lead.md | 1,2,7,8 | Supabase, Chrome DevTools |
| frontend-lead | @workflows/spawn-prompts/frontend-lead.md | 3,4 | Shadcn, Ref |
| backend-lead | @workflows/spawn-prompts/backend-lead.md | 5,6 | Supabase, Ref |

Spawn config: `subagent_type="general-purpose"`, `model="opus"`, `mode="bypassPermissions"`.
See @references/tool-patterns.md for spawn call syntax.

## Step 5: Assign Tasks

Assign tasks by owner per the table above using `TaskUpdate({ taskId, owner })`.

## Step 6: TDD Pipeline Execution

Per story, the pipeline runs in phases:

1. **RED** (quality-lead): Write failing tests for US-{N} → Tasks 1,2 complete → Unblocks 3,4,5,6
2. **GREEN** (frontend-lead + backend-lead PARALLEL): Implement UI and API/DB → Cross-communicate on interfaces → Both complete → Unblocks 7
3. **VERIFY** (quality-lead): Run all tests → PASS: proceed to E2E → FAIL: message responsible teammate
4. **E2E** (quality-lead): Browser verification → PASS: story complete → FAIL: identify and route fix

## Step 7: Lead Coordination

- **Shared types:** Receive type requests from teammates → create in `src/types/` → notify both leads
- **Test failures:** Monitor quality-lead reports → intervene if teammate stuck (no progress after 2 checks)
- **API contracts:** Relay mismatches between frontend and backend expectations

## Step 8: Story Completion

After US-{N} complete (all 8 tasks):
1. Update `.sdd/sdd-state.md` implementation progress table
2. Git commit: `feat({feature}): implement US-{N} - {story title}`
3. Create tasks for next story (repeat Step 3 with US-{N+1})
4. Reassign teammates

## Step 9: All Stories Complete

1. Quality-lead runs full test suite (final integration)
2. Mark Phase 8 complete in `.sdd/sdd-state.md`
3. Log to `audit-trail.md`
4. Proceed to Phase 9 (solo)

## Step 10: Cleanup

Send `shutdown_request` to all 3 teammates. After confirmation, run `TeamDelete()`.

**Recovery:** If teammate crashes, re-spawn with same name + "RECOVERY MODE" in prompt. Reassign remaining tasks. See SKILL.md Known Constraints.

## Performance Expectations

| Metric | Target |
|--------|--------|
| Per-story pipeline | 15-30 minutes |
| Test writing (red) | 5-10 minutes |
| Parallel implementation (green) | 8-15 minutes |
| Verification + E2E | 5-10 minutes |
| Total for 3-story feature | 45-90 minutes |
| Subagent spawns per teammate | 3-5 per story |
| Cross-domain messages | 3-8 per story |
