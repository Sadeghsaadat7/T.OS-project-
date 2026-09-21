# TOS — Stage C5
# Evidence & Truth Specification
## Final Architecture Specification — v1.0

**Project:** TOS — Trading Operations System  
**Stage:** C5 — Evidence & Truth Specification  
**Document Status:** FINAL / FROZEN  
**Version:** v1.0  
**Previous Stage:** C4 — Gate Specifications — FROZEN  
**Next Stage:** C6 — Logic Operational Specification

---

## 1. Document Purpose

This document defines the architecture governing the evidentiary control layer of TOS.

C5 establishes the rules and structures required to ensure that Evidence used throughout TOS is:

- identifiable,
- versioned,
- contextualized,
- traceable,
- validated,
- qualified,
- temporally reconstructable,
- auditable,
- and correctly related to the TOS artifacts that consume it.

This document is an architectural specification.

It does not define Prompt Engineering, implementation details, technology selection, or trading strategy.

---

## 2. Architectural Scope

C5 governs:

- Evidence Identity
- Evidence Versioning
- Evidence Context
- Provenance
- Lineage
- Validation
- Qualification
- Temporal Evidentiary State
- Evidence Binding
- Evidence Conflict
- Historical Reconstruction
- Impact Trace
- Auditability
- Evidence Integrity and Recovery

C5 does not govern:

- Work Object definition
- Work Object lifecycle
- Gate definition
- Evaluation decisions
- Gate Decisions
- Operational Logic
- Handoff
- Trading Decisions

---

## 3. Core Architectural Principle

> **C5 controls the evidentiary substrate; it does not control decisions.**

C5 controls how Evidence is represented, versioned, traced, qualified, validated, and related.

C5 does not determine:

- what a Gate should decide,
- whether a Work Object should transition,
- whether a Logic rule is correct,
- or whether a trading action should be taken.

---

## 4. Evidence Definition

For TOS:

> **Evidence is an identifiable, traceable, contextualized, and auditable representation of an observation, measurement, statement, extraction, record, event, or other informational artifact that can be used to support or evaluate a claim, analysis, or operational process.**

Examples include:

- market observations,
- OHLCV records,
- timestamped market events,
- transcript segments,
- recorded statements,
- screenshots,
- historical observations,
- test results,
- measurements,
- independently produced observations,
- validated external data.

---

## 5. What Is Not Evidence

The following are not automatically Evidence:

- opinion,
- unsupported conclusion,
- trading decision,
- instruction,
- interpretation,
- hypothesis,
- agent memory,
- undocumented assumption.

A hypothesis may be represented elsewhere as a claim or analytical artifact, but its existence does not automatically make it Evidence.

---

## 6. Source ≠ Evidence

A Source and Evidence are distinct.

Conceptually:

```text
Source
  ↓
Specific location / timestamp / context
  ↓
Extraction or observation
  ↓
Evidence
```

For example, a YouTube video is a Source.

A precisely identified timestamped statement extracted from that video may become Evidence when registered with appropriate provenance and context.

This distinction is mandatory for traceability.

---

## 7. Evidence ≠ Truth

C5 does not define absolute or universal Truth.

Evidence may support, contradict, qualify, or contextualize a claim without establishing universal truth.

Therefore:

```text
Evidence ≠ Truth
Evidence Status ≠ Universal Truth Status
```

The term **“Truth”** in the name of Stage C5 refers to evidentiary qualification and truth-status handling. It does **not** mean that C5 owns, determines, or declares absolute or universal truth.

C5 is therefore not a Truth Engine.

---

## 8. C5 Architectural Boundary

### C5 owns

- Evidence Identity
- Evidence Version
- Evidence Context
- Provenance
- Lineage
- Validation Records
- Qualification Records
- Temporal Evidentiary State
- Evidence Binding
- Conflict Representation
- Impact Trace
- Historical Evidence Reconstruction
- Evidentiary Auditability

### C5 does not own

- Work Object definition
- Work Object lifecycle
- Gate definition
- Evaluation decision
- Gate Decision
- Operational Logic
- Handoff
- Trading decision

---

## 9. C5 Object Model

C5 contains eight principal architectural elements:

```text
Evidence
   │
   ├── Evidence Version
   │      └── Evidence Context
   │
   ├── Provenance Record
   │
   ├── Validation Record
   │
   ├── Qualification Record
   │
   ├── Evidence Binding
   │
   ├── Evidence Conflict
   │
   └── Lineage Record
```

These elements exist because each addresses a distinct requirement for evidence integrity, traceability, historical reconstruction, or auditability.

---

## 10. Evidence

Evidence provides the stable identity of an evidentiary item.

Example:

```text
Evidence ID = E-100
```

Evidence identity remains stable across versions.

Identity must not silently change.

---

## 11. Evidence Version

An Evidence item may have multiple immutable versions.

Example:

```text
E-100:v1
E-100:v2
E-100:v3
```

A Version represents a specific immutable representation of Evidence.

### Mandatory Rule

> **Evidence Versions are immutable.**

A changed representation must create a new Version rather than silently modifying an existing Version.

---

## 12. Historical Version Pinning

Historical usage of Evidence must identify the exact Evidence Version used.

For example:

```text
E-100
```

is insufficient for historical reconstruction.

The historical reference must resolve to:

```text
E-100:v3
```

or an equivalent immutable version identifier.

Future versions must never silently rewrite the evidentiary basis of a historical Evaluation.

---

## 13. Evidence Context

Evidence Context defines the circumstances necessary to interpret the Evidence correctly.

Examples include:

- Instrument
- Market
- Timestamp
- Timeframe
- Session
- Observation context
- Source context
- Relevant environmental conditions

Example:

```text
Evidence:
EURUSD price event

Context:
Instrument = EURUSD
Timestamp = 10:30 UTC
Timeframe = 5m
Session = London
```

Evidence Context is part of the Evidence Version.

It is not currently a standalone first-class object because there is no demonstrated architectural requirement for that additional abstraction.

### Context ≠ Interpretation

For example:

```text
Timeframe = 5m
```

is Context.

Whereas:

```text
This event is a liquidity sweep.
```

is Interpretation and belongs outside C5 Evidence Context.

---

## 14. Provenance

Provenance answers:

> **Where did this Evidence come from and how was it produced?**

A Provenance Record may represent the trace:

```text
Source
  ↓
Acquisition
  ↓
Extraction
  ↓
Normalization / Transformation
  ↓
Evidence
```

C5 owns the traceability of this chain.

C5 does not own the business or transformation logic used to generate the Evidence.

---

## 15. Lineage

Lineage answers:

> **What prior Evidence or artifacts was this Evidence derived from?**

Example:

```text
E-101:v1
   ↓
derived from
   ↓
E-205:v1
```

Provenance and Lineage are distinct:

- **Provenance** describes origin and production trace.
- **Lineage** describes derivation relationships between Evidence or related artifacts.

Neither should be collapsed into the other.

---

## 16. Validation

Validation represents the recorded integrity/validation status of Evidence.

C5 maintains Validation Records rather than treating validation as a single permanent mutable flag.

Possible validation outcomes include:

- Unknown
- Valid / Passed
- Invalid / Failed
- Inconclusive

Validation is temporal.

Evidence that was previously validated may later be invalidated due to a newly discovered integrity problem.

Historical validation records remain preserved.

---

## 17. Qualification

Qualification represents the evidentiary or epistemic character of Evidence.

Possible qualification states include:

- Unverified
- Source-Supported
- Observed
- Derived
- Contradicted

Qualification does not mean:

> “This is universally true.”

It describes the evidentiary standing of the Evidence within its known context.

---

## 18. Validation ≠ Qualification

These concepts must remain separate.

### Validation asks:

> Is the Evidence representation or integrity sufficiently valid according to the applicable validation process?

### Qualification asks:

> What evidentiary status or character does this Evidence currently possess?

Therefore:

```text
Valid
```

does not automatically mean:

```text
Source-Supported
```

and:

```text
Contradicted
```

does not automatically mean:

```text
Invalid
```

---

## 19. Applicability / Freshness

Evidence has an applicability/freshness dimension separate from integrity.

Possible states include:

- Current
- Stale
- Context-Limited
- Unknown

This prevents:

```text
Stale ≠ Invalid
```

A historical observation may remain valid Evidence while no longer being current for a particular operational context.

---

## 20. Lifecycle

Evidence lifecycle is independent from validation, qualification, and applicability.

Initial lifecycle model:

```text
Candidate
   ↓
Registered
   ↓
Available
   ↓
Superseded
   ↓
Archived
```

Important distinctions:

```text
Superseded ≠ Invalid
Archived ≠ Invalid
Used ≠ Lifecycle State
Validated ≠ Lifecycle State
```

Lifecycle cannot be used as a substitute for another status dimension.

---

## 21. Conflict State

Evidence may conflict with other Evidence.

C5 therefore represents conflicts explicitly.

Possible conflict states:

```text
None
Detected
Unresolved
Resolved
```

A Conflict may reference multiple Evidence Versions.

Example:

```text
Conflict C-01

E-101:v1
     ↕
Direct Contradiction
     ↕
E-102:v1
```

### Mandatory Rule

> **C5 represents conflicts; it does not autonomously resolve them.**

A contradiction does not automatically invalidate either participant.

---

## 22. Evidence Binding

Evidence Binding represents a version-specific relationship between Evidence and another TOS artifact.

Examples:

```text
Evidence Version
      ↓
Binding
      ↓
Work Object Version
```

or:

```text
Evidence Version
      ↓
Binding
      ↓
Evaluation
```

Binding must preserve sufficient information for historical reconstruction.

Evidence Binding does not transfer ownership of the target object to C5.

---

## 23. C5 ↔ C2 — Work Object

C2 owns the Work Object.

C3 owns its lifecycle and state transitions.

C5 may establish:

```text
Evidence Version
      ↓
Binding
      ↓
Work Object Version
```

C5 does not:

- modify the Work Object,
- change its lifecycle,
- transition its state,
- determine its disposition.

If Evidence later becomes invalid, C5 may identify affected Work Objects through Impact Trace.

The downstream owner determines what happens next.

---

## 24. C5 ↔ C3 — State & Lifecycle

C3 owns Work Object state and lifecycle.

A change in Evidence status must not directly become a C3 state transition.

Conceptually:

```text
Evidence Status Change
        ↓
Impact Trace
        ↓
Affected Work Object identified
        ↓
C3 / responsible downstream policy evaluates consequences
```

C5 provides evidentiary information; it does not command lifecycle transitions.

---

## 25. C5 ↔ C4 — Gate / Evaluation

C4 owns:

- Gate
- Evaluation
- Gate Decision
- Disposition

C5 provides the evidentiary context required by C4.

An Evaluation must be able to reconstruct, as applicable:

```text
Work Object ID + Version
        +
Gate Definition ID + Version
        +
Evaluation Context
        +
Evidence ID + Version
        +
Applicable Evidentiary State
```

C5 does not determine:

```text
PASS
FAIL
```

or any other Gate Decision.

### C4 Frozen Integrity

C5 introduces no change to C4 ownership or Gate architecture.

C4 remains **FROZEN**.

---

## 26. C5 ↔ C6 — Operational Logic

C6 owns Operational Logic.

C6 may consume Evidence and its context.

C5 may provide:

- Evidence
- Version
- Context
- Provenance
- Validation
- Qualification
- Lineage
- Conflict information

C5 does not determine whether a Logic rule is correct.

---

## 27. Temporal Evidentiary State

Current status alone is insufficient for a reliable historical system.

Validation and Qualification must preserve historical state.

Example:

```text
2026-09-20
E-100:v1
Validation = Valid
```

Later:

```text
2026-09-25
E-100:v1
Validation = Invalid
```

Both states remain historically meaningful.

Therefore:

> **Evidentiary state must be reconstructable as-of the relevant historical point in time.**

---

## 28. Evidence Version Immutability vs Temporal Status

These two concepts must never be confused.

```text
Evidence Version
        ↓
Immutable representation
```

while:

```text
Validation / Qualification / Applicability
        ↓
Temporal records describing the state of that Version
```

Therefore a change in evidentiary status does not mutate the Evidence Version itself.

This distinction is fundamental to C5.

---

## 29. Temporal Events

Where semantically necessary, C5 distinguishes relevant timestamps such as:

- Observation / Capture Time
- Registration Time
- Validation Time
- Invalidation Time
- Supersession Time

Only timestamps carrying distinct semantic meaning should be represented.

C5 intentionally avoids unnecessary timestamp proliferation.

---

## 30. Historical Reconstruction

C5 must support reconstruction of the evidentiary state applicable to a historical Evaluation.

Conceptually:

```text
Historical Evaluation
       │
       ├── Evidence ID
       ├── Evidence Version
       ├── Binding
       ├── Context
       ├── Provenance
       ├── Validation State @ Time T
       ├── Qualification State @ Time T
       └── Lineage
```

This ensures:

> **Current Evidence State cannot silently rewrite Historical Evaluation Context.**

---

## 31. Impact Trace

When Evidence becomes invalid, superseded, or otherwise materially changes its evidentiary status, C5 must be able to identify potentially affected downstream usages.

Example:

```text
E-100:v1
   ↓
Status Change
   ↓
Impact Trace
   ├── Evaluation A
   ├── Work Object B
   └── Analysis C
```

Impact Trace is informational.

It does not mean:

```text
C5 → automatically rerun Gate
```

or:

```text
C5 → automatically change Work Object
```

The responsible downstream owner or policy determines whether further action is required.

---

## 32. Failure and Recovery

C5 must preserve evidentiary integrity under failure.

### Incomplete Registration

An incomplete registration cannot become:

```text
Available Evidence
```

It must remain auditable as incomplete or failed registration where appropriate.

### Validation Failure

Validation failure does not justify silent deletion.

### Source Unavailable

A source becoming unavailable does not automatically invalidate previously captured Evidence if the Evidence and its provenance remain sufficiently preserved.

### Broken Provenance

Broken or incomplete provenance must be represented explicitly rather than silently hidden.

### Recovery

Recovery must not rewrite historical Evidence or erase the historical record of failure.

---

## 33. Concurrency

C5 must support concurrent Evidence production.

When multiple Agents create Evidence concurrently:

- Identity allocation must remain controlled.
- Version allocation must be concurrency-safe.
- Historical Versions must not collide.
- No Agent may silently overwrite another Agent's Evidence Version.

Identity resolution and deduplication remain separate concerns.

---

## 34. Duplicate / Identity Resolution

Two Agents may independently observe or record the same real-world event.

C5 must therefore be capable of representing:

```text
Potential Duplicate
```

or:

```text
Identity Candidate
```

without automatically asserting that two records are definitively the same real-world event.

### Mandatory Distinction

```text
Deduplication ≠ Identity Resolution
```

Semantic identity decisions must not be hidden inside a simplistic duplicate-removal mechanism.

---

## 35. Core C5 Invariants

The following invariants are mandatory:

1. **Evidence Identity is stable.**
2. **Evidence Versions are immutable.**
3. **Historical Evidence references are version-pinned.**
4. **Evidence cannot silently mutate after use.**
5. **Superseded does not mean Invalid.**
6. **Source Unavailable does not automatically mean Evidence Invalid.**
7. **Conflict does not automatically imply Invalidity.**
8. **C5 does not own Gate Decisions.**
9. **C5 does not own Work Object lifecycle.**
10. **C5 does not own Operational Logic.**
11. **Historical evidentiary state must remain reconstructable.**
12. **Evidence provenance must be auditable.**
13. **Incomplete registration cannot become Available Evidence.**
14. **Version allocation must be concurrency-safe.**
15. **Current Evidence State must not silently rewrite historical usage.**
16. **Impact Trace must not become automatic downstream decision-making.**
17. **C5 must not function as a universal Truth Engine or Knowledge Store.**
18. **Evidence Version immutability must remain distinct from temporal status records.**

---

## 36. C5 Architectural Flow

The complete conceptual flow is:

```text
SOURCE
   │
   ▼
ACQUISITION / EXTRACTION
   │
   ▼
EVIDENCE CANDIDATE
   │
   ▼
REGISTRATION
   │
   ├── Identity
   ├── Context
   └── Provenance
   │
   ▼
EVIDENCE VERSION
   │
   ├── Validation
   ├── Qualification
   ├── Applicability
   ├── Lineage
   └── Conflict
   │
   ▼
AVAILABLE EVIDENCE
   │
   ▼
EVIDENCE BINDING
   │
   ├──────────────► WORK OBJECT
   │
   └──────────────► EVALUATION
                          │
                          ▼
                     C4 DECISION
```

Status changes later in the Evidence lifecycle follow:

```text
EVIDENCE STATUS CHANGE
          │
          ▼
     IMPACT TRACE
          │
          ▼
IDENTIFY AFFECTED USAGES
          │
          ▼
DOWNSTREAM OWNER / POLICY
          │
          ▼
DECIDE WHETHER ACTION IS REQUIRED
```

---

## 37. C5 Boundary Map

```text
┌─────────────────────────────────────────────┐
│                    C1                       │
│          Agent Operational Contracts        │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────┐
│                    C2                       │
│                Work Object                  │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────┐
│                    C3                       │
│          State & Lifecycle Control          │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
        ┌────────────────────────┐
        │           C5           │
        │  Evidence Control Plane│
        │                        │
        │ Identity              │
        │ Version               │
        │ Context               │
        │ Provenance            │
        │ Lineage               │
        │ Validation            │
        │ Qualification         │
        │ Temporal State        │
        │ Binding               │
        │ Conflict              │
        │ Impact Trace          │
        └───────────┬────────────┘
                    │
             ┌──────┴──────┐
             ▼             ▼
        ┌─────────┐   ┌─────────┐
        │   C4    │   │   C6    │
        │  Gate   │   │  Logic  │
        └─────────┘   └─────────┘
```

C5 is therefore a controlled evidentiary layer rather than a command center.

---

## 38. Architectural Non-Goals

C5 explicitly does not attempt to become:

### Truth Engine

It does not declare universal truth.

### Decision Engine

It does not decide Gate outcomes or trades.

### Work Object Manager

It does not own Work Object state.

### Logic Engine

It does not own operational reasoning.

### Universal Knowledge Base

It does not contain every piece of TOS knowledge merely because that knowledge exists somewhere in the system.

### Data Warehouse

Its architecture does not prescribe a universal physical data-storage solution.

---

## 39. Implementation Independence

C5 defines architectural contracts and invariants rather than prescribing a specific technology stack.

Possible future implementations may use different combinations of:

- databases,
- object storage,
- document stores,
- event systems,
- external data sources,
- archival systems.

The architecture remains valid as long as its ownership boundaries, contracts, and invariants are preserved.

---

## 40. Final Architectural Acceptance Criteria

The C5 architecture is considered complete when the implemented system can demonstrate:

1. Stable Evidence Identity.
2. Immutable Evidence Versioning.
3. Version-pinned historical references.
4. Auditable Provenance.
5. Explicit Lineage.
6. Temporal Validation.
7. Temporal Qualification.
8. Independent Applicability / Freshness state.
9. Explicit Conflict representation.
10. Auditable Evidence Binding.
11. Historical reconstruction.
12. Impact Trace.
13. Failure recovery without silent historical mutation.
14. Concurrency-safe Version allocation.
15. Clear C2 / C3 / C4 / C6 boundaries.
16. No automatic downstream decision-making by C5.
17. No Truth-God or Universal-Database drift.
18. Explicit separation between immutable Evidence Versions and temporal status records.

---

## 41. Final Architectural Statement

> **C5 — Evidence & Truth Specification establishes the Evidence Control Plane of TOS. It guarantees that evidentiary information used throughout the system remains identifiable, versioned, contextualized, traceable, qualified, temporally reconstructable, auditable, and structurally related to the work and evaluations that consume it. C5 preserves the integrity and history of Evidence without assuming authority over absolute Truth, Operational Logic, Gate Decisions, Work Object Lifecycle, Handoff, or Trading Decisions.**

---

## 42. Stage Status

**C5 — Evidence & Truth Specification**

**FINAL / FROZEN / APPROVED FOR GITHUB UPLOAD**

This document supersedes the C5 draft architecture discussions and represents the finalized architectural specification for Stage C5.

Per the permanent TOS stage rule:

```text
CURRENT STAGE
      ↓
FINALIZE DOCUMENT
      ↓
GITHUB UPLOAD
      ↓
USER CONFIRMS UPLOAD
      ↓
NEXT STAGE
```

No C6 work begins until the user independently uploads this C5 Final document to the TOS GitHub repository and confirms the upload.
