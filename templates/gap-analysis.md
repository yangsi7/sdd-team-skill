# Gap Analysis: {Project Name}

**Analysis Date**: {YYYY-MM-DD}
**Spec Analyst**: spec-analyst
**Implementation Analyst**: impl-analyst
**Gap Resolver**: gap-resolver

## Gap Inventory

| # | Feature | Spec Location | Code Location | Gap Type | Description | Root Cause | Severity | Recommended Action |
|---|---------|--------------|--------------|----------|-------------|-----------|----------|-------------------|
| 1 | {feature} | `{spec path}` or N/A | `{code path}` or N/A | {ALIGNED/SPEC_AHEAD/CODE_AHEAD/DIVERGED} | {gap} | {cause} | {severity} | {action} |

## Root Cause Categories

| Category | Description | Typical Action |
|----------|-------------|---------------|
| `intentional_skip` | Feature consciously deferred, spec not updated | Update spec with DEFERRED status |
| `accidental_drift` | Implementation diverged during development | Reconcile spec or code |
| `spec_outdated` | Spec written for earlier version | Update spec to current requirements |
| `code_refactored` | Code restructured, spec not synced | Update spec to match code |
| `new_undocumented` | Feature added without spec | Create spec or document skip decision |

## Findings by Gap Type

### ALIGNED Features ({N} total)

| Feature | Spec | Code | Notes |
|---------|------|------|-------|
| {feature} | `{path}` | `{path}` | {notes} |

### SPEC_AHEAD Features ({N} total)

| Feature | Spec | Missing Code | Root Cause | Severity | Action |
|---------|------|-------------|-----------|----------|--------|
| {feature} | `{path}` | {expected} | {cause} | {severity} | {action} |

### CODE_AHEAD Features ({N} total)

| Feature | Code | Missing Spec | Root Cause | Severity | Action |
|---------|------|-------------|-----------|----------|--------|
| {feature} | `{path}` | {expected} | {cause} | {severity} | {action} |

### DIVERGED Features ({N} total)

| Feature | Spec Says | Code Does | Root Cause | Severity | Action |
|---------|-----------|-----------|-----------|----------|--------|
| {feature} | {spec} | {actual} | {cause} | {severity} | {plan} |

## Summary Statistics

| Gap Type | Count | % of Total |
|----------|-------|-----------|
| ALIGNED | {N} | {N}% |
| SPEC_AHEAD | {N} | {N}% |
| CODE_AHEAD | {N} | {N}% |
| DIVERGED | {N} | {N}% |
| **Total** | **{N}** | **100%** |

| Severity | Count |
|----------|-------|
| CRITICAL | {N} |
| HIGH | {N} |
| MEDIUM | {N} |
| LOW | {N} |

**Alignment Score**: {ALIGNED / Total * 100}%

## Remediation Priority Queue

| Priority | # | Feature | Gap Type | Severity | Action | Effort |
|----------|---|---------|----------|----------|--------|--------|
| P1 | {gap#} | {feature} | {type} | CRITICAL | {action} | {S/M/L} |
| P2 | {gap#} | {feature} | {type} | HIGH | {action} | {S/M/L} |
