# TaskCompleted Hook

Validates task completion before marking as done. Team lead runs this when a teammate reports task complete.

## When This Runs

Triggered when teammate sends "task done" / "implementation complete" or calls `TaskUpdate` with `status: completed`.

## Pre-Approval Checklist

| Check | What | How | Pass | Reject Message |
|-------|------|-----|------|---------------|
| File Ownership | All modified files within teammate's domain | Cross-reference `git diff --name-only` against `file-ownership.md` | All files in domain | "File ownership violation: {file} outside {domain}. Revert and message {correct_owner}." |
| Test Existence | Implementation has corresponding tests (frontend/backend leads ONLY) | Check for test files matching each impl file; must have `it()`/`test()` blocks | Tests exist, non-trivial | "TDD Article III: No tests for {file}. Coordinate with quality-lead." |
| Constitution | Matches spec (Art IV), no over-engineering (Art VI), quality (Art VIII) | Compare code against spec acceptance criteria | Compliant | "Constitution violation — Article {N} ({name}): {issue}" |
| Artifact Update | `tasks.md`, `sdd-state.md`, `audit-trail.md` updated | Check artifacts reflect completion | Updated | SOFT reject: "Please update {artifact}" |

## Verdict Logic

1. File ownership FAIL → **REJECT** (hard block)
2. Test existence FAIL → **REJECT** (hard block — TDD non-negotiable)
3. Constitution FAIL → **REJECT** (hard block)
4. Artifact update FAIL → **SOFT REJECT** (ask for update, don't block critical path)
5. All pass → **APPROVE**

## Post-Approval Actions

1. Mark task `completed` via TaskUpdate
2. Check TaskList for next unblocked task
3. If next task exists: assign via TaskUpdate with `owner`
4. If no tasks remain: evaluate review opportunity or shutdown

## Contingency: Repeated Rejections (3+)

- Message teammate asking if they need help or are blocked
- Consider reassigning or breaking into smaller pieces
- Spawn code-reviewer subagent for independent assessment
