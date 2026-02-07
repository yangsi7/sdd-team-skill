# sdd-team-skill

Team-powered Specification-Driven Development for Claude Code Agent Teams.

Extends the base `/sdd` skill with 3-teammate teams at three high-value points:
parallel validation (GATE 2), TDD implementation (Phase 8), and existing-specs
audit. Complexity 1-3 defers to solo `/sdd`; 4+ activates teams.

## Quick Start

**Prerequisites:**
- Claude Code CLI installed
- Experimental agent teams flag enabled

**Installation:**
```bash
git clone https://github.com/yangsi7/sdd-team-skill.git ~/.claude/skills/sdd-team
```

**Enable agent teams:**
```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

**Usage:**
```
/sdd-team "Add user authentication with OAuth"
```

## Commands

| Command | Action | Team Pattern |
|---------|--------|-------------|
| `/sdd-team` | Full SDD workflow (team-enhanced) | GATE 2 + Phase 8 teams |
| `/sdd-team audit` | Audit existing specs vs code | MapReduce audit team |
| `/sdd-team validate` | Run GATE 2 validation only | 3-validator team |
| `/sdd-team implement` | Run Phase 8 only | 3-lead pipeline team |
| `/sdd-team status` | Check SDD + team state | Status report |

## How It Works

### Complexity Routing

The skill scores feature complexity to decide whether teams are warranted:

| Factor | Points |
|--------|--------|
| Multiple integrations | +2 |
| New technology | +2 |
| DB schema changes | +1 |
| Auth/security | +2 |
| Multi-user interactions | +1 |
| External API deps | +2 |
| Frontend + Backend | +1 |

| Score | Action |
|-------|--------|
| 1-3 | Defer to solo `/sdd` |
| 4-7 | Standard: 3 teammates |
| 8-10 | Enhanced: 3 teammates + extra subagents |

### Team Pattern A: GATE 2 Validation

Three validators run in parallel (16 tasks total), each spawning domain-specific
subagents. Cross-domain messaging catches issues that independent validators miss
(e.g., backend finds unprotected route, messages security validator who confirms
open-redirect risk). The gate passes only when 0 CRITICAL and 0 HIGH findings
remain across all domains.

- **backend-validator**: API routes, data-layer schema, architecture, error handling, query strategy
- **frontend-validator**: UI components, accessibility, responsive design, UX scenarios
- **security-validator**: Auth flows, RLS/permissions, security headers, test coverage strategy

### Team Pattern B: Phase 8 Implementation (TDD Pipeline)

Per-story TDD pipeline with strict file ownership to prevent merge conflicts:

1. **RED** -- quality-lead writes failing unit and integration tests
2. **GREEN** -- frontend-lead and backend-lead implement in parallel (blocked until tests exist)
3. **VERIFY** -- quality-lead runs all tests, then E2E browser verification

Each story produces 8 tasks. Stories execute sequentially; leads coordinate via
messaging on API contracts and shared types (created by team lead only).

### Team Pattern C: Audit (MapReduce)

Project-wide spec-vs-code divergence analysis:

1. **MAP** (parallel) -- spec-analyst inventories specs; implementation-analyst maps codebase
2. **REDUCE** (blocked until both maps complete) -- gap-resolver cross-references, categorizes as ALIGNED / SPEC_AHEAD / CODE_AHEAD / DIVERGED
3. **REMEDIATE** -- gap-resolver fixes CRITICAL/HIGH gaps, produces audit report

Designed to run iteratively. Each run reads previous audit state and reports
incremental changes.

## Architecture

### File Tree

```
sdd-team/
  SKILL.md                          # Orchestrator entry point
  README.md                         # This file
  examples/
    team-walkthrough.md             # End-to-end examples for all 3 patterns
  references/
    existing-sdd-import.md          # Relationship to base /sdd skill
    mcp-allocation.md               # MCP assignment matrix
    state-management.md             # 3-layer state model
    team-vs-subagent-guide.md       # Decision framework
    tool-patterns.md                # TeamCreate/TaskCreate/Spawn syntax
    universal-directives.md         # 11 rules all teammates follow
  templates/
    audit-report.md                 # Audit output format
    gap-analysis.md                 # Gap inventory format
    status-report.md                # Status check format
    validation-synthesis.md         # GATE 2 synthesis format
  workflows/
    audit-team.md                   # MapReduce audit workflow
    file-ownership.md               # Domain boundaries for parallel impl
    implementation-team.md          # TDD pipeline workflow
    validation-team.md              # GATE 2 validation workflow
    hooks/
      task-completed.md             # Pre-approval checklist for task completion
      teammate-idle.md              # Decision table for idle teammates
    spawn-prompts/
      backend-lead.md               # Phase 8: API, DB, migrations, RLS
      backend-validator.md          # GATE 2: API routes, schema, architecture
      frontend-lead.md              # Phase 8: UI components, pages, client state
      frontend-validator.md         # GATE 2: UI, a11y, responsive, UX
      gap-resolver.md               # Audit: cross-reference, remediate
      implementation-analyst.md     # Audit: map codebase by feature
      quality-lead.md               # Phase 8: TDD driver, E2E verification
      security-validator.md         # GATE 2: auth, RLS, headers, tests
      spec-analyst.md               # Audit: inventory and analyze specs
```

### Relationship to Base `/sdd`

`/sdd-team` imports phases 0-7 and 9 verbatim from the base `/sdd` skill. Only
phases where cross-domain coordination adds clear value are team-enhanced:

| Component | Base `/sdd` | `/sdd-team` |
|-----------|------------|-------------|
| GATE 2 | 6 independent subagents | 3 communicating teammates (each spawns 2-3 subagents) |
| Phase 8 | Sequential single agent | 3-teammate TDD pipeline (RED-GREEN-VERIFY) |
| Audit | Not available | MapReduce team for spec-vs-code analysis |
| Complexity routing | All same | 1-3 defers to `/sdd`; 4+ uses teams |
| State/gates/artifacts | Same | Same |

### Spawn Prompt Roles

| Role | Team | MCPs |
|------|------|------|
| backend-validator | GATE 2 | Supabase, Ref |
| frontend-validator | GATE 2 | Shadcn, Ref |
| security-validator | GATE 2 | Supabase, Ref |
| quality-lead | Phase 8 | Supabase, Chrome DevTools |
| frontend-lead | Phase 8 | Shadcn, Ref |
| backend-lead | Phase 8 | Supabase, Ref |
| spec-analyst | Audit | Firecrawl, Ref |
| implementation-analyst | Audit | Supabase, Chrome DevTools |
| gap-resolver | Audit | Supabase, Chrome DevTools |

## Recommended MCPs

| MCP | Used By | Purpose |
|-----|---------|---------|
| Firecrawl | Research subagents, Spec Analyst | Web scraping (2-step: search then scrape) |
| Supabase | Backend Validator, Backend Lead, Quality Lead, Impl Analyst, Gap Resolver | DB schema, auth, RLS, migrations, test data |
| Shadcn | Frontend Validator, Frontend Lead | React component registry |
| Chrome DevTools | Quality Lead, Impl Analyst, Gap Resolver | Browser automation, E2E, screenshots |
| Ref | All validators and leads | Documentation search |

Each role gets only the MCPs it needs. The team lead gets none -- it coordinates,
never does domain work directly.

## Known Constraints

| # | Constraint | Impact |
|---|-----------|--------|
| 1 | One team per session | Must `TeamDelete` before creating next team |
| 2 | No teammate session resumption | Re-create team, skip completed tasks via `.sdd/sdd-state.md` |
| 3 | Cost: 3-5x vs subagents | Only use teams at complexity 4+; 1-3 defers to `/sdd` |
| 4 | Task status lag | Nudge idle teammates after 5+ min silence |
| 5 | Terminal split-pane | Works in iTerm2/Terminal.app only (not VS Code/Ghostty) |

## Troubleshooting

| Error | Detection | Recovery |
|-------|-----------|----------|
| Teams not enabled | `TeamCreate` tool unavailable | Set `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`, restart CLI |
| Complexity too low | Score 1-3 | Defer to `/sdd` (no teams needed) |
| No spec.md | `/validate` without spec | Run `/sdd-team` or `/feature` first to create spec |
| GATE 2 FAIL | CRITICAL/HIGH findings in report | Fix spec issues listed in validation report, re-run `/sdd-team validate` |
| Audit FAIL | CRITICAL in audit report | Fix gaps listed in audit report |
| `[NEEDS CLARIFICATION]` | Markers in spec.md | Run `/clarify` to resolve ambiguities |
| Teammate crash | No response after timeout | Re-spawn with same name, reassign remaining tasks (max 2 retries) |
| Teammate repeat fail | 2+ crashes same role | Fallback to subagent-only for that domain |
| Team stuck | All idle, tasks pending | Nudge teammates, reassign, unblock dependencies |
| State corruption | Invalid `sdd-state.md` | Rebuild state from on-disk artifacts |
| TDD violation | Code written before tests | Delete code, restart with failing tests first |
| Team cleanup fail | `TeamDelete` errors | Manual cleanup: `rm -rf ~/.claude/teams/{name} ~/.claude/tasks/{name}` |

## CLAUDE.md Best Practices

Analysis of the project's `~/.claude/CLAUDE.md` (43 lines):

**Strengths:**
- "ULTRA IMPORTANT" section effectively grabs attention for MCP overrides
- Firecrawl 2-step pattern prevents real 200K+ token overflow
- Time estimation framework with real examples and risk multipliers

**Weaknesses:**
- 43 lines total -- too sparse for a global CLAUDE.md (target 80-150 lines)
- 30/43 lines (70%) are time estimation -- inverted priority ratio
- Missing: build commands, code conventions, git workflow, memory sync
- Missing: subfolder CLAUDE.md guidance, skills integration
- Typos: "ammount" should be "amount", "ethe coherent" should be "a coherent"
- No documentation philosophy (specs vs memory vs CLAUDE.md role separation)

**Recommendations:**
1. Move time estimation to `memory/MEMORY.md` (reference material, not convention)
2. Expand global CLAUDE.md to ~100 lines with build commands, code conventions, git workflow
3. Apply a CLAUDE-TEMPLATE.md to each project with project-specific sections
4. Establish 3-layer model: CLAUDE.md (conventions), memory/ (learnings), specs/ (targets)
5. Split project CLAUDE.md files exceeding 400 lines into root + subfolder files

## Version History

- **v1.1.0** (2026-02-07): Token optimization -- compressed SKILL.md from ~3800 to ~1869 lines, extracted patterns to references
- **v1.0.0**: Initial SDD Team skill with GATE 2, Phase 8, and Audit team workflows
