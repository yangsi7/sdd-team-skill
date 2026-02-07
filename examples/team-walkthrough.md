# SDD-Team Walkthrough: End-to-End Examples

Three team patterns with messaging flows and state transitions. Tool call syntax in @references/tool-patterns.md.

---

## Example 1: GATE 2 Validation Team

### User Command
```
/sdd-team "Add user authentication with OAuth"
```

### What Happens

**Phases 0-4 (Solo):** Lead runs discovery, product definition, constitution, specification, and clarification using base `/sdd` skill. GATE 1 passes.

**GATE 2 Team Spawns:**
- Lead creates team `sdd-validation-user-auth`
- Creates 3 validation tasks (backend, frontend, security) + 1 synthesis task (blocked by all 3)
- Spawns 3 validator teammates, each with domain-specific MCPs
- Assigns one task to each validator

**Cross-Domain Messaging:**
- Backend validator finds unprotected `/api/auth/callback` route accepting unvalidated `redirect_url`
- Messages security validator: "Open redirect risk in OAuth callback"
- Security validator confirms, adds HIGH severity finding, recommends allowlist validation
- This cross-domain discovery is the key advantage over independent subagents

**Synthesis:**
- All validators complete → synthesis task unblocks
- Lead collects findings, writes `specs/user-auth/validation-report.md`
- Determines verdict: PASS (0 critical, 0 high) or FAIL

**Cleanup:** Shutdown all teammates → TeamDelete

### State Transitions
```
BEFORE: sdd-state.md → phase=GATE_2, status=PENDING
DURING: ~/.claude/teams/sdd-validation-user-auth/ (ephemeral)
AFTER:  sdd-state.md → gate2_status=PASS, phase=5
        specs/user-auth/validation-report.md created
```

---

## Example 2: Implementation Team

### User Command
```
/sdd-team implement
```

### What Happens

**Pre-check:** Lead verifies GATE 3 passed, `tasks.md` exists, phase=8 pending.

**Team Creation:**
- Lead creates team `sdd-impl-user-auth`
- Creates TDD pipeline tasks from `tasks.md` stories: write tests → implement → verify (chained with dependencies)
- Spawns quality-lead, frontend-lead, backend-lead

**TDD Pipeline Messaging:**
- Quality lead writes failing tests for Story 1, messages both leads: "OAuth tests ready, start implementing"
- Frontend lead asks backend lead: "What's the callback response shape?"
- Backend lead responds: "Redirect to /dashboard on success, /login?error={code} on error"
- Both leads implement in parallel within their file domains
- Quality lead re-runs tests, verifies all pass

**State Checkpoints:**
- After each story verified: lead updates `sdd-state.md` checkpoint
- After all stories: phase=8, status=COMPLETE

**Cleanup:** Shutdown all teammates → TeamDelete

### State Transitions
```
BEFORE: sdd-state.md → phase=8, status=PENDING
DURING: ~/.claude/teams/sdd-impl-user-auth/ (ephemeral)
AFTER:  sdd-state.md → phase=8, status=COMPLETE
```

---

## Example 3: Existing Specs Audit

### User Command
```
/sdd-team audit
```

### What Happens

**Pre-check:** Lead checks `specs/` directory exists. No sdd-state.md required (standalone).

**Team Creation:**
- Lead creates team `sdd-audit-myproject`
- Creates Phase A tasks (parallel mapping) + Phase B task (gap analysis, blocked by mapping)
- Spawns spec-analyst, impl-analyst, gap-resolver

**MapReduce Messaging:**
- Spec analyst maps all spec files → messages gap-resolver: "12 features across 8 specs"
- Impl analyst maps all code (using Supabase + Chrome DevTools) → messages gap-resolver: "10 features implemented"
- Gap resolver compares maps, categorizes gaps (ALIGNED/SPEC_AHEAD/CODE_AHEAD/DIVERGED)
- Finds: 2 HIGH gaps (missing GitHub OAuth, missing avatar upload), 1 CODE_AHEAD (Settings page), 1 SPEC_AHEAD (push notifications)

**Remediation:** Gap resolver dynamically creates remediation task for CRITICAL/HIGH gaps, fixes them.

**Cleanup:** Lead writes audit report → shutdown → TeamDelete

### State Transitions
```
BEFORE: sdd-state.md → (may not exist)
DURING: ~/.claude/teams/sdd-audit-myproject/ (ephemeral)
AFTER:  .sdd/audit-reports/audit-{date}-round-1.md (persistent)
        sdd-state.md → audit results recorded
```
