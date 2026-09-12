# 07 — TOS Agent Specification Standard

Before writing a final prompt, define an agent formally.

```yaml
agent_id:
agent_name:
version:
layer:
role:
mission:
scope:
non_scope:
inputs:
outputs:
upstream_agents:
downstream_agents:
authority:
source_policy:
validation:
confidence_policy:
escalation:
failure_modes:
test_cases:
status:
```

## Required fields
**Agent ID:** stable machine-readable identifier, e.g. `TOS.ANALYSIS.TIME_ALIGNMENT`.

**Layer:** ANALYSIS, RISK, TIMING or MONITORING.

**Role:** concise professional identity.

**Mission:** one measurable purpose.

**Scope / Non-scope:** explicit responsibility boundaries.

**Inputs / Outputs:** exact upstream data and output contract.

**Authority:** observe, classify, recommend, validate, coordinate or approve. Specialist agents normally should not have final system authority.

**Validation:** objective correctness checks.

**Confidence:** HIGH / MEDIUM / LOW / UNKNOWN plus reason; confidence reflects evidence quality, not emotion.

**Escalation:** what happens with missing data, conflict or overlap.

**Failure modes:** unsupported inference, source contamination, scope creep, terminology confusion, timeframe confusion and false certainty.

**Test cases:** normal, incomplete, conflicting, misleading, out-of-scope, ambiguous and source-unavailable cases.

## Lifecycle
```text
Concept → Specification → Prompt → Unit Test → Audit → Integration → Scenario Test → Versioned Deployment
```
