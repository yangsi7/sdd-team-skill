# Existing SDD Import Map

## Overview

`/sdd-team` extends base `/sdd` (at `~/.claude/skills/sdd/`) with Agent Teams at 3 high-value points. Most phases imported verbatim — only phases where cross-domain coordination adds clear value are team-enhanced.

## Imported Verbatim (No Team Enhancement)

| Phase | Skill Reference | Mode |
|-------|----------------|------|
| 0 (Discovery) | `@.claude/skills/sdd/phases/00-discovery/SKILL.md` | Solo + subagents |
| 1 (Product) | `@.claude/skills/sdd/phases/01-product/SKILL.md` | User dialog |
| 2 (Constitution) | `@.claude/skills/sdd/phases/02-constitution/SKILL.md` | User dialog |
| 3 (Specification) | `@.claude/skills/sdd/phases/03-specification/SKILL.md` | Solo + subagents |
| 4 (Clarification) | `@.claude/skills/sdd/phases/04-clarification/SKILL.md` | User dialog |
| 5 (Planning) | `@.claude/skills/sdd/phases/05-planning/SKILL.md` | Solo |
| 6 (Tasks) | `@.claude/skills/sdd/phases/06-tasks/SKILL.md` | Solo |
| 7 (Audit) | `@.claude/skills/sdd/phases/07-audit/SKILL.md` | Solo |
| 9 (Finalization) | `@.claude/skills/sdd/phases/09-finalization/SKILL.md` | Solo |

## Team-Enhanced Phases

| Phase | Enhancement | Workflow File |
|-------|------------|---------------|
| GATE 2 (Validation) | 3-teammate validation team replaces 6 parallel subagents | `@workflows/validation-team.md` |
| 8 (Implementation) | 3-teammate TDD pipeline replaces sequential implementation | `@workflows/implementation-team.md` |

## New Capability (Not in Base SDD)

| Capability | Workflow File | Notes |
|-----------|---------------|-------|
| Existing Specs Audit | `@workflows/audit-team.md` | MapReduce pattern, iterative remediation |

## Base SDD vs SDD-Team Comparison

| Aspect | Base SDD | SDD-Team |
|--------|----------|----------|
| GATE 2 | 6 independent subagents | 3 communicating teammates (each spawns 2-3 subagents) |
| Phase 8 | Sequential single agent | 3-teammate TDD pipeline (quality → impl → verify) |
| Audit | Not available | `/sdd-team audit` for existing codebase audit |
| Complexity routing | All same | 1-3 defers to base `/sdd`; 4+ uses teams |
| Intent detection | Same patterns | + new "audit" intent |
| State management | Same `.sdd/sdd-state.md` | Same schema |
| Gate enforcement | Same criteria | Same criteria |
| Artifact formats | Same schemas | Same schemas |
| Phase sequencing | Same order | Same order |

Verbatim phases: use `Skill("sdd", args="phase {N}")`. Team-enhanced phases: load `@workflows/{type}-team.md`.

## Complexity-Based Routing

| Complexity Score | Routing |
|-----------------|---------|
| 1-3 (Simple) | Defer entirely to base `/sdd` — no teams needed |
| 4-6 (Moderate) | Use `/sdd-team` with teams at GATE 2 and Phase 8 |
| 7-10 (Complex) | Use `/sdd-team` with full team support + deeper subagent chains |
