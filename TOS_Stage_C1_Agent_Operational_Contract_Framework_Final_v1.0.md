# TOS — Stage C1 Agent Operational Contract Framework

**Stage:** C — Operational Specification  
**Component:** C1 — Agent Operational Contracts  
**Status:** FINAL  
**Version:** 1.0  
**Authority:** TOS Engineer / Human Authority

---

## 1. Purpose

C1 converts the frozen Stage B agent architecture into deterministic operational contracts for individual agents.

The contract defines the boundaries within which an agent may operate, the inputs it may receive, the outputs it must produce, the conditions for execution and completion, prohibited behavior, escalation, handoff, auditability, and change control.

C1 does **not** create Prompt Engineering artifacts, implementation code, or trading-agent logic.

## 2. Architectural Position

```text
STAGE B — WHAT THE SYSTEM IS
            ↓
C1 — HOW EACH AGENT IS AUTHORIZED TO OPERATE
            ↓
C2 — WHAT WORK OBJECTS THE AGENT OPERATES ON
            ↓
C3 — HOW WORK OBJECTS MOVE THROUGH STATES
```

C1 must remain consistent with the frozen Stage B architecture.

## 3. Governing Principles

### 3.1 Contract Boundary

An agent operates only within its approved Contract and its current Assignment.

```text
AGENT CONTRACT
      +
CURRENT ASSIGNMENT
      ↓
ACTUAL OPERATING BOUNDARY
```

The Agent Contract defines the agent's standing capabilities and restrictions. The Assignment defines the specific work authorized in the current execution.

An Assignment may narrow an agent's scope but may not expand the authority granted by the Contract.

### 3.2 Reasoning Boundary

An agent may reason, analyze, infer, and hypothesize within its authorized boundaries. It may not redefine its Contract, Assignment, Authority, architecture, policies, or evidence standards.

> **AN AGENT MAY REASON WITHIN ITS CONTRACT AND ASSIGNMENT, BUT IT MAY NOT REDEFINE EITHER.**

### 3.3 Human Authority

The TOS Engineer / Human Authority remains the final authority for architecture, frozen decisions, controlled changes, and promotion decisions explicitly reserved to the Engineer.

### 3.4 No Silent Change

No agent may silently modify architecture, agent roles, scope/non-scope, evidence hierarchy or standards, output contracts, QC rules, workflow, knowledge schema, repository structure, or approved policies. Controlled changes require explicit approval and versioning.

## 4. Canonical Agent Contract

Every operational agent must have a Contract containing:

```yaml
agent_contract:
  agent_id:
  agent_name:
  agent_version:
  mission:
  scope:
  non_scope:
  responsibilities:
  authority_boundary:
  inputs:
    required:
    optional:
  outputs:
    required:
    optional:
  dependencies:
  preconditions:
  operating_rules:
  forbidden_actions:
  completion_conditions:
  failure_conditions:
  blocked_conditions:
  conflict_conditions:
  handoff:
  escalation:
  human_authority_interaction:
  audit_requirements:
  traceability_requirements:
  contract_change_protocol:
  version_control:
  contract_status:
```

## 5. Contract Field Specifications

### 5.1 Identity

Required: `agent_id`, `agent_name`, `agent_version`.

Identity must uniquely identify the operational agent and its current Contract version.

### 5.2 Mission

Defines why the agent exists. Mission must describe operational purpose, not become an unrestricted list of possible activities.

### 5.3 Scope

Defines the work the agent is authorized to perform. Scope must be explicit enough for independent QC to determine whether an action is inside or outside authority.

### 5.4 Non-Scope

Defines activities the agent is explicitly not authorized to perform. It is a control boundary, not merely documentation.

Examples include architecture redesign, uncontrolled scope expansion, unsupported ICT rule creation, replacing another specialized role, bypassing QC/audit, and converting inference/hypothesis into ICT teaching.

### 5.5 Responsibilities

Defines concrete duties expected when they fall inside Scope and Assignment. Responsibilities must not create authority beyond Scope.

### 5.6 Authority Boundary

Defines what the agent may execute, recommend, escalate, or never decide.

```text
MAY_EXECUTE
MAY_RECOMMEND
MUST_ESCALATE
MUST_NOT_DECIDE
```

Examples:

| Decision / Action | Agent Authority |
|---|---|
| Execute assigned research | MAY_EXECUTE |
| Produce an inference | MAY_EXECUTE, if permitted by role |
| Produce a hypothesis | MAY_EXECUTE, if permitted by role |
| Recommend a change | MAY_RECOMMEND |
| Resolve an architecture conflict | MUST_ESCALATE |
| Change frozen architecture | MUST_NOT_DECIDE |
| Change evidence hierarchy | MUST_NOT_DECIDE |
| Bypass a gate | MUST_NOT_DECIDE |
| Promote hypothesis to validated operational knowledge | MUST_ESCALATE / Engineer approval required |

Authority is determined by Contract and higher-level TOS policy, not by agent confidence.

### 5.7 Inputs

Inputs are divided into Required and Optional. Required Inputs must exist before normal execution. An agent must not silently invent missing Required Inputs.

### 5.8 Outputs

Outputs are divided into Required and Optional. Required Outputs are mandatory deliverables for the Assignment. Outputs must be structured sufficiently for downstream validation and handoff. Stage B reporting and `agent_result` contracts remain inherited requirements.

### 5.9 Dependencies

Defines upstream objects, agents, services, evidence, policies, or control components required by the agent. Dependencies must be explicit enough to identify blocking conditions.

### 5.10 Preconditions

Generic baseline:

```text
Assignment exists
AND Required Inputs are available
AND source/work object is identified
AND agent Contract is valid
AND required dependencies are available
AND no blocking condition prevents execution
```

If not satisfied:

```text
DO NOT EXECUTE → BLOCKED → REPORT REASON → ESCALATE IF REQUIRED
```

### 5.11 Operating Rules

Baseline rules:

1. Operate only within Contract and Assignment.
2. Use only authorized inputs.
3. Preserve source and evidence traceability.
4. Do not silently fill missing evidence with assumptions.
5. Distinguish facts from interpretation.
6. Classify inference and hypothesis explicitly where applicable.
7. Preserve contradictions and unknowns.
8. Do not modify frozen architecture or policy.
9. Do not bypass QC, audit, or integration gates.
10. Do not declare completion without satisfying Completion Conditions.
11. Escalate unresolved authority, evidence, or structural conflicts.
12. Preserve sufficient execution context for downstream inspection.

### 5.12 Forbidden Actions

Forbidden Actions must be testable by QC.

```text
ARCHITECTURE_MODIFICATION
SCOPE_EXPANSION
AUTHORITY_ESCALATION
SOURCE_MISREPRESENTATION
UNSUPPORTED_CLAIM
HIDDEN_INFERENCE
HYPOTHESIS_AS_FACT
EXTERNAL_FRAMEWORK_INJECTION
GATE_BYPASS
SILENT_COMPLETION
SILENT_DATA_MODIFICATION
EVIDENCE_FABRICATION
```

Agent-specific forbidden actions must be added where required.

### 5.13 Completion Conditions

Completion is system-verifiable, not merely an agent assertion.

```text
AGENT CLAIM OF COMPLETION ≠ SYSTEM COMPLETION
```

Generic completion requires assigned scope assessed; required work performed or explicitly recorded as incomplete; required outputs produced; mandatory evidence attached where applicable; assumptions, contradictions, unknowns, and limitations identified; required validation performed; handoff prepared; and required report contract satisfied.

The Completeness Check and later gates determine whether work may advance.

### 5.14 Failure Conditions

`FAILED` means the agent attempted authorized work but could not complete it because execution itself failed. The report must state what failed, where, available evidence, impact, retry/rework possibility, and escalation if applicable. Failure must not become `COMPLETE`.

### 5.15 Blocked Conditions

`BLOCKED` means the agent cannot legitimately continue because a required prerequisite, dependency, authorization, source, or input is unavailable. The reason and required unblock action must be preserved.

### 5.16 Conflict Conditions

`CONFLICT` means relevant information, sources, results, interpretations, or system constraints materially disagree. Conflicts must be preserved rather than silently resolved.

A conflict record should identify conflicting items, evidence for each, nature of conflict, affected work, unresolved status, and required resolution authority.

### 5.17 Handoff

Every handoff must contain enough information for the receiving component to continue without reconstructing hidden context.

Minimum:

```text
source_object
work_object
task_id
assignment_id
sending_agent
receiving_agent
current_status
completed_work
incomplete_work
evidence
assumptions
contradictions
unknowns
validation_performed
warnings
required_next_action
required_inputs
escalation_requirements
```

The standard Stage B `agent_result` contract remains mandatory. Logic agents additionally use the Stage B `logic_classification` structure.

### 5.18 Escalation

Escalation is required when the agent encounters a condition outside its authority or contract.

Baseline triggers include architecture/policy conflict, authority ambiguity, evidence conflict requiring higher-level resolution, blocked dependency requiring intervention, request to change frozen rules or expand scope, unresolved material contradiction, and proposed promotion requiring Engineer approval.

Escalation must state:

```text
WHY_ESCALATED
WHAT_IS_BLOCKED
WHAT_EVIDENCE_EXISTS
WHAT_DECISION_IS_REQUIRED
WHO_HAS_AUTHORITY
```

### 5.19 Human Authority Interaction

The TOS Engineer / Human Authority remains the final authority for controlled decisions reserved by TOS architecture.

Agents may research, analyze, infer, hypothesize, test where authorized, recommend, report, and escalate.

Agents may not independently redesign Frozen Stage B architecture, alter system-wide policy, redefine authority, change evidence hierarchy, bypass gates, or promote hypotheses into validated operational knowledge where Engineer approval is required.

### 5.20 Audit Requirements

Every operational Agent Contract must be auditable. Auditability must allow verification of Contract version, Assignment, inputs, outputs, evidence, assumptions, decisions, escalations, handoff, and forbidden actions.

### 5.21 Traceability Requirements

Material claims and operational outputs must remain traceable to source, work object, assignment, evidence, and Agent execution where applicable.

```text
SOURCE → WORK OBJECT → ASSIGNMENT → AGENT → RESULT
→ EVIDENCE → VALIDATION → HANDOFF → KNOWLEDGE OBJECT
```

### 5.22 Contract Change Protocol

An Agent cannot change its own Contract.

```text
CHANGE PROPOSAL
      ↓
IMPACT / CONSISTENCY REVIEW
      ↓
TOS ENGINEER APPROVAL
      ↓
NEW CONTRACT VERSION
      ↓
VERSIONED RELEASE
      ↓
OLD VERSION PRESERVED
```

A proposed change must identify current version, proposed change, reason, affected agents/components, affected workflows/gates, compatibility impact, and validation requirements.

### 5.23 Version Control

Each Contract must have an immutable version identifier, effective version, change history, approval record, and status. Previous approved versions remain traceable. Historical execution remains associated with the Contract version active at execution time.

### 5.24 Contract Status

```text
DRAFT
PROPOSED
APPROVED
ACTIVE
SUPERSEDED
RETIRED
```

Only an approved/active Contract may authorize normal operational execution.

## 6. Relationship Between Contract and Assignment

The distinction is mandatory:

```text
AGENT CONTRACT → standing authority
        +
ASSIGNMENT → current authorized work
        =
EXECUTION BOUNDARY
```

Rules:

1. Assignment cannot grant authority forbidden by Contract.
2. Assignment can narrow Scope.
3. Assignment must identify required outputs.
4. Assignment must identify dependencies and status.
5. Agent must not extend Assignment without authorization.
6. A new task requires a new or explicitly updated Assignment.

## 7. Relationship With Stage B Contracts

C1 operationalizes, but does not replace, Stage B contracts.

Stage B remains the architectural source for agent identity/role, mandatory research report, standard `agent_result`, logic classification, evidence policy, batch lifecycle, gate architecture, and Human Authority.

C1 adds the operational conditions required to execute those frozen structures deterministically.

## 8. QC Testability

Q01 must be able to test C1 compliance, including:

```text
Correct Agent Contract used?
Correct Contract version used?
Current Assignment valid?
Execution within Scope?
Required Inputs present?
Required Outputs produced?
Forbidden Actions avoided?
Evidence and assumptions identified?
Contradictions and unknowns preserved?
Completion Conditions satisfied?
Correct status reported?
Handoff complete?
Escalation rules followed?
Unauthorized change attempted?
```

A Contract requirement that cannot be reasonably inspected should be refined before operational deployment.

## 9. C1 Constitutional Rules

```text
1. CONTRACT > AGENT PREFERENCE
2. ASSIGNMENT > UNSPECIFIED WORK
3. AUTHORITY > AGENT CONFIDENCE
4. EVIDENCE > ASSERTION
5. GATE RESULT > AGENT CLAIM OF COMPLETION
6. EXPLICIT STATUS > SILENT ASSUMPTION
7. VERSIONED CHANGE > SILENT MODIFICATION
8. HUMAN AUTHORITY > AGENT STRUCTURAL DECISION
```

> **SOURCE TRUTH ≠ AGENT REASONING ≠ AGENT HYPOTHESIS**

## 10. C1 Final Operational Model

```text
                    TOS ENGINEER
                         │
                         ▼
                APPROVED AGENT CONTRACT
                         │
                         ▼
                 CURRENT ASSIGNMENT
                         │
                         ▼
                    PRECONDITIONS
                         │
                 ┌───────┴───────┐
                 │               │
              BLOCKED          READY
                                 │
                                 ▼
                            AGENT EXECUTION
                                 │
              ┌──────────┬──────┼──────────┐
              ▼          ▼      ▼          ▼
            RESULT     FAILURE CONFLICT   ESCALATION
              │
              ▼
       COMPLETION CHECK
              │
              ▼
          QC / AUDIT
              │
              ▼
            HANDOFF
```

## 11. C1 Definition of Done

C1 is complete when:

- every operational agent can be represented by the canonical Contract
- Contract and Assignment are explicitly separated
- Scope and Non-Scope are enforceable
- Authority boundaries are explicit
- Required and Optional Inputs are defined
- Required and Optional Outputs are defined
- Preconditions are defined
- Operating Rules are defined
- Forbidden Actions are testable
- Completion is system-verifiable
- Failure, Blocked, and Conflict are distinct
- Handoff requirements are explicit
- Escalation requirements are explicit
- Human Authority remains final where required
- Auditability and traceability are mandatory
- Contract changes are controlled and versioned
- Q01 can independently test Contract compliance
- no rule conflicts with Frozen Stage B architecture

## 12. Stage C Progress

```text
C1 — Agent Operational Contracts
STATUS: FINAL

NEXT:
C2 — Work Object Specification
```

C1 is an operational specification layer only. Prompt Engineering and implementation remain outside the scope of this document.

## Version Record

| Version | Status | Change |
|---|---|---|
| v1.0 | FINAL | Initial final C1 Agent Operational Contract Framework; added explicit Contract/Assignment separation, Authority Boundary, deterministic completion/failure/block/conflict conditions, handoff, escalation, auditability, traceability, and controlled Contract change protocol. |
