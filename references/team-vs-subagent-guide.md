# Teams vs Subagents: Decision Guide

## Key Differences

| Aspect | Subagents (Task tool) | Teams (Agent Teams) |
|--------|----------------------|---------------------|
| Communication | None between subagents | Direct messaging (SendMessage) |
| Shared State | None | Shared task list |
| Autonomy | Single task, returns result | Multiple tasks, persistent |
| Context | Fresh per invocation | Preserved across tasks |
| Coordination | Parent orchestrates | Peers coordinate |
| Cost | Lower (single turn) | Higher (persistent sessions) |

## When to Use: Comparison

| Criteria | Teams | Subagents |
|----------|-------|-----------|
| Cross-domain communication needed | Yes | No |
| Interdependent work (API contracts) | Yes | No |
| Pipeline patterns (test → impl → verify) | Yes | No |
| Shared context across operations | Yes | No |
| Independent, narrow specialist tasks | No | Yes |
| Fire-and-forget (research, doc lookup) | No | Yes |
| Parent aggregates results | No | Yes |

## Decision Tree

```
Need cross-domain communication?
├─ YES → TEAM
└─ NO
    ├─ Pipeline/dependency pattern? → YES → TEAM
    ├─ Multiple related tasks, same agent? → YES → TEAM (context preservation)
    ├─ Parallel with synthesis needed? → TEAM
    ├─ Parent aggregates? → SUBAGENT
    └─ One-shot specialist? → SUBAGENT
```

## Hybrid Pattern (What SDD-Team Uses)

- **Team level**: 3 autonomous teammates coordinate via messaging
- **Subagent level**: Each teammate spawns subagents for research and narrow tasks
- Cross-domain awareness (team) + efficient delegation (subagents)

## Team Sizing

Always 3 teammates: 80% of successful examples use 3; 5-6 tasks per teammate is optimal; more than 3 → coordination overhead exceeds benefit.

## SDD Phase → Mode Mapping

| Phase | Mode | Rationale |
|-------|------|-----------|
| 0-4 (Discovery → Clarification) | Solo | User dialog, sequential decisions |
| GATE 2 | **Team (3)** | Cross-domain validation |
| 5-7 (Planning → Audit) | Solo | Deterministic, single-pass |
| GATE 3 | Solo | Gate check |
| 8 (Implementation) | **Team (3)** | TDD pipeline |
| 9 (Finalization) | Solo | Sequential wrap-up |
| Audit (standalone) | **Team (3)** | MapReduce gap analysis |
