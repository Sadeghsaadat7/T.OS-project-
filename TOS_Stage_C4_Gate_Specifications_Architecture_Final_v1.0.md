# TOS --- Stage C4

# Gate Specifications --- Architecture Final

**Document ID:** TOS-Stage-C4-Architecture\
**Version:** 1.0\
**Status:** FINAL --- READY FOR GITHUB UPLOAD\
**Stage:** C4 --- Gate Specifications\
**Predecessors:** Stage B, C1, C2, C3\
**Next Stage:** C5 --- Evidence & Truth Specification

## 1. Purpose

Stage C4 defines the architecture of Gate control within TOS: Gate
Definition, Eligibility, Evaluation Attempts, Evaluation Results,
Conflict Resolution, Authoritative Gate Decisions, Dispositions,
Overrides, Provenance, Historical Immutability, and interfaces to C3,
C5, C6, C7 and independent QC.

C4 is an architecture specification. It does not define Prompt
Engineering, implementation mechanics, database design, or
infrastructure-specific recovery mechanisms.

## 2. Core Definition

A Gate is a controlled decision boundary that evaluates a defined Work
Object Version against a defined Gate Definition Version and produces a
traceable Gate Decision and Disposition under explicit authority.

Canonical chain:

``` text
WORK OBJECT → VERSION BINDING → GATE DEFINITION
→ GATE DECISION CONTEXT → ELIGIBILITY
→ EVALUATION ATTEMPT → EVALUATION RESULT
→ RESOLUTION (IF REQUIRED) → AUTHORITATIVE DECISION
→ GATE DISPOSITION → C3 LIFECYCLE / C7 HANDOFF
```

Evidence and Logic are referenced inputs owned by later stages. QC
remains an independent verification function.

## 3. Fundamental Distinctions

``` text
GATE ≠ GATE ELIGIBILITY
ELIGIBILITY ≠ EVALUATION
EVALUATION ATTEMPT ≠ EVALUATION RESULT
EVALUATION RESULT ≠ GATE DECISION
GATE DECISION ≠ GATE DISPOSITION
GATE DECISION ≠ LIFECYCLE TRANSITION
GATE DECISION ≠ HANDOFF
GATE DECISION ≠ QC RESULT
```

Operational conditions must not be silently converted into Gate
Decisions:

``` text
UNKNOWN ≠ FAIL
INCOMPLETE ≠ FAIL
TIMEOUT ≠ FAIL
CONFLICT ≠ FAIL
CRASH ≠ FAIL
RETRY ≠ FAIL
DUPLICATE ≠ FAIL
```

## 4. Gate Definition & Decision Context

C4 owns the Gate Definition and its versioned decision-policy boundary.

A Gate Decision Context is uniquely determined by:

``` text
Work Object ID
+ Work Object Version
+ Gate Definition ID
+ Gate Definition Version
```

When applicable it also binds exact Evidence and Logic
references/versions.

The context is immutable after establishment. "Current" or "Latest"
references must not silently replace the versions bound to the decision.

## 5. Eligibility

Eligibility determines whether a Gate evaluation may be initiated. It is
not itself a Gate Decision.

A Gate may be eligible, not yet eligible, blocked, invalid for the
supplied context, or operationally unable to evaluate. These conditions
remain distinct from PASS/FAIL unless the Gate Definition explicitly
defines otherwise.

## 6. Evaluation Control

An Evaluation Attempt records an actual attempt and may be:

``` text
STARTED / COMPLETED / INTERRUPTED / TIMEOUT / FAILED / CANCELLED
```

These are operational statuses, not automatically Gate Decisions.

An Evaluation Result records the substantive result produced by an
attempt.

Canonical chain:

``` text
EVALUATION ATTEMPT → EVALUATION RESULT
→ GATE EVALUATION / RESOLUTION → AUTHORITATIVE DECISION
```

A failed or interrupted attempt does not become FAIL merely because it
did not complete.

## 7. Concurrent Evaluation & Conflict

Multiple immutable Evaluation Records may exist for one Decision
Context.

Invariant:

> One Gate Decision Context may have at most one simultaneously
> authoritative Gate Decision.

Last-write-wins, first-write-wins, and majority-vote behavior must not
be assumed unless explicitly defined by the Gate Definition.

If valid evaluations conflict:

``` text
Resolution Status = CONFLICT
Decision = NULL
```

until the defined Conflict Policy produces an authoritative resolution.

Conflict records preserve the conflicting evaluations, context/version
binding, detection, policy, authority, actions, and final resolution.

Resolution must be authorized, traceable, version-bound, and bounded to
prevent uncontrolled resolution loops.

## 8. Authoritative Decision & Durability

A Gate Decision is the authoritative outcome for a Gate Decision
Context. It is not simply the latest Evaluation Result.

The invariant is:

``` text
ONE DECISION CONTEXT
→ MULTIPLE IMMUTABLE EVALUATION RECORDS MAY EXIST
→ ZERO OR ONE SIMULTANEOUS AUTHORITATIVE DECISION
```

A calculated decision is not automatically a durably committed decision.

``` text
CALCULATED DECISION ≠ DURABLY COMMITTED DECISION
```

If a crash occurs before durable recording, no committed authoritative
decision exists. If the decision was durably recorded but
acknowledgement was lost, recovery must discover the existing decision
rather than create a duplicate.

Persistence, transactionality, storage, and general recovery remain
cross-cutting concerns.

## 9. Gate Disposition

Disposition describes the controlled operational consequence of an
authoritative Gate Decision.

Examples:

``` text
PROCEED / REWORK / HOLD / ESCALATE / BLOCK
```

Disposition is not the Decision.

``` text
PASS ≠ PROCEED
FAIL ≠ REWORK
```

unless the applicable Gate Definition explicitly establishes that
relationship.

## 10. Lifecycle and Handoff Boundaries

``` text
GATE PASS ≠ C3 STATE TRANSITION
GATE PASS ≠ C7 HANDOFF SUCCESS
```

C3 owns State, Transition, Transition Authority, lifecycle conditions
and lifecycle history.

C7 owns Handoff Protocol and Handoff execution.

C4 provides Gate Decision/Disposition information; it does not create a
parallel lifecycle state machine or execute handoff.

## 11. Override Control

An Override is not an edit to a historical decision.

``` text
ORIGINAL DECISION
→ OVERRIDE REQUEST
→ AUTHORITY VERIFICATION
→ AUTHORIZED OVERRIDE
→ NEW DECISION RECORD
```

The original decision remains immutable. The new decision preserves the
same Decision Context and references the original decision.

Example:

``` text
D1 = PASS
D2 = Authorized Override → FAIL
```

D1 remains history; D2 becomes authoritative only through the controlled
override process.

## 12. Historical Immutability & Provenance

Gate history is append-only from an architectural perspective.

It must preserve:

-   Decision Context;
-   Work Object and Gate Definition versions;
-   Evaluation Attempts;
-   Evaluation Results;
-   conflicts and resolution trace;
-   decisions;
-   overrides;
-   dispositions;
-   operational failures;
-   recovery events.

Recovery must preserve history.

If:

``` text
EA-001 → CRASH
EA-002 → RETRY
```

both events remain visible. The retry must not erase the crash.

Every authoritative decision must be reconstructable to its Work Object
Version, Gate Definition Version, evaluation chain, relevant
Evidence/Logic versions, authority, timestamp, conflict/resolution
chain, override chain, and Disposition.

## 13. Evidence & Logic Boundaries

C5 owns Evidence & Truth. C6 owns Operational Logic.

C4 may require exact Evidence and Logic references/versions but does not
create, redefine, or own those systems.

If relevant evidence changes after PASS:

``` text
OLD DECISION → remains historical
NEW EVIDENCE → may require re-evaluation
RE-EVALUATION → new attempt/result/decision chain
```

Historical Truth, Current Operational Applicability, Lifecycle Success,
and Handoff Success remain distinct.

## 14. QC Boundary

QC remains independent.

The following may coexist:

``` text
Gate Decision = PASS
QC Outcome = FAIL
```

QC must not mutate the historical Gate Decision. It may block downstream
lifecycle or handoff actions according to C3/C7 contracts.

Therefore:

``` text
QC ≠ Hidden Second Gate
```

## 15. Operational Failure, Concurrency, Idempotency & Recovery

C4 distinguishes Gate semantics from operational failure.

Crashes, timeouts, interruptions, dependency failures, duplicate
requests, acknowledgement loss, and concurrency conflicts are not
automatically Gate FAIL decisions.

C4 requires protection against duplicate authoritative decisions,
stale-version decisions, conflicting simultaneous evaluations,
concurrent overrides, and concurrent re-evaluation.

General concurrency mechanisms, idempotency infrastructure, transaction
engines, storage, and recovery engines are cross-cutting and outside C4
ownership.

## 16. Version Integrity

Every decision must be bound to exact versions:

``` text
Work Object ID
Work Object Version
Gate Definition ID
Gate Definition Version
```

and, where applicable:

``` text
Evidence Reference / Version
Logic Reference / Version
```

Vague references such as CURRENT, LATEST, or DEFAULT are prohibited when
they make historical reconstruction ambiguous.

Post-decision changes do not rewrite history. Where re-evaluation is
required, it creates a new evaluation/decision chain.

## 17. Scope Boundary

### C4 Owns

-   Gate Definition and Version
-   Gate Eligibility
-   Gate Decision Context
-   Evaluation Control
-   Evaluation Attempt
-   Evaluation Result
-   Concurrent Evaluation Control
-   Conflict Control and Resolution
-   Authoritative Gate Decision
-   Gate Disposition
-   Override Control
-   Decision Provenance
-   Gate History

### C4 Does Not Own

-   Agent Operational Contract → C1
-   Work Object Identity / Version → C2
-   Lifecycle / State / Transition → C3
-   Evidence & Truth → C5
-   Operational Logic → C6
-   Handoff Protocol → C7
-   General Workflow
-   Database / Storage Engine
-   General Transaction Engine
-   General Recovery Engine
-   Infrastructure-level concurrency implementation

## 18. Canonical Gate Flow

``` text
WORK OBJECT
↓
VERSION BINDING
↓
GATE DEFINITION
↓
GATE DECISION CONTEXT
↓
ELIGIBILITY
↓
EVALUATION ATTEMPT
↓
EVALUATION RESULT
↓
CONFLICT?
├─ NO → AUTHORITATIVE DECISION
└─ YES → RESOLUTION → AUTHORITATIVE DECISION
↓
GATE DISPOSITION
↓
C3 LIFECYCLE / C7 HANDOFF
```

## 19. Audit Reconstruction Requirement

An auditor must be able to reconstruct:

1.  Work Object and Version;
2.  Gate Definition and Version;
3.  Evidence/Logic references used;
4.  eligibility;
5.  all evaluation attempts;
6.  completed, failed, timed-out, or crashed attempts;
7.  evaluation results;
8.  conflicts;
9.  conflict resolution;
10. decision authority and timestamp;
11. override chain;
12. authoritative decision at each point;
13. disposition;
14. subsequent QC result;
15. subsequent C3 lifecycle or C7 handoff outcome.

The system must preserve not only the final decision, but why it existed
and how it was reached.

## 20. Architectural Integrity Principles

1.  Gate Decision is explicit.
2.  Attempt and Result remain separate.
3.  Decision Context is version-bound.
4.  Historical records remain immutable.
5.  Conflict never silently becomes PASS or FAIL.
6.  Operational failure never silently becomes Gate FAIL.
7.  One Context has at most one simultaneous authoritative Decision.
8.  Override creates a new record.
9.  QC does not mutate historical Gate Decisions.
10. Gate PASS does not directly mutate C3 State.
11. Gate PASS does not imply Handoff success.
12. Evidence and Logic ownership remain in C5/C6.
13. Recovery preserves history.
14. Version ambiguity is prohibited.
15. C4 does not become a general workflow or recovery engine.
16. Authoritative decisions are reconstructable.

## 21. Architectural Completion Criteria

C4 is complete when:

-   Gate Definition/versioning is explicit;
-   Decision Context is unique and immutable;
-   Eligibility is separated from Evaluation;
-   Attempt is separated from Result;
-   concurrent evaluations are controlled;
-   conflicts are first-class and resolvable;
-   the one-authoritative-decision invariant is preserved;
-   decision durability is distinct from calculation;
-   Disposition is distinct from Decision;
-   Override is append-only and traceable;
-   historical records are immutable;
-   Evidence/Logic boundaries remain external;
-   QC remains independent;
-   Lifecycle/Handoff boundaries remain external;
-   operational failure is not silently converted to FAIL;
-   recovery preserves history;
-   version integrity is explicit;
-   post-decision change and re-evaluation are controlled;
-   audit reconstruction is possible;
-   cross-cutting reliability remains outside C4;
-   no hidden workflow or state machine has been introduced.

## 22. Final Architectural Statement

C4 establishes the Gate as a controlled, version-bound, auditable
decision boundary within TOS.

``` text
WORK OBJECT VERSION
+
GATE DEFINITION VERSION
↓
GATE DECISION CONTEXT
↓
CONTROLLED EVALUATION
↓
AUTHORITATIVE DECISION
↓
DISPOSITION
```

while preserving:

``` text
C4 GATE
≠ C3 STATE
≠ C7 HANDOFF
≠ QC RESULT
≠ C5 EVIDENCE / TRUTH
≠ C6 OPERATIONAL LOGIC
```

C4 therefore controls whether a defined decision boundary has been
authoritatively satisfied, while preserving the authority of the
surrounding TOS architecture.

# FINAL STATUS

**TOS Stage C4 --- Gate Specifications --- Architecture Final v1.0**

**STATUS: FINAL --- READY FOR GITHUB UPLOAD**

The C4 architecture has completed design, Stress Tests I--III, C1/C2/C3
reconciliation, boundary review, concurrent/conflicting evaluation
review, targeted six-gap reconciliation, final adversarial combined
checks, and the final document-level consistency review.

No unresolved architecture-level contradiction was identified.

Next authorized action:

``` text
C4 FINAL DOCUMENT
↓
USER UPLOADS TO GITHUB
↓
USER CONFIRMS UPLOAD
↓
ONLY THEN
↓
C5 — EVIDENCE & TRUTH SPECIFICATION
```

No Prompt Engineering or implementation detail is included.
