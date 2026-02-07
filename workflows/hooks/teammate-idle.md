# TeammateIdle Hook

Determines next action when a teammate goes idle. Idle is NORMAL — teammates go idle between every turn. Only evaluate when actively looking for work to assign. If teammate just sent you a message, they're waiting for your response — not a trigger for this hook.

## Decision Table

| Step | Check | If Yes | If No |
|------|-------|--------|-------|
| 1 | Has in_progress task? | Wait — they're working or awaiting your response | → Step 2 |
| 2 | Unclaimed unblocked tasks exist? | Assign lowest-ID task matching teammate's domain | → Step 3 |
| 3 | Review opportunities? (completed tasks needing second eyes) | Ask teammate to review peer's work on {feature} | → Step 4 |
| 4 | Has blocked tasks? | Nudge blocker for status; offer helper task if possible | → Step 5 |
| 5 | Truly idle? (no tasks at all) | Verify all assigned tasks complete → send shutdown_request | — |

### Assignment Protocol (Step 2)
1. Select lowest-ID unclaimed, unblocked task
2. Verify domain match (frontend tasks → frontend-lead, etc.)
3. `TaskUpdate({ taskId, owner: teammate_name })`
4. Message: "Assigned task #{id}: {subject}. Check TaskGet for details."

## Edge Cases

| Case | Action |
|------|--------|
| Multiple teammates idle simultaneously | Process in order; assign different tasks to each; if only one task, assign to best domain match |
| Teammate rejects shutdown | Read reason; let them continue if still working; clarify and re-request if misunderstanding |
| All teammates idle, tasks remain (all blocked) | Identify root blocker; leader unblocks if possible; escalate to user if external |
| Idle during TDD wait | Expected — frontend/backend wait for quality-lead's tests. Don't reassign unless wait is unusually long |

## Anti-Patterns

1. Don't spam idle teammates — one message per idle cycle
2. Don't reassign in-progress tasks — idle after a message doesn't mean stopped
3. Don't assign cross-domain tasks — frontend-lead shouldn't get backend work
4. Don't delay shutdown — if no work remains, shut down to save resources
5. Don't ignore peer DM summaries — teammate may be collaborating, no intervention needed
