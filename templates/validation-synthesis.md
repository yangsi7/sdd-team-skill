# Validation Report: {Feature Name}

**Date**: {YYYY-MM-DD}
**Team**: sdd-validation-{feature-slug}
**Spec**: `{path/to/spec.md}`

## Validator Summary

| Domain | Validator | Findings | Verdict |
|--------|-----------|----------|---------|
| Backend | backend-validator | {C}C / {H}H / {M}M / {L}L | {PASS/FAIL} |
| Frontend | frontend-validator | {C}C / {H}H / {M}M / {L}L | {PASS/FAIL} |
| Security | security-validator | {C}C / {H}H / {M}M / {L}L | {PASS/FAIL} |

## Consolidated Findings

| ID | Domain | Severity | Finding | Recommendation |
|----|--------|----------|---------|----------------|
| V-001 | {domain} | {CRITICAL/HIGH/MEDIUM/LOW} | {description} | {recommendation} |

## Cross-Domain Issues

| ID | Source Domain | Affected Domain | Finding | Implication | Recommendation |
|----|-------------|-----------------|---------|-------------|----------------|
| X-001 | {domain} | {domain} | {finding} | {implication} | {recommendation} |

## Overall Verdict: {PASS / FAIL}

Pass criteria: 0 Critical AND 0 High findings across all domains.

| Criterion | Status |
|-----------|--------|
| 0 Critical findings | {MET / NOT MET — {N} critical} |
| 0 High findings | {MET / NOT MET — {N} high} |

## Blocking Issues (if FAIL)

| # | ID | Domain | Severity | Finding | Required Resolution |
|---|-----|--------|----------|---------|-------------------|
| 1 | {V-XXX} | {domain} | {CRITICAL/HIGH} | {finding} | {resolution} |

## Recommended Spec Changes

| Section | Current | Recommended Change | Rationale |
|---------|---------|-------------------|-----------|
| {section} | {current text} | {proposed text} | {finding ID} |

## Medium/Low Findings (Non-Blocking)

| ID | Domain | Severity | Finding | Recommendation |
|----|--------|----------|---------|----------------|
| {V-XXX} | {domain} | {MEDIUM/LOW} | {finding} | {recommendation} |

## Next Steps

- [ ] If PASS: Proceed to Phase 5 (Planning)
- [ ] If FAIL: Resolve blocking issues, re-run `/sdd-team validate`
- [ ] Advisory: Address medium/low findings during Phase 8
