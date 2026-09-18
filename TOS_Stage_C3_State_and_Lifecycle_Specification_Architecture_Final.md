# TOS — Stage C3
# State & Lifecycle Specification — Architecture Final
# Version: 1.0
# Status: FINAL / APPROVED FOR GITHUB UPLOAD

## 1. Purpose

Stage C3 defines how a TOS Work Object exists, progresses, changes state, pauses, resumes, is reworked, is handed off, and reaches closure during its operational lifecycle.

C2 defines what a Work Object is and its structural specification. C3 defines how that Work Object lives and moves through its lifecycle.

C3 does not define detailed Gate specifications, Evidence/Truth taxonomy, Operational Logic, or Handoff Protocol. Those belong to later stages.

## 2. Core Conceptual Distinctions

- `STATE ≠ EVENT`
- `STATE ≠ TRANSITION`
- `STATE ≠ CONDITION`
- `STATE ≠ QUALITY`
- `STATE ≠ QC RESULT`
- `STATE ≠ GATE RESULT`
- `AGENT COMPLETION ≠ SYSTEM COMPLETION`
- `AGENT CONFIDENCE ≠ QC VALIDATION`

State is a stable, authoritative operational condition. Event is something that happened. Transition is controlled movement between states. Condition is a criterion required for a transition.

## 3. C3 Architecture Domains

1. `C3.1 — Lifecycle Model`
2. `C3.2 — State Model`
3. `C3.3 — State Definitions`
4. `C3.4 — Transition Model`
5. `C3.5 — Transition Authority`
6. `C3.6 — Entry / Exit Conditions`
7. `C3.7 — Exception, Rework & Recovery Paths`
8. `C3.8 — State / Event History & Auditability`
9. `C3.9 — Lifecycle Integrity & QC Interface`

## 4. C3.1 — Lifecycle Model

Normal lifecycle:

`CREATE → ASSIGN → EXECUTE → SUBMIT → VALIDATE → HANDOFF → CLOSE`

Core State representation:

`CREATED → ASSIGNED → READY → IN_PROGRESS → SUBMITTED → VALIDATING → VALIDATED → HANDED_OFF → CLOSED`

The lifecycle is a controlled operational process, not merely a status label.

## 5. C3.2 — State Model

Core states:

1. `CREATED`
2. `ASSIGNED`
3. `READY`
4. `IN_PROGRESS`
5. `SUBMITTED`
6. `VALIDATING`
7. `VALIDATED`
8. `HANDED_OFF`
9. `CLOSED`

`READY` is retained at architecture level because Assignment does not necessarily mean all operational prerequisites for execution are satisfied. During detailed specification, it must be tested against an explicit operational distinction; if none exists, it may be removed rather than retained for symmetry.

The following are controlled Lifecycle Contexts, not Core States:

- `BLOCKED`
- `WAITING_FOR_INPUT`
- `REWORK_REQUIRED`
- `DEPENDENCY_PENDING`
- `EXCEPTION`

Example:

`State = IN_PROGRESS`
`Context = BLOCKED`
`Reason = Missing Required Input`

This prevents State Explosion.

Additional terminal states such as `TERMINATED`, `INVALIDATED`, or `CANCELLED` may be supported only when each has distinct operational meaning and defined lifecycle behavior. They are not Core States unless formally justified.

## 6. C3.3 — State Definitions

Every Core State must have an explicit operational definition establishing:

- State meaning
- Entry conditions
- Exit conditions
- Permitted outgoing transitions
- Authorized transition actor/authority
- Relevant lifecycle contexts
- Relationship to ownership
- Relationship to version
- Relationship to validation/QC
- Relationship to handoff
- Prohibited transitions

State definitions must not embed detailed Gate, Evidence/Truth, or Logic specifications.

## 7. C3.4 — Transition Model

Every valid transition follows:

`SOURCE STATE → TRANSITION → CONDITIONS → AUTHORITY → TARGET STATE`

Execution sequence:

`REQUEST → CHECK CONDITIONS → CHECK AUTHORITY → EXECUTE TRANSITION → RECORD TRANSITION → UPDATE CURRENT STATE`

A committed Transition must be atomic. There must be no partial lifecycle update causing State, Version, Ownership, or required Transition Records to become inconsistent.

If a Transition is invalid, Current State must not change. The invalid attempt may still be recorded for audit.

The requester is not necessarily the authorizer.

Only one authoritative valid Transition may commit against a given Current Work Object Version at a time. The system must prevent race conditions, lost updates, conflicting simultaneous transitions, and silent State corruption.

## 8. C3.5 — Transition Authority

Every Transition must have defined authority.

Authority must establish:

- Who may request it
- Who may authorize it
- Conditions limiting that authority
- Prohibited transitions for the requesting actor
- Whether independent QC is required
- Whether the actor performing the work may authorize its own lifecycle completion

The Agent being reviewed must not define its own acceptance authority. QC authority remains independent where QC validation is required.

## 9. C3.6 — Entry / Exit Conditions

Each State must define controlled entry and exit conditions.

Entry Conditions establish what must be true before entering a State.

Exit Conditions establish what must be true before leaving a State.

C3 defines the lifecycle interface for Conditions. Detailed Gate Criteria belong to C4, Evidence/Truth requirements to C5, and Operational Logic to C6.

## 10. C3.7 — Exception, Rework & Recovery Paths

`REWORK` is not a Core State.

Typical rework path:

`VALIDATING → REWORK REQUIRED → IN_PROGRESS → SUBMITTED → VALIDATING`

The rework reason must be controlled and traceable.

Blocking is represented as Lifecycle Context:

`ACTIVE STATE + BLOCKED CONTEXT → RESUME → ALLOWED ACTIVE STATE`

The system must record why the object became blocked and what event/condition permits resumption.

An Agent must not unilaterally roll a Work Object backward from a validated or otherwise protected State. Reopen/rework must occur through an authorized Transition.

Exceptions must be explicit, controlled, traceable, associated with the affected Work Object and lifecycle point, and resolved through an authorized path. Free text must not replace controlled lifecycle records.

## 11. C3.8 — State / Event History & Auditability

Current State alone is insufficient.

The system must maintain traceable lifecycle history capable of reconstructing sequences such as:

`CREATED → ASSIGNED → READY → IN_PROGRESS → SUBMITTED → VALIDATING → IN_PROGRESS → SUBMITTED → VALIDATED`

Events are distinct from State. Examples include:

- Assignment Created
- Input Received
- Agent Started
- Evidence Added
- Submission Requested
- QC Started
- QC Completed
- Rework Requested
- Handoff Executed

Not every Event becomes a State.

State and Transition history must be protected from silent rewriting. Corrections must create new traceable records rather than silently deleting or rewriting historical records.

## 12. Formal Transition Record

Every committed Transition must produce a traceable Transition Record containing at minimum:

- Transition ID
- Work Object ID
- Previous State
- Transition
- New State
- Actor
- Authority
- Timestamp
- Work Object Version
- Condition / Reference
- Reason, where applicable

The Transition Record is the formal bridge between Current State and Audit History.

## 13. Ownership, Responsibility & Authority

Mandatory distinctions:

`STATE ≠ OWNER`
`OWNER ≠ RESPONSIBILITY`
`RESPONSIBILITY ≠ AUTHORITY`

Lifecycle ownership may change, but ownership changes must be authorized, traceable, version-consistent, and lifecycle-consistent.

Creator, Assigned Agent, Owner, QC Authority, and Auditor must not be conflated unless explicitly defined by a later operational specification.

## 14. Version + State Integrity

State transitions must be consistent with Work Object Version.

The system must prevent:

- Transition against stale version with silent overwrite
- State change without corresponding version consistency
- Ownership change conflicting with committed lifecycle version
- Silent modification of validated lifecycle records

State, Version, Ownership, and Transition History must remain mutually coherent.

## 15. Lifecycle Integrity Principles

1. Every Work Object has one authoritative Current State.
2. Current State is not a quality judgment.
3. Current State is not a QC Result.
4. Current State is not a Gate Result.
5. Current State is not an Event.
6. Every State Change occurs through an authorized Transition.
7. Every Transition has defined Conditions and Authority.
8. Invalid Transitions must not change Current State.
9. Every committed Transition produces a traceable Transition Record.
10. State History must be protected from silent rewriting.
11. Rework and Blocking must not create unnecessary State Explosion.
12. Version, Ownership, and State changes must remain consistent.
13. Concurrent conflicting State changes must be controlled.
14. QC must independently verify Lifecycle integrity.
15. Agent claims of completion must never be treated as system completion without required validation.
16. Agent confidence must never be treated as QC validation.

## 16. C3.9 — Lifecycle Integrity & QC Interface

QC must independently inspect lifecycle integrity and must not rely solely on the Current State reported by an Agent.

QC must be capable of verifying:

- Current State
- State History
- Transition History
- Transition Authority
- Transition Conditions
- Version Consistency
- Ownership Changes
- Lifecycle Context
- Handoff History
- Rejected Transition Attempts
- Lifecycle completeness
- Unauthorized transitions
- Invalid transitions
- Silent modifications
- Rework integrity
- Concurrency conflicts
- Final State legitimacy

The fundamental QC question is:

> Was the Current State actually produced by a valid lifecycle Transition?

not merely:

> Does the Work Object say `CLOSED`?

QC therefore validates lifecycle integrity independently of Agent claims.

## 17. Conceptual Lifecycle Model

```text
WORK OBJECT
     │
     ▼
CURRENT STATE
     │
     ▼
AUTHORIZED EVENT
     │
     ▼
TRANSITION
     │
     ├── CONDITION
     ├── AUTHORITY
     └── VERSION
     │
     ▼
NEW STATE
     │
     ▼
TRANSITION RECORD
     │
     ▼
STATE HISTORY
     │
     ▼
QC / AUDIT
```

## 18. Conceptual State Diagram

```text
CREATED
   ↓
ASSIGNED
   ↓
READY
   ↓
IN_PROGRESS
   ├── BLOCKED CONTEXT → RESUME → IN_PROGRESS
   ↓
SUBMITTED
   ↓
VALIDATING
   ├── PASS → VALIDATED → HANDOFF → HANDED_OFF → CLOSE → CLOSED
   └── FAIL → REWORK → IN_PROGRESS
```

This is architectural. Exact transition conditions, authorities, Gate requirements, evidence requirements, and logic are defined in later stages.

## 19. Stage Boundary Definition

C3 defines:

- Lifecycle
- State
- State Definitions
- Transition
- Transition Authority
- Entry / Exit Conditions
- Lifecycle Context
- Exception / Rework / Recovery
- State / Event History
- Transition Records
- Version-State integrity
- Lifecycle QC interface

C3 does not define:

- Detailed Gate Specifications → C4
- Evidence & Truth Specification → C5
- Operational Logic Specification → C6
- Handoff Protocol → C7

C3 provides interfaces to these later stages without duplicating their detailed specifications.

## 20. Final Architecture Decision

The C3 architecture is complete at architecture level when:

- Lifecycle model is defined.
- Core State model is defined.
- State/Context distinction is established.
- Transition model is defined.
- Transition authority is defined as an architectural requirement.
- Entry/Exit Conditions are defined as an architectural requirement.
- Rework/Blocking/Recovery are controlled without unnecessary State Explosion.
- State/Event History is defined.
- Formal Transition Record is defined.
- Version/Ownership/Concurrency integrity is addressed.
- QC has an independent lifecycle-audit interface.
- Boundaries to C4–C7 are explicit.

This document is an architecture specification, not an implementation specification.

Detailed schemas, enumerations, exact transition matrices, machine-readable rules, validation algorithms, and implementation mechanics must be developed only in the appropriate subsequent specification stages.

## 21. Governing Principle

The central lifecycle rule of TOS is:

`A Work Object does not become complete because an Agent says it is complete.`

Completion exists only through the controlled lifecycle defined by State, authorized Transition, required Conditions, validated records, and independent QC where applicable.

Therefore:

`AGENT CLAIM OF COMPLETION ≠ SYSTEM COMPLETION`

`CURRENT STATE ≠ PROOF OF CORRECTNESS`

`QC VALIDATION = INDEPENDENT LIFECYCLE INTEGRITY CHECK`

---

# END OF TOS STAGE C3
# State & Lifecycle Specification — Architecture Final v1.0
