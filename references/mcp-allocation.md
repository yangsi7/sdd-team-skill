# MCP Allocation Matrix

## Principle

Each role gets ONLY the MCPs it needs. Fewer MCPs = less context pollution, no cross-domain accidents, more token budget for actual work.

## Validation Team (GATE 2)

| Role | MCPs | Rationale |
|------|------|-----------|
| Team Lead | None | Coordination only — delegates all validation work |
| Backend Validator | Supabase, Ref | Queries schema/RLS for alignment; references framework docs |
| Frontend Validator | Shadcn, Ref | Verifies component registry; references React/Next.js docs |
| Security Validator | Supabase, Ref | Inspects RLS, auth config, storage rules; references OWASP |

## Implementation Team (Phase 8)

| Role | MCPs | Rationale |
|------|------|-----------|
| Team Lead | None | Coordination only — manages TDD pipeline |
| Frontend Lead | Shadcn, Ref | Installs UI components; references React/Next.js docs |
| Backend Lead | Supabase, Ref | Runs migrations, manages RLS/storage; references framework docs |
| Quality Lead | Supabase, Chrome DevTools, Telegram, Gmail | Test data setup; E2E browser tests; notification verify; auth email flows |

## Audit Team (Existing Specs)

| Role | MCPs | Rationale |
|------|------|-----------|
| Team Lead | None | Coordination only — orchestrates MapReduce |
| Spec Analyst | Firecrawl, Ref | Cross-references external docs; checks framework conventions |
| Implementation Analyst | Supabase, Chrome DevTools | Verifies DB state + UI via screenshots |
| Gap Resolver | Supabase, Chrome DevTools | Same access as Impl Analyst to verify fixes |

## Subagent MCPs (Spawned by Teammates)

| Subagent Purpose | MCPs | Notes |
|------------------|------|-------|
| Research | Firecrawl, Ref | Two-step: `firecrawl_search` → `firecrawl_scrape` |
| Code exploration | None | Uses Glob, Grep, Read tools only |
| DB verification | Supabase | Query-only, no mutations |
| UI verification | Chrome DevTools | Screenshot + DOM inspection |
| Notification test | Telegram | Send/verify test notifications |
| Auth flow test | Gmail | Verify auth emails |

## Gmail Configuration

- **Email**: simon.yang.ch@gmail.com
- **Used by**: Quality Lead (E2E auth flow testing)
- **Access pattern**: Read-only inbox search for verification codes and confirmation links

## Anti-Patterns

| Anti-Pattern | Why It's Wrong |
|-------------|---------------|
| Give all MCPs to all teammates | Token waste, context pollution, cross-domain accidents |
| Give Team Lead MCPs | Lead should coordinate, not do domain work |
| Skip Ref for validators | Validators guess at patterns instead of verifying |
| Give Firecrawl to implementation team | Implementation uses pre-researched findings, not live research |
