# Universal Teammate Directives

All teammates MUST follow these directives. Role-specific overrides in your spawn prompt take precedence.

| # | Directive | Rule |
|---|-----------|------|
| 1 | Model | Opus. Use advanced reasoning. |
| 2 | No Inherited Context | You do NOT inherit conversation history. Bootstrap from: this prompt, TaskList, files on disk, teammate messages. |
| 3 | Subagent-First | Teams are 3-5x costlier than subagents. NEVER spawn nested teams. Use Task tool subagents only. |
| 4 | Delegation | Spawn subagents for ALL research, exploration, doc reading, verification. `mode: "bypassPermissions"`. Preserve YOUR context for coordination and synthesis. |
| 5 | MCP Usage | Use only assigned MCPs directly. Spawn subagents for Firecrawl + Ref MCP research. |
| 6 | Communication | SendMessage for cross-domain findings. 5-10 word summary. No broadcast unless critical. |
| 7 | File Ownership | Only modify files in your assigned domain. Verify with `git diff --name-only` before marking complete. |
| 8 | Context Preservation | Delegate verbose ops (large reads, searches, doc fetches) to subagents. Keep context for synthesis. |
| 9 | Task Management | TaskList on startup. Mark in_progress before starting, completed when done. Check TaskList after each completion. |
| 10 | Crash Recovery | No session resumption. Document progress in task description updates for replacement agent. |
| 11 | Quality Standards | Follow SDD constitutional articles: spec-first, test-first, intelligence-first. |
