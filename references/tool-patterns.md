# Tool Patterns Reference

## TeamCreate

```
TeamCreate({ team_name: "sdd-{type}-{feature}", description: "SDD {type} team for {feature}" })
```

## TaskCreate

```
TaskCreate({ subject: "...", description: "...", activeForm: "..." })
```

Descriptions must include: spec path, focus area, severity format, report location.

## Spawn Teammate

```
Task({ subagent_type: "general-purpose", name: "{role}", team_name: "sdd-{type}-{feature}",
       model: "opus", mode: "bypassPermissions", description: "...",
       prompt: "Read spawn prompt at ~/.claude/skills/sdd-team/workflows/spawn-prompts/{role}.md.
                Context: spec={path}, feature={name}, team={team_name}, teammates={list}." })
```

Teammates don't inherit conversation history. Spawn prompt must be self-contained.

## TaskUpdate

| Action | Call |
|--------|------|
| Assign | `TaskUpdate({ taskId: "N", owner: "teammate-name" })` |
| Start | `TaskUpdate({ taskId: "N", status: "in_progress" })` |
| Complete | `TaskUpdate({ taskId: "N", status: "completed" })` |
| Block | `TaskUpdate({ taskId: "N", addBlockedBy: ["M"] })` |

## SendMessage

| Type | Usage |
|------|-------|
| Direct | `SendMessage({ type: "message", recipient: "name", content: "...", summary: "5-10 words" })` |
| Shutdown | `SendMessage({ type: "shutdown_request", recipient: "name", content: "Work complete" })` |
| Broadcast | `SendMessage({ type: "broadcast", content: "...", summary: "..." })` — USE SPARINGLY |

## Cleanup Sequence

1. `SendMessage({ type: "shutdown_request" })` to each teammate
2. Wait for confirmations
3. `TeamDelete()`
