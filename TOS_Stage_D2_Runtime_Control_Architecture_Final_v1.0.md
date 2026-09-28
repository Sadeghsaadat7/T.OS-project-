# TOS — Trading Operations System

# Stage D2 — Runtime Control Architecture

**Document Status:** FINAL / FROZEN / CLOSED  
**Version:** v1.0  
**Stage:** D2  
**Architecture Domain:** Runtime Control  
**Predecessors:** D0 — Stage D Boundary Definition; D1 — Operational Coordination Architecture  
**Architecture Principle:** Architecture First

---

# 1. Purpose

Stage D2 defines the minimum architecture required to control the TOS Runtime during operational execution.

The purpose of D2 is to ensure that Runtime execution can be:

- allowed,
- restricted,
- paused,
- stopped,
- resumed,
- enabled, or
- disabled

under controlled Runtime-level conditions.

D2 exists to keep Runtime execution operationally controlled without taking ownership of semantic meaning, Flow Coordination, Operational Continuity, Recovery, Authority, or Work execution.

---

# 2. Core Architectural Principle

> **Runtime Control does not create Semantic Authority.**

D2 controls Runtime conditions.

D2 does not determine what Runtime activity means.

Therefore:

> **D2 controls Runtime; it does not define the meaning of Runtime activity.**

---

# 3. D2 Architectural Objective

D2 must provide the minimum Runtime Control capability required for TOS to prevent uncontrolled execution.

The minimum objective is:

> **When Runtime-level conditions permit execution, D2 must allow controlled execution; when Runtime-level conditions require limitation, D2 must be able to restrict, pause, stop, or otherwise disable Runtime execution without assuming ownership of the underlying semantic or operational decision.**

---

# 4. D2 Scope

D2 owns the following Runtime-level concerns:

1. Runtime State Control
2. Runtime Control Actions
3. Runtime Constraints
4. Runtime Safety Boundary

These four areas constitute the **Minimum Sufficient Architecture** for D2.

No additional subsystem is introduced unless a proven architectural requirement demonstrates that one of these four areas is insufficient.

---

# 5. D2 Minimum Sufficient Architecture

## 5.1 Runtime State Control

D2 maintains and controls the Runtime state relevant to Runtime-level operation.

The minimum Runtime states are:

- ENABLED
- RESTRICTED
- PAUSED
- STOPPED
- DISABLED

These states describe Runtime control conditions only.

They do not represent:

- Work Object state,
- Lifecycle state,
- Flow state,
- Recovery state,
- Authority state,
- Semantic validity, or
- Trading decision state.

---

## 5.2 Runtime Control Actions

D2 provides the minimum Runtime-level control actions required by TOS:

- ENABLE
- DISABLE
- ALLOW
- RESTRICT
- PAUSE
- STOP
- RESUME

These actions are Runtime control actions only.

They must not be interpreted as semantic judgments.

For example:

`STOP` means that Runtime execution is to be stopped.

It does not mean:

- the Flow is invalid,
- the Work Object failed,
- the Logic is incorrect,
- the Recovery has failed,
- an Authority decision has been made, or
- a trading decision has been rejected.

---

## 5.3 Runtime Constraints

D2 must be able to apply Runtime-level restrictions when Runtime execution cannot proceed under normal conditions.

A Runtime Constraint limits what Runtime may operationally execute.

A Runtime Constraint does not determine:

- what the Work means,
- which Flow is semantically correct,
- which Logic is valid,
- which Evidence is true,
- who possesses Authority, or
- how Recovery should occur.

Therefore:

> **D2 enforces Runtime constraints; it does not own the meaning behind those constraints.**

---

## 5.4 Runtime Safety Boundary

D2 must provide a Runtime-level safety boundary capable of preventing uncontrolled continuation.

When a Runtime-level condition requires containment, D2 must be able to apply an appropriate Runtime control action such as:

- RESTRICT,
- PAUSE, or
- STOP.

The purpose is containment of Runtime execution.

The safety boundary does not authorize D2 to diagnose semantic causes or assume ownership of Recovery.

---

# 6. Runtime Control Model

The conceptual D2 model is:

```text
                 D1
          Flow Coordination
                 |
                 | Execution Context
                 v
        +----------------------+
        |          D2          |
        |   Runtime Control    |
        |                      |
        |  Runtime State       |
        |  Control Actions     |
        |  Constraints         |
        |  Safety Boundary     |
        +----------+-----------+
                   |
                   v
          Controlled Runtime
```

D2 controls the Runtime conditions under which execution may occur.

D2 does not execute the Work itself.

---

# 7. Runtime State Semantics

## ENABLED

Runtime is enabled for normal Runtime-level operation.

`ENABLED` does not mean that every Work Object or Flow is valid.

---

## RESTRICTED

Runtime remains operational but is subject to one or more Runtime-level restrictions.

`RESTRICTED` does not determine which operational Flow is semantically correct.

---

## PAUSED

Runtime execution is temporarily suspended under controlled Runtime conditions.

`PAUSED` does not define Operational Continuity.

---

## STOPPED

Runtime execution has been stopped.

`STOPPED` does not itself indicate whether the underlying operation failed or whether Recovery is required.

---

## DISABLED

Runtime is not enabled for operation under the applicable Runtime control conditions.

`DISABLED` does not represent an Authority decision.

---

# 8. Runtime Control vs Flow Coordination

D1 and D2 have distinct responsibilities.

### D1

D1 coordinates the operational Flow.

### D2

D2 controls whether Runtime conditions permit that Flow to execute.

The relationship is:

```text
D1
  |
  | Coordinated Flow
  v
D2
  |
  | Runtime Control Condition
  v
Runtime
```

D2 may restrict execution without changing the Flow.

D2 must not:

- redesign the Flow,
- reroute the Flow,
- select an alternative Flow,
- determine Flow correctness, or
- interpret Flow semantics.

Therefore:

> **D1 coordinates the Flow; D2 controls the Runtime conditions under which the Flow may execute.**

---

# 9. Runtime Control vs Operational Continuity

D2 and D3 must remain separate.

D2 may:

- PAUSE Runtime,
- STOP Runtime,
- RESTRICT Runtime.

D3 owns Operational Continuity.

D2 must not determine:

- how operations continue after interruption,
- what operational state should be preserved,
- where an operation should continue,
- how a Flow should be resumed from a continuity perspective.

Boundary principle:

> **D2 may interrupt Runtime execution; D3 owns Operational Continuity around that interruption.**

---

# 10. Runtime Control vs Recovery

D2 and D5 must remain separate.

When a Runtime abnormality occurs, D2 may provide containment:

```text
Runtime Abnormality
        |
        v
       D2
        |
   PAUSE / STOP /
     RESTRICT
        |
        v
       D5
        |
     Recovery
```

D2 does not own:

- failure diagnosis,
- Recovery planning,
- Recovery workflow,
- Recovery completion,
- Recovery validation.

Boundary principle:

> **D2 provides Runtime containment; D5 owns Recovery.**

---

# 11. RESUME Boundary

`RESUME` is a Runtime control action.

Within D2:

> `RESUME` means that Runtime may return from a controlled paused/stopped condition to an applicable execution condition.

`RESUME` must not mean:

- Recovery completed,
- Flow approved,
- Work validated,
- Authority granted,
- Logic validated,
- Evidence validated.

A Recovery decision remains within D5.

A semantic or operational decision remains within its owning architecture.

---

# 12. Runtime Control vs Authority

D2 does not create, determine, or adjudicate Authority.

D7 preserves and enforces Authority Boundaries.

Where an Authority-related constraint has a Runtime consequence, D2 may enforce the applicable Runtime restriction.

Conceptually:

```text
Authority Boundary
        |
        v
       D7
        |
        | Applicable Runtime Constraint
        v
       D2
        |
        v
Runtime Control
```

D2 must not determine:

- who owns Authority,
- whether an Actor possesses Authority,
- which conflicting Authority claim is superior,
- whether a semantic instruction is legitimate.

Boundary principle:

> **D7 owns Authority Boundaries; D2 enforces applicable Runtime-level consequences.**

---

# 13. D2 and Execution

D2 controls execution conditions.

D2 does not execute Work.

Therefore:

> **D2 controls execution conditions; D2 does not execute the work itself.**

D2 must not become:

- an Activity Executor,
- an Agent Executor,
- a Work Processor,
- a Trading Decision Engine, or
- a Strategy Engine.

---

# 14. Runtime Control Signal

D2 exposes the minimum conceptual mechanism required to communicate Runtime control actions:

> **Runtime Control Signal**

A Runtime Control Signal represents a Runtime-level control instruction or condition such as:

- ENABLE,
- DISABLE,
- ALLOW,
- RESTRICT,
- PAUSE,
- STOP,
- RESUME.

The Runtime Control Signal must remain Runtime-focused.

It must not become a general-purpose semantic message or coordination mechanism.

D2 must not introduce an independent messaging architecture merely for Runtime Control unless a later proven architectural requirement requires it.

---

# 15. Failure Containment Boundary

When Runtime enters an abnormal condition that requires immediate containment:

```text
Runtime Abnormality
        |
        v
D2 Runtime Safety Boundary
        |
        +----> RESTRICT
        |
        +----> PAUSE
        |
        +----> STOP
```

The selected Runtime action contains execution.

The underlying failure and its Recovery remain outside D2.

This prevents D2 from absorbing D5.

---

# 16. D2 Exclusions

The following are explicitly outside D2 ownership.

## 16.1 Semantic Ownership

D2 does not own:

- Work semantics,
- Logic,
- Evidence,
- Truth,
- Gate meaning,
- Handoff meaning.

These remain governed by the applicable architectures in Stage C.

---

## 16.2 Coordination

D2 does not own:

- Flow design,
- Flow routing,
- Work sequencing,
- Agent coordination,
- Operational Flow decisions.

These remain outside D2.

---

## 16.3 Operational Continuity

D2 does not own:

- Continuity planning,
- Continuity state management,
- Operational continuation strategy,
- Preservation strategy.

These belong to D3.

---

## 16.4 Recovery

D2 does not own:

- Failure diagnosis,
- Recovery planning,
- Recovery execution,
- Recovery completion,
- Recovery validation.

These belong to D5.

---

## 16.5 Authority

D2 does not own:

- Identity ownership,
- Authority definition,
- Authority adjudication,
- Semantic conflict resolution.

These remain outside D2 and within the appropriate authority architecture.

---

## 16.6 Execution

D2 does not own:

- Work execution,
- Agent execution,
- Trading decisions,
- Strategy decisions.

---

# 17. D2 Boundary Rules

The following rules are mandatory architectural boundaries:

### D2-BR-01
D2 controls Runtime; it does not define Runtime activity semantics.

### D2-BR-02
D2 must not change or redesign the coordinated Flow.

### D2-BR-03
D2 may contain Runtime execution without owning Operational Continuity.

### D2-BR-04
D2 may contain Runtime failure without owning Recovery.

### D2-BR-05
D2 does not create or adjudicate Authority.

### D2-BR-06
D2 Runtime States must not be interpreted as Work, Lifecycle, Flow, Recovery, or Authority states.

### D2-BR-07
D2 Control Actions must remain Runtime-level actions.

### D2-BR-08
`RESUME` must not imply Recovery completion or semantic validation.

### D2-BR-09
D2 must not become an Activity Executor or Agent Executor.

### D2-BR-10
D2 must remain the minimum Runtime Control layer required by TOS.

---

# 18. D2 Architectural Boundaries

The principal boundaries are:

```text
D1
Flow Coordination
      |
      | Flow / Execution Context
      v
D2
Runtime Control
      |
      | Runtime Conditions
      v
Runtime
```

And:

```text
D2 ↔ D3
Runtime Control ↔ Operational Continuity

D2 ↔ D5
Runtime Containment ↔ Recovery

D2 ↔ D7
Runtime Enforcement ↔ Authority Boundaries
```

Each side retains its own ownership.

---

# 19. Minimum Sufficient Architecture Check

The D2 architecture is considered sufficient when it provides:

| Requirement | Covered |
|---|---|
| Runtime State Control | YES |
| Runtime Start/Stop Control | YES |
| Runtime Enable/Disable | YES |
| Runtime Restriction | YES |
| Runtime Pause/Resume | YES |
| Runtime Safety Boundary | YES |
| Flow Coordination | Excluded |
| Operational Continuity | Excluded |
| Recovery | Excluded |
| Authority Adjudication | Excluded |
| Semantic Decision | Excluded |
| Work Execution | Excluded |
| Trading Strategy | Excluded |

No additional architectural component is currently justified.

---

# 20. Boundary Stress Test Result

The targeted D2 Boundary Stress Test covered:

1. D1 ↔ D2
2. D2 ↔ D3
3. D2 ↔ D5
4. D2 ↔ D7

Result:

> **PASS**

The tests found no architectural reason to expand D2 into Coordination, Continuity, Recovery, Authority, or Semantic Processing.

---

# 21. Architectural Consistency Review Result

The targeted consistency review covered:

- D0 ↔ D2
- D1 ↔ D2
- D2 ↔ D3
- D2 ↔ D5
- D2 ↔ D7

Result:

> **CLEAN / PASS**

Additional checks:

- Authority Leakage → PASS
- Scope Creep → PASS
- Over-design → PASS

No architectural reason was identified to reopen D0 or any Frozen Stage C architecture.

---

# 22. Design Restraint

D2 intentionally does not define:

- implementation technology,
- software components,
- APIs,
- infrastructure,
- deployment topology,
- databases,
- code,
- prompts,
- agent implementation,
- monitoring technology.

These are outside the current Architecture-First scope.

D2 defines only the architectural responsibility and minimum control structure required for TOS Runtime Control.

---

# 23. Final Architectural Principle

The complete D2 architecture can be summarized as:

```text
                D2
                 |
     +-----------+-----------+
     |           |           |
 Runtime      Runtime     Runtime
 State        Control     Safety
 Control      Actions     Boundary
     |           |
     +-----------+
           |
     Runtime Constraints
```

The governing principle is:

> **D2 controls whether and under what Runtime-level conditions TOS execution may proceed. It does not decide what the execution means, what Flow should be followed, how continuity should be preserved, how failure should be recovered, or who possesses Authority.**

---

# 24. Architectural Status

**D2 Minimum Sufficient Architecture: PASS**

**Boundary Stress Test: PASS**

**Targeted Consistency Review: PASS**

**Document-Level Review: PASS**

**Authority Leakage: NOT FOUND**

**Scope Creep: NOT FOUND**

**Over-design: NOT FOUND**

**Missing Required Architecture: NOT FOUND**

**Architectural Reopening Required: NO**

**Status: FINAL / FROZEN / CLOSED**
