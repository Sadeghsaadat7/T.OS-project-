# TOS — Stage C7: Handoff Protocol
## Final Architecture Specification v1.0

**Project:** TOS — Trading Operations System  
**Stage:** C7 — Handoff Protocol  
**Document Status:** FINAL / FROZEN / CLOSED  
**Architecture Status:** Approved  
**Version:** v1.0  
**Supersedes:** C7 Final Draft v1.1  
**Scope:** Architecture only — no Prompt Engineering, Implementation, or Coding

---

## 1. Purpose

Stage C7 defines the architecture for controlled transfer of a Work Object, result, or authorized reference from an authorized Source to an authorized Recipient.

C7 ensures that a handoff preserves:

- Work Object identity
- source and recipient identity
- authority context
- gate context
- evidence and logic references
- exact version/context binding
- transfer integrity
- delivery status
- recipient acknowledgment
- failure and recovery history
- auditability

C7 does **not** become the owner of the Work Object, its lifecycle, its truth, its logic, or the authority that gave it meaning.

### Core Principle

> **Execute and record the transfer — without taking ownership of the thing being transferred or the authority that gave it meaning.**

---

# 2. Architectural Boundary

C7 owns the controlled execution and recording of a handoff.

C7 owns:

1. Handoff Request
2. Handoff Identity
3. Handoff Execution Authorization
4. Handoff Eligibility Checks
5. Source/Recipient Binding
6. Version and Context Binding
7. Dispatch
8. Delivery Verification
9. Recipient Acknowledgment
10. Attempt Management
11. Failure Recording
12. Authorized Recovery
13. Retry
14. Handoff Completion
15. Handoff Audit History

C7 does not own:

- Agent authority → C1
- Work Object definition and identity → C2
- Work Object lifecycle/state → C3
- Gate decisions → C4
- Evidence and truth determination → C5
- Logic determination → C6

---

# 3. C1–C7 Authority Separation

| Stage | Authority Domain | Responsibility |
|---|---|---|
| C1 | Agent Authority | Defines what an agent is authorized to do |
| C2 | Work Object Authority | Defines Work Object identity and structure |
| C3 | Lifecycle Authority | Controls Work Object lifecycle/state |
| C4 | Gate Authority | Produces and controls Gate decisions |
| C5 | Evidence/Truth Authority | Controls evidence and truth status |
| C6 | Logic Authority | Produces and controls operational logic/results |
| C7 | Handoff Execution Authority | Controls execution of an authorized transfer |

### Critical Rule

C7 does **not** become the general authorization layer.

C7 only determines whether the specific handoff may be executed under already-established authorities and conditions.

---

# 4. Core Architectural Distinctions

The following distinctions are mandatory.

## 4.1 Handoff ≠ Lifecycle Transition

A successful handoff does not automatically transition the Work Object.

C7 records transfer completion.

C3 determines lifecycle transition.

Therefore:

> Handoff Success ≠ Work Object Completion

---

## 4.2 Handoff ≠ Gate Decision

A Gate PASS does not automatically mean that C7 must execute a handoff.

C4 produces the Gate decision.

C7 consumes the applicable Gate decision as an input/context reference and executes the transfer only when its own execution conditions are satisfied.

---

## 4.3 Transfer Integrity ≠ Evidence Truth

C7 can verify that the transferred payload/reference is intact and corresponds to the bound version.

C7 does not determine whether the underlying evidence is true.

Truth authority remains with C5.

---

## 4.4 Handoff Execution ≠ Logic Validation

C7 transfers the authorized result/context produced through C6.

C7 does not reinterpret, promote, modify, or independently validate the underlying logic.

---

## 4.5 Handoff Success ≠ Content Correctness

Delivery and acknowledgment confirm transfer-level conditions only.

They do not constitute:

- Truth approval
- Logic approval
- Gate approval
- Evidence approval
- QC approval
- Semantic correctness approval

---

# 5. Handoff Identity

Every Handoff receives an independent and stable:

> **Handoff ID**

The Handoff ID identifies the transfer intent and remains stable across retries.

Example:

```text
HO-100
```

A retry does not create a new Handoff.

---

# 6. Handoff Attempt Identity

Every execution attempt receives a separate:

> **Attempt ID**

Example:

```text
HO-100
  Attempt-01 → FAILED
  Attempt-02 → TIMEOUT
  Attempt-03 → SUCCESS
```

This distinction is mandatory.

### Rule

> Same Handoff + new Attempt = retry of the same transfer intent.

A new Handoff ID is created only when a genuinely new transfer intent exists.

---

# 7. Intended vs Actual Participants

C7 must distinguish between:

- Intended Source
- Actual Sender
- Intended Recipient
- Actual Recipient
- Authority Holder

The intended participant is the participant defined by the handoff context.

The actual participant is the entity that actually executed or received the transfer.

This distinction preserves auditability when execution differs from the original plan.

---

# 8. Handoff Envelope and Payload

C7 separates:

## 8.1 Handoff Envelope

The envelope contains transfer-control information, including where applicable:

- Handoff ID
- Handoff Definition / Context Version
- Attempt ID
- Work Object ID
- Work Object Version
- Intended Source
- Actual Sender
- Intended Recipient
- Actual Recipient
- Authorization context
- Gate reference/version
- Evidence references/versions
- Logic references/versions
- timestamps
- Handoff status
- integrity information
- audit information

## 8.2 Payload

The payload is the actual result, artifact, Work Object reference, or authorized information being transferred.

The payload must be immutable for the relevant Handoff execution context, or represented by an immutable versioned reference.

If the underlying artifact is changed, the changed artifact must have its own identity/version according to the applicable Work Object architecture.

---

# 9. Version and Context Binding

C7 must bind the exact versions relevant to the handoff.

At minimum, where applicable:

- Work Object ID + Version
- Handoff Definition / Context Version
- Gate decision/reference + version
- Evidence references + versions
- Logic references + versions
- Source/Recipient identity
- Authorization context

Historical Handoffs must not silently resolve to the latest version.

### Version Model

Three distinct concepts must remain identifiable:

1. **Work Object Version**
2. **Handoff Definition / Context Version**
3. **Attempt ID**

A retry creates a new Attempt ID.

A retry does **not** automatically create a new Handoff Context Version.

---

# 10. Handoff Lifecycle

## 10.1 Core States

The core Handoff states are:

1. REQUESTED
2. AUTHORIZED
3. DISPATCHED
4. DELIVERED
5. ACKNOWLEDGED
6. COMPLETED

## 10.2 Terminal States

Where the applicable Handoff policy defines terminal conditions:

- REJECTED
- FAILED
- EXPIRED
- CANCELLED

## 10.3 Events / Conditions

The following are not automatically Handoff states:

- Eligibility Check
- Timeout
- Duplicate Detected
- Cancellation Requested
- Authorization Expiration
- QC Finding
- Partial Delivery Detected

They are Events or Conditions that may cause evaluation or transition.

## 10.4 Control Processes

The following are control processes rather than core states:

- Recovery
- Retry Decision
- Retry Execution

This prevents State Explosion and preserves the C3 distinction between State, Event, Condition, and Transition.

---

# 11. Canonical Handoff Flow

```text
HANDOFF REQUEST
      ↓
IDENTITY BINDING
      ↓
CONTEXT / VERSION SNAPSHOT
      ↓
ELIGIBILITY CHECK
      ↓
AUTHORIZATION
      ↓
AUTHORIZED
      ↓
DISPATCH
      ↓
DELIVERY VERIFICATION
      ↓
DELIVERED
      ↓
ACKNOWLEDGMENT
      ↓
ACKNOWLEDGED
      ↓
COMPLETED
```

---

# 12. Eligibility vs Authorization

These are separate controls.

## Eligibility

Eligibility asks:

> “Are the required execution preconditions currently satisfied?”

Examples include:

- required Work Object version is available
- intended recipient is valid
- required context is present
- required references are resolvable
- handoff has not passed its execution boundary
- required transfer conditions are satisfied

Eligibility is an execution-precondition check.

It must not become a hidden Gate.

## Authorization

Authorization asks:

> “Does applicable authority permit this Handoff execution within the defined scope and validity?”

Authorization is governed by the appropriate authority chain and consumed by C7 for execution.

### Mandatory Rule

> C7 Eligibility Check ≠ C4 Gate Decision.

---

# 13. State Transition Rules

## 13.1 Normal Flow

```text
REQUESTED
   ↓
AUTHORIZED
   ↓
DISPATCHED
   ↓
DELIVERED
   ↓
ACKNOWLEDGED
   ↓
COMPLETED
```

Transitions require the conditions defined for the applicable handoff context.

---

## 13.2 Attempt Failure / Timeout

An Attempt Failure or Timeout:

1. records the failed attempt
2. preserves the Handoff identity
3. evaluates recovery policy
4. may produce a new Attempt
5. may escalate
6. may terminate the Handoff if the Handoff-level failure condition is satisfied

### Mandatory distinction

> Attempt Failure ≠ Handoff Failure

A Handoff becomes FAILED only when its defined Handoff-level failure condition is satisfied, such as exhausted recovery, no authorized retry, or another explicitly defined terminal condition.

---

## 13.3 Eligibility Failure

An eligibility failure prevents or stops execution.

It does not automatically imply a specific terminal Handoff state unless the applicable policy explicitly defines one.

C7 must not convert an execution-precondition failure into a new Gate decision.

---

## 13.4 Recipient Rejection

Recipient rejection must be distinguishable from technical delivery failure.

Depending on policy, rejection may:

- trigger recovery
- trigger escalation
- create a REJECTED terminal state
- require another authorized action

C7 records the event and applies the defined handoff policy.

---

## 13.5 Authorization Expiration

Authorization expiration blocks further execution under that authorization.

It does not automatically mean that the Handoff itself is EXPIRED.

A new authorization may be required before a retry can occur.

---

## 13.6 Handoff Expiration

The Handoff becomes EXPIRED only when the Handoff's own defined expiration boundary has been reached and the applicable terminal condition is satisfied.

### Mandatory distinction

> Authorization Expiration ≠ Handoff Expiration

---

## 13.7 Cancellation

Cancellation requires:

- valid cancellation authority
- a cancellable lifecycle phase
- satisfaction of the applicable cancellation condition

Cancellation is distinct from failure.

Cancellation must never rewrite historical events.

---

# 14. Cancellation Boundaries

The ability to cancel depends on the current execution phase and applicable authority.

Illustrative boundary:

```text
REQUESTED
   → may be cancellable

AUTHORIZED
   → may be cancellable if authority permits

DISPATCHED
   → cancellation depends on whether execution is still reversibly stoppable

DELIVERED
   → historical delivery cannot be undone; corrective process required

ACKNOWLEDGED
   → historical acknowledgment cannot be erased; corrective process required

COMPLETED
   → completed Handoff cannot be rewritten; separate corrective action required
```

Cancellation does not erase the historical Handoff record.

---

# 15. Dispatch

Dispatch is the controlled initiation of transfer.

Before dispatch, C7 must ensure that:

- Handoff identity is bound
- required versions/context are bound
- execution eligibility is satisfied
- required authorization is valid
- the intended source and recipient are resolved
- payload/reference integrity conditions are satisfied

C7 may re-check execution preconditions immediately before dispatch.

If a previously valid condition has changed, C7 stops execution and returns control to the appropriate authority.

C7 does not create a new semantic or Gate decision merely because an execution condition changed.

---

# 16. Delivery Verification

Delivery is not assumed merely because dispatch occurred.

The transition:

```text
DISPATCH
   ↓
DELIVERY VERIFICATION
   ↓
DELIVERED
```

requires the defined delivery criteria to be satisfied.

If delivery criteria are not satisfied:

```text
Attempt Failure / Integrity Failure
        ↓
Recovery Evaluation
```

A partially delivered or corrupted payload must not be recorded as successfully delivered.

---

# 17. Recipient Acknowledgment

Recipient acknowledgment confirms the defined receipt and/or operational readiness condition.

It does **not** mean:

- content is true
- logic is correct
- Gate is approved
- evidence is valid
- QC has passed
- semantic correctness has been independently validated

Recipient acknowledgment is therefore distinct from:

- Delivery Verification
- QC Validation
- Gate Decision
- Evidence/Truth Validation
- Logic Validation

---

# 18. Failure and Recovery Architecture

Failure handling is attempt-aware and history-preserving.

Canonical pattern:

```text
ANY ATTEMPT
    ↓
FAILURE / TIMEOUT
    ↓
RECORDED
    ↓
RECOVERY POLICY
    ├── RETRY
    ├── ESCALATE
    └── TERMINATE
```

Recovery cannot self-grant authority.

Recovery actions must remain within valid authority and authorization boundaries.

---

# 19. Retry

A retry:

- keeps the same Handoff ID
- creates a new Attempt ID
- preserves all previous attempt history
- re-evaluates required execution conditions
- requires still-valid authorization or new authorization where necessary

Example:

```text
HO-100

Attempt-01 → FAILED
Attempt-02 → TIMEOUT
Attempt-03 → SUCCESS
```

This remains one Handoff.

---

# 20. Crash / Restart Recovery

A system crash or restart does not create a new Handoff by itself.

After recovery, C7 must reconcile the existing Handoff and Attempt history.

The system must avoid creating duplicate transfer intent merely because execution was interrupted.

### Rule

> Crash ≠ New Handoff

---

# 21. Duplicate and Idempotency Handling

C7 must be able to identify duplicate execution conditions.

Duplicate detection is an Event/Condition, not a core Handoff State.

The system must distinguish:

- duplicate request
- duplicate attempt
- duplicate delivery
- legitimate retry

A duplicate must not silently become a second independent Handoff.

---

# 22. Partial Delivery

Partial delivery must be explicitly detectable where applicable.

If the delivery does not satisfy the defined delivery criteria:

> It is not DELIVERED.

The partial delivery event must be recorded and routed through the applicable recovery path.

---

# 23. QC Boundary

QC is not a hidden part of Handoff execution.

QC may identify a problem after:

- dispatch
- delivery
- acknowledgment
- completion

A QC finding does not directly rewrite C7 history.

Canonical pattern:

```text
QC Finding
    ↓
Appropriate Authority
    ↓
Controlled Corrective Action
```

Example:

```text
C7 Handoff COMPLETED
        ↓
QC Finding
        ↓
Appropriate Authority
        ↓
Corrective Action
```

The original completed Handoff remains historically true.

---

# 24. Audit History

C7 must maintain an auditable history of:

- Handoff creation
- identity binding
- version/context binding
- authorization
- eligibility results
- dispatch
- delivery verification
- acknowledgment
- attempts
- failures
- timeouts
- retries
- recovery actions
- cancellation requests/actions
- rejection
- expiration
- completion
- relevant QC findings/references
- corrective actions/references

### Audit Rule

Historical Handoff records are append-only and must not be rewritten to make past execution appear different from what occurred.

---

# 25. End-to-End Architectural Example

```text
C6 Logic Result
      ↓
C4 Gate PASS
      ↓
C7 Handoff Request
      ↓
Identity Binding
      ↓
Version / Context Snapshot
      ↓
Eligibility Check
      ↓
Execution Authorization
      ↓
AUTHORIZED
      ↓
DISPATCH
      ↓
Delivery Verification
      ↓
DELIVERED
      ↓
Recipient Acknowledgment
      ↓
ACKNOWLEDGED
      ↓
COMPLETED
      ↓
C7 Handoff Outcome
      ↓
C3 Lifecycle Evaluation
```

Important:

The final C3 lifecycle decision remains a C3 responsibility.

C7 completion does not automatically complete the Work Object.

---

# 26. Cross-Stage Relationship

```text
C1 — Agent Authority
        │
        ├──────────────┐
        ↓              │
C2 — Work Object      │
        │              │
        ↓              │
C3 — Lifecycle        │
        │              │
        ↓              │
C4 — Gate Decision    │
        │              │
        ↓              │
C5 — Evidence/Truth   │
        │              │
        ↓              │
C6 — Logic            │
        │              │
        └──────┐       │
               ↓       ↓
          C7 — Handoff Protocol
               │
               ↓
        Transfer Outcome
               │
               ↓
        C3 Lifecycle Evaluation
```

The diagram represents dependency and information flow, not transfer of ownership between stages.

---

# 27. Five Core Architectural Invariants

### Invariant 1
> **Handoff ≠ Lifecycle Transition**

### Invariant 2
> **Handoff ≠ Gate Decision**

### Invariant 3
> **Transfer Integrity ≠ Evidence Truth**

### Invariant 4
> **Handoff Execution ≠ Content / Logic Validation**

### Invariant 5
> **Handoff Success ≠ Work Object Completion**

These invariants must remain intact in future implementation and evolution.

---

# 28. Failure / Recovery Invariants

1. Attempt failure does not automatically equal Handoff failure.
2. Retry does not create a new Handoff unless the transfer intent itself changes.
3. Recovery cannot self-grant authority.
4. Authorization expiration is distinct from attempt timeout.
5. Handoff expiration is distinct from authorization expiration.
6. Duplicate detection does not create a new Handoff.
7. Partial delivery is not successful delivery.
8. Cancellation does not erase history.
9. QC findings do not rewrite historical Handoff states.
10. Crash/restart does not create a new transfer intent automatically.

---

# 29. Non-Goals

C7 does not define:

- agent design
- agent prompt engineering
- Work Object schema ownership
- general lifecycle design
- Gate semantics
- Evidence Truth methodology
- Logic methodology
- trading strategy
- market interpretation
- implementation technology
- database technology
- transport technology
- programming language
- deployment architecture
- UI/UX

Those concerns remain outside C7 unless explicitly assigned to a later architecture stage.

---

# 30. Architectural Completion Criteria

C7 is architecturally complete when the system can unambiguously answer:

1. What is being handed off?
2. Which exact Work Object/version is being transferred?
3. Who is the intended Source?
4. Who actually executed the transfer?
5. Who is the intended Recipient?
6. Who actually received it?
7. Under what authority is the transfer executed?
8. Which Gate decision/context applies?
9. Which Evidence versions apply?
10. Which Logic versions apply?
11. Which Handoff ID identifies the transfer intent?
12. Which Attempt ID identifies each execution attempt?
13. What state is the Handoff in?
14. What event or condition caused the current transition?
15. Was delivery actually verified?
16. Was recipient acknowledgment received?
17. Did an attempt fail?
18. Was recovery authorized?
19. Was a retry performed?
20. Did authorization expire?
21. Did the Handoff itself expire?
22. Was cancellation requested or executed?
23. Was a duplicate detected?
24. Was delivery partial or corrupted?
25. Was the Handoff completed?
26. What historical evidence proves what occurred?
27. Does completion affect the Work Object lifecycle?
28. If QC later finds an issue, how is corrective action handled without rewriting history?

C7 architecture provides explicit ownership and control for each of these questions.

---

# 31. Final Architectural Statement

C7 establishes the controlled transfer layer of TOS.

It provides a reliable mechanism for moving authorized Work Objects, results, or references between participants while preserving identity, authority, context, version, integrity, acknowledgment, failure history, recovery history, and auditability.

C7 remains deliberately bounded:

> **It executes and records the transfer without taking ownership of the object, lifecycle, truth, gate, or logic being transferred.**

---

## Document Status

**FINAL / FROZEN / CLOSED**

This document is the approved Stage C7 architectural specification.

No Prompt Engineering, Implementation, Coding, or Tool-Level execution design is included in this stage.

Future changes to this document require an explicit architectural reason and must follow the TOS change-control process.
