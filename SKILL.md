---
name: sdd-team
description: >
  Team-powered SDD workflow using Claude Code Agent Teams. Creates 3-teammate
  teams for parallel validation (GATE 2), implementation (Phase 8), and existing
  specs audit. Triggers: /sdd-team, /sdd-team audit, /sdd-team validate, /sdd-team implement.
degree-of-freedom: high
allowed-tools: Task, SlashCommand, Read, Write, Edit, Glob, Grep, Bash(fd:*), Bash(rg:*), Bash(git:*), Bash(project-intel.mjs:*), Bash(pnpm:*), Bash(pytest:*), Bash(gh:*), AskUserQuestion, TeamCreate, TeamDelete, TaskCreate, TaskUpdate, TaskList, TaskGet, SendMessage
---

# SDD Team Orchestrator

**Announce**: "Using the SDD Team orchestrator for parallel agent-powered development."

If TeamCreate tool is NOT available → "Enable Agent Teams: set `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`, restart Claude Code." → STOP.

## Known Constraints

| # | Constraint | Impact |
|---|-----------|--------|
| 1 | One team per session | Must TeamDelete before creating next team |
| 2 | No teammate session resumption | Re-create team, skip completed tasks via `.sdd/sdd-state.md` |
| 3 | Cost: 3-5x vs subagents | Only use teams at complexity 4+; 1-3 defers to `/sdd` |
| 4 | Task status lag | Nudge idle teammates after 5+ min silence |
| 5 | Terminal split-pane | Works in iTerm2/Terminal.app only (not VS Code/Ghostty) |

## Quick Reference

| Command | Action | Team Pattern |
|---------|--------|-------------|
| /sdd-team | Full SDD workflow (team-enhanced) | GATE 2 + Phase 8 teams |
| /sdd-team audit | Audit existing specs vs code | MapReduce audit team |
| /sdd-team validate | Run GATE 2 validation only | 3-validator team |
| /sdd-team implement | Run Phase 8 only | 3-lead pipeline team |
| /sdd-team status | Check SDD + team state | Status report |

**$FEATURE**: `NNN-feature-name` (e.g., `001-auth-oauth`)

## Intent Router

| Input | Intent | Route |
|-------|--------|-------|
| (empty) or feature description | `full_workflow` | Phases 0-9 with team-enhanced GATE 2 + Phase 8 |
| "audit" or "audit existing" | `audit` | @workflows/audit-team.md |
| "validate" or "run validators" | `validate` | @workflows/validation-team.md |
| "implement" or "start coding" | `implement` | @workflows/implementation-team.md |
| "status" or "where am I" | `status` | See @templates/status-report.md |
| "continue" or "next" | `continue` | Resume from checkpoint |

## Dynamic Team Sizing

Score complexity per @.claude/skills/sdd/references/complexity-detection.md:

| Factor | Pts | | Complexity | Action |
|--------|-----|---|-----------|--------|
| Multiple integrations | +2 | | **1-3** | Defer to `/sdd` solo |
| New technology | +2 | | **4-7** | Standard: 3 teammates |
| DB schema changes | +1 | | **8-10** | Enhanced: 3 teammates + extra subagents |
| Auth/security | +2 | | | |
| Multi-user interactions | +1 | | | |
| External API deps | +2 | | | |
| Frontend + Backend | +1 | | | |

## Phase Execution

### Team-Enhanced Phases

| Phase | Enhancement | Workflow |
|-------|------------|---------|
| GATE 2 (before Phase 5) | 3-teammate validation team | @workflows/validation-team.md |
| Phase 8 (/implement) | 3-teammate TDD pipeline | @workflows/implementation-team.md |
| /audit (standalone) | 3-teammate MapReduce audit | @workflows/audit-team.md |

Solo phases (0-6, 9) are imported verbatim from @references/existing-sdd-import.md.

### Full Workflow Sequence

```
START → Complexity Check
  [1-3] → Defer to /sdd (STOP)
  [4-5] → Ask user if teams needed
  [6+]  → Phase 0 (discovery) + teams

Phase 0: Discovery (solo) → GATE 0
Phase 1-2: Product/Constitution (solo, optional)
Phase 3: Specification (solo) → GATE 1
Phase 4: Clarification (solo, if needed)
★ GATE 2: TEAM Validation ★ → Pass: 0 CRITICAL/HIGH → TeamDelete
Phase 5-6: Planning/Tasks (solo)
Phase 7: Audit (solo) → GATE 3
★ Phase 8: TEAM Implementation ★ → TDD per story → TeamDelete
Phase 9: Finalization (solo)
```

## Validation Gates

| Gate | Criteria | On-Fail |
|------|----------|---------|
| GATE 0 | `.sdd/discovery/` artifacts exist | Block Phase 3 |
| GATE 1 | spec.md exists, 0 `[NEEDS CLARIFICATION]`, >= 2 ACs per story | Route to Phase 3/4 |
| GATE 2 | 0 CRITICAL, 0 HIGH across all validators | Route to Phase 3/4 |
| GATE 3 | audit-report.md PASS, 0 CRITICAL, 0 HIGH | Block Phase 8 |

## State Management

See @references/state-management.md for state layers, flow, and recovery.

## Team Spawn Patterns

See @references/tool-patterns.md for TeamCreate, TaskCreate, spawn, assign, messaging, and cleanup patterns.

## Subagent Strategy

| Operation | Who Spawns | Subagent Types |
|-----------|-----------|---------------|
| Research (docs, APIs) | Teammates | Firecrawl, Ref MCP, Explore |
| Validation (domain checks) | Teammates | sdd-*-validator agents |
| Implementation (code) | Teammates | Explore, Research, Review |
| Coordination/Gates | Lead only | Status, Gate enforcement |

## Status Report

See @templates/status-report.md for the status report template.

## Error Handling

| Error | Detection | Recovery |
|-------|-----------|----------|
| Teams not enabled | TeamCreate unavailable | "Enable CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS" |
| Complexity too low | Score 1-3 | Defer to `/sdd` |
| No spec.md | /validate without spec | "Run /feature first" |
| GATE 2 FAIL | CRITICAL/HIGH findings | "Fix spec issues: [list]" |
| Audit FAIL | CRITICAL in report | "Fix issues: [list]" |
| [NEEDS CLARIFICATION] | Markers in spec | "Run /clarify first" |
| Teammate crash | No response after timeout | Re-spawn, reassign (max 2 retries) |
| Teammate repeat fail | 2+ crashes | Fallback to subagent-only |
| Team stuck | All idle, tasks pending | Nudge → reassign → unblock |
| State corruption | Invalid sdd-state.md | Rebuild from artifacts |
| TDD violation | Code before test | "Delete code, start with TDD" |

## Quality Gates

See @.claude/skills/sdd/references/quality-gates.md for constitutional articles (I, III, IV, VI, VIII-XI).

## Memory Auto-Sync

- New patterns → `memory/patterns.md`
- Architecture decisions → `memory/decisions.md`
- Lessons learned → `memory/learnings.md`

## Integration Points

- `/sdd-team` extends `/sdd` — all solo phases imported verbatim; complexity 1-3 defers to `/sdd`
- Six validators (in `.claude/agents/`): sdd-frontend/data-layer/auth/api/architecture/testing-validator — invoked by teammates
- Team workflows: @workflows/validation-team.md, @workflows/implementation-team.md, @workflows/audit-team.md

## Version

**Version:** 1.1.0 | **Updated:** 2026-02-07
- v1.1.0: Token optimization — compressed SKILL.md, extracted patterns to references
- v1.0.0: Initial SDD Team skill
