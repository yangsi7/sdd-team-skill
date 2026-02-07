# State Management: SDD + Teams Integration

## State Layers

### Layer 1: SDD Persistent State (survives sessions)

| Artifact | Location | Purpose |
|----------|----------|---------|
| `sdd-state.md` | `.sdd/sdd-state.md` | Current feature, phase, checkpoint, gate results |
| `audit-trail.md` | `.sdd/audit-trail.md` | Decision log with timestamps and rationale |
| Spec files | `specs/{feature}/spec.md` | Feature specifications |
| Plan | `specs/{feature}/plan.md` | Implementation plan |
| Tasks | `specs/{feature}/tasks.md` | User stories and acceptance criteria |
| Reports | `specs/{feature}/validation-report.md` | Gate validation reports |
| Audit reports | `.sdd/audit-reports/` | Existing specs audit outputs |

### Layer 2: Team Ephemeral State (per-session)

| Artifact | Location | Purpose |
|----------|----------|---------|
| Team config | `~/.claude/teams/{team-name}/config.json` | Team members, roles, agent IDs |
| Task list | `~/.claude/tasks/{team-name}/` | Team task assignments and status |

### Layer 3: Subagent State (per-invocation)

- Tool results returned to parent teammate
- No persistence — results consumed immediately
- Parent teammate decides what to persist

## State Flow During Team Phases

### GATE 2 (Validation Team)

| Step | Action | State Change |
|------|--------|-------------|
| Pre-team | — | `sdd-state.md`: phase=GATE_2, status=PENDING |
| Team creation | TeamCreate | `~/.claude/teams/sdd-validation-{feature}/` created |
| Task execution | TaskCreate × N | Validators produce findings, exchange cross-domain messages |
| Synthesis | Lead collects findings | `specs/{feature}/validation-report.md` written |
| State update | Lead writes state | `sdd-state.md`: gate2_status=PASS/FAIL; `audit-trail.md` entry added |
| Cleanup | TeamDelete | Ephemeral state removed; persistent state preserved |

### Phase 8 (Implementation Team)

| Step | Action | State Change |
|------|--------|-------------|
| Pre-team | — | `sdd-state.md`: phase=8, status=PENDING |
| Team creation | TeamCreate + story mapping | `~/.claude/teams/sdd-impl-{feature}/` created |
| TDD pipeline | Per story: tests → impl → verify | `sdd-state.md` checkpoint updated per story |
| Completion | All stories verified | `sdd-state.md`: phase=8, status=COMPLETE |
| Cleanup | TeamDelete | Ephemeral state removed |

### Audit (Existing Specs)

| Step | Action | State Change |
|------|--------|-------------|
| Pre-team | — | No existing sdd-state.md required (standalone) |
| Team creation | TeamCreate | `~/.claude/teams/sdd-audit-{project}/` created |
| Phase A: Mapping | Spec + Impl analysts map independently | No state updates (mapping only) |
| Phase B: Gap analysis | Gap Resolver compares maps | `.sdd/audit-reports/audit-{date}-round-{N}.md` written |
| Phase C: Remediation | Gap Resolver fixes CRITICAL/HIGH | `sdd-state.md` created/updated with audit results |
| Cleanup | TeamDelete | Ephemeral state removed |

## State Recovery

If a session crashes mid-team:

1. **Persistent state is safe** — `.sdd/sdd-state.md` has the last checkpoint
2. **Team state is lost** — ephemeral dirs under `~/.claude/` are gone
3. **Artifacts on disk are preserved** — reports, code, specs already written to project dir
4. **Recovery**: Read sdd-state.md → re-create team → skip completed tasks → resume from incomplete task

## State Update Protocol

To prevent state conflicts from parallel writes:

1. **Only the Team Lead updates `.sdd/sdd-state.md`** — teammates never modify it directly
2. **Only the Team Lead updates `.sdd/audit-trail.md`** — ensures consistent decision log
3. **Teammates signal via messages**: "Task X complete, results in {location}"
4. **Lead batches updates**: collects results, then writes state once

This single-writer pattern avoids race conditions when 3 teammates run in parallel.
