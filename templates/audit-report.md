# SDD Audit Report: {Project Name}

**Date**: {YYYY-MM-DD}
**Audit Round**: #{N}
**Auditor Team**: sdd-audit-{project-slug}

## Executive Summary

| Metric | Value |
|--------|-------|
| Total Spec Files | {N} |
| Total Features | {N} |
| Alignment Score | {N}% |
| Critical Gaps | {N} |
| High Gaps | {N} |
| Medium Gaps | {N} |
| Low Gaps | {N} |

**Overall Assessment**: {HEALTHY | DRIFTING | DIVERGED | CRITICAL}

- HEALTHY: >90% alignment, 0 critical
- DRIFTING: 70-90% alignment, 0 critical
- DIVERGED: 50-70% alignment, or 1+ critical
- CRITICAL: <50% alignment, or 3+ critical

## Gap Inventory

| # | Feature | Spec Status | Code Status | Gap Type | Severity | Action |
|---|---------|-------------|-------------|----------|----------|--------|
| 1 | {feature} | {EXISTS/MISSING} | {EXISTS/MISSING} | {ALIGNED/SPEC_AHEAD/CODE_AHEAD/DIVERGED} | {severity} | {action} |

## Detailed Findings

### SPEC_AHEAD Gaps

| Feature | Spec Location | Expected Implementation | Recommended Action |
|---------|--------------|------------------------|-------------------|
| {feature} | `{path}` | {what should exist} | {Implement / Archive / Defer} |

### CODE_AHEAD Gaps

| Feature | Code Location | What Code Does | Recommended Action |
|---------|--------------|----------------|-------------------|
| {feature} | `{path}` | {description} | {Create spec / Document / Remove} |

### DIVERGED Gaps

| Feature | Spec Says | Code Does | Root Cause | Recommended Action |
|---------|-----------|-----------|-----------|-------------------|
| {feature} | {spec} | {actual} | {cause} | {plan} |

## Root Cause Analysis

| Root Cause Category | Count | Examples |
|-------------------|-------|---------|
| `intentional_skip` | {N} | {description} |
| `accidental_drift` | {N} | {description} |
| `spec_outdated` | {N} | {description} |
| `code_refactored` | {N} | {description} |
| `new_undocumented` | {N} | {description} |

## Remediation Plan

### Priority 1: Critical Gaps (Immediate)

| # | Gap | Action | Effort |
|---|-----|--------|--------|
| 1 | {gap} | {steps} | {S/M/L} |

### Priority 2: High Gaps (This Sprint)

| # | Gap | Action | Effort |
|---|-----|--------|--------|
| 1 | {gap} | {steps} | {S/M/L} |

### Priority 3: Medium/Low Gaps (Backlog)

| # | Gap | Action | Effort |
|---|-----|--------|--------|
| 1 | {gap} | {steps} | {S/M/L} |

## Recommendations for Preventing Future Drift

1. {Recommendation}: {details}
2. {Recommendation}: {details}
3. {Recommendation}: {details}
