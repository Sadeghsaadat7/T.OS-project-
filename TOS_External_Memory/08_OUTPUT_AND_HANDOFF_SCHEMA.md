# 08 — Output, Evidence and Handoff Standard

## Recommended structured result
```xml
<agent_result>
  <status>COMPLETE|PARTIAL|BLOCKED|CONFLICT</status>
  <facts>...</facts>
  <interpretation>...</interpretation>
  <assumptions>...</assumptions>
  <evidence>...</evidence>
  <confidence>
    <level>HIGH|MEDIUM|LOW|UNKNOWN</level>
    <reason>...</reason>
  </confidence>
  <conclusion>...</conclusion>
  <validation>
    <passed>...</passed>
    <failed>...</failed>
    <unresolved>...</unresolved>
  </validation>
  <handoff>
    <next_agent>...</next_agent>
    <required_inputs>...</required_inputs>
    <warnings>...</warnings>
  </handoff>
</agent_result>
```

## Evidence record
```yaml
claim_id:
claim:
source:
source_type:
title:
date:
url:
timestamp:
evidence:
status:
confidence:
notes:
```

## Handoff must state
1. What was determined.
2. What remains unknown.
3. Evidence supporting the result.
4. What the next agent is expected to do.
5. What must not be reinterpreted without evidence.

## Machine vs human output
Use structured XML/JSON/YAML for agent-to-agent transfer and Markdown for human-readable reports. Important artifacts may contain both.

## Validation questions
- Was the task completed?
- Were required inputs available?
- Were sources valid?
- Were boundaries respected?
- Were contradictions found?
- What remains unresolved?
