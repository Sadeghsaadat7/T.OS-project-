# TOS — Trading Operations System

## Stage C6 — Logic Operational Specification

**Document Status:** FINAL / FROZEN  
**Architecture Stage:** Stage C6  
**Version:** v1.0  
**Authority:** TOS Engineer / Human Authority / System Owner

---

# 1. Purpose

Stage C6 defines the operational architecture for how TOS represents, analyzes, validates, applies, investigates, evolves, and preserves **Logic** and related reasoning processes.

C6 exists to ensure that TOS does not merely identify market patterns or observations, but can determine:

- What was observed?
- Why may it have occurred?
- How does the mechanism operate?
- Under what conditions does the Logic apply?
- What supports the Logic?
- What contradicts it?
- What remains unknown?
- How was the conclusion known at that point in time?
- How should the Logic evolve when new evidence appears?

C6 must preserve reasoning integrity while preventing unsupported interpretations from becoming authoritative knowledge.

---

# 2. Architectural Position

C6 operates between the evidence/truth layer and downstream operational decision processes.

Conceptually:

Evidence & Truth (C5)
→ Observation / Investigation
→ Logic / Logic Point
→ Scenario / Interpretation
→ Logic Validation
→ C6 Result
→ Gate Evaluation (C4) / Handoff (C7) / Other Consumers

C6 does not replace C1, C2, C3, C4, or C5.

---

# 3. C6 Core Responsibilities

C6 owns the semantic and operational treatment of:

1. Logic
2. Logic Point
3. Investigation
4. Open Float Study
5. Scenario
6. Logic Validation
7. Market Event Analysis
8. Controlled Logic Evolution

These form the core C6 reasoning domain.

---

# 4. Explicitly Outside C6

### C1 — Agent Operational Contract
Agent authority, permissions, assignment boundaries, and behavioral constraints.

### C2 — Work Object
Generic Work Object identity, structure, assignment, versioning, traceability, and audit framework.

### C3 — State & Lifecycle
Generic state machine, transitions, transition authority, lifecycle history, recovery, and rework mechanics.

### C4 — Gates
Gate eligibility, evaluation, authoritative Gate Decision, override, disposition, and gate history.

### C5 — Evidence & Truth
Evidence identity, provenance, lineage, validation, qualification, applicability, conflict, and historical reconstruction.

### C7 — Handoff
Transfer, routing, acknowledgement, retry, delivery, and receiving-side handoff mechanics.

### Shared/Future Infrastructure
Generic Time Infrastructure, Knowledge Repository infrastructure, and future Performance Engine remain outside exclusive C6 ownership.

---

# 5. Fundamental Architectural Boundaries

The following distinctions are mandatory C6 invariants.

1. Logic ≠ Evidence
2. Logic ≠ Gate Decision
3. Logic ≠ Lifecycle Engine
4. Logic ≠ Handoff
5. Observation ≠ Perspective
6. Perspective ≠ Fact
7. Hypothesis ≠ Knowledge
8. Pattern ≠ Validated Logic
9. Valid Evidence ≠ Validated Logic
10. Convergence ≠ Guaranteed Outcome
11. Divergence ≠ Decision
12. PD Array ≠ Automatically Valid Setup
13. Logbook ≠ Truth Engine
14. Narrative ≠ Evidence
15. Agent Confidence ≠ Logic Validity
16. UNKNOWN ≠ FALSE
17. Failure ≠ Automatic Disproof
18. Retirement ≠ Deletion
19. Outcome Does Not Rewrite the Past
20. Self-Improvement ≠ Self-Rewrite
21. No Silent Logic Mutation
22. No Automatic Promotion
23. No Post-Hoc Contamination
24. Source Teaching ≠ Agent Inference

These invariants are architectural constraints, not optional guidelines.

---

# 6. Logic

## 6.1 Definition

For C6 purposes, Logic is an operationally inspectable relationship or rule that explains how observations, conditions, elements, or events relate and what interpretation or conclusion can be derived under defined circumstances.

A Logic must be interpretable through:

- What?
- Why?
- How?
- Under what conditions?
- Based on what?
- When does it apply?
- When does it not apply?
- What supports it?
- What contradicts it?
- What remains unknown?

Logic must be traceable to its supporting reasoning and evidence.

---

# 7. Logic Epistemic Status

C6 must preserve the status of knowledge rather than collapsing all reasoning into one category.

Relevant statuses include:

- FACT
- SOURCE-SUPPORTED KNOWLEDGE
- OBSERVATION
- DERIVED LOGIC
- CANDIDATE
- HYPOTHESIS
- UNKNOWN
- CONFLICT
- RETIRED

These statuses describe epistemic condition and must not be treated as interchangeable.

Agent inference must never be silently represented as source-supported teaching.

---

# 8. Logic Point

Logic Point is provisionally defined as an independently referenceable reasoning/explanation unit that may relate to one or more:

- Logic objects
- Investigations
- Evidence
- Market Events

A Logic Point is not required to contain the complete structure of a Logic.

This model remains **Source-Dependent** pending verification against the relevant Michael Huddleston / ICT source material.

If source research establishes a different conceptual model, the source-supported interpretation takes precedence and C6 may be revised through the appropriate architectural change process.

---

# 9. Investigation

Investigation is the structured examination of an event, condition, relationship, Logic Point, Scenario, or research question.

An Investigation may produce:

- observations
- candidate relationships
- candidate Logic
- hypotheses
- scenarios
- validation requests
- findings
- unresolved questions
- Logbook candidates
- requests for review of existing Logic

Investigation does not automatically produce Truth, Validated Logic, or Gate Decisions.

---

# 10. Open Float Study

Open Float Study is provisionally modeled as an **Investigation Capability / Process**.

It allows TOS to investigate information without prematurely forcing that information into a predefined conclusion.

A Study may examine:

- Time Context
- Price Elements
- Market Events
- Logic Points
- Criteria
- Indications
- Validations
- Divergences
- Convergences
- Multiple time contexts
- Scenarios
- Market conditions
- Existing Logic
- Research questions

A Study may begin before an outcome is known.

It may produce:

- observations
- relationships
- candidate Logic
- hypotheses
- scenarios
- validation requirements
- unresolved questions
- lessons
- Logbook candidates

It cannot:

- promote itself to authoritative Truth
- alter C5 Evidence
- issue a Gate Decision
- directly change C3 Work Object State
- silently modify Logic
- rewrite historical knowledge

The concrete execution of a Study should use C2/C3 Work Object and lifecycle mechanisms rather than introducing a separate lifecycle engine.

The exact meaning of Open Float Study in Michael Huddleston's teaching remains **Source-Dependent** and must be verified before being treated as source-authoritative.

---

# 11. Market Event

Market Event represents what happened.

It must remain distinct from:

- Investigation — examination of what happened
- Logic — explanation/relationship
- Scenario — conditional interpretation
- Logbook — historical learning record

A Market Event may remain:

- Known
- Partially Known
- Unknown
- Unresolved

An unresolved event must not be forced into an explanation merely for narrative completeness.

---

# 12. Market Event Investigation

A Market Event Investigation should preserve, where applicable:

- Market Time
- Knowledge Time
- observed conditions
- available evidence
- initial Perspective
- candidate explanations
- supporting evidence
- contradicting evidence
- relevant Logic
- Logic Points
- scenarios
- validation status
- outcome
- unresolved questions
- later discoveries
- lessons

The investigation must distinguish what was known before the event from what was discovered afterward.

---

# 13. Time Context and Time Mapping

Time is a critical contextual input to C6.

A working hierarchy may include:

MONTHLY
→ WEEKLY
→ DAILY
→ INTRADAY
→ SESSION
→ EVENT TIME

This hierarchy remains a shared/contextual framework rather than exclusive C6 ownership.

C6 must consume relevant Time Context when determining Logic applicability, Scenario interpretation, and Market Event relationships.

---

# 14. Knowledge Time

Knowledge Time is a cross-stage architectural invariant.

TOS must distinguish:

**Market Time** = when an event occurred.

**Knowledge Time** = when the information, observation, inference, or discovery became available to the system/user.

This prevents post-hoc contamination.

Information discovered after an event must not be silently inserted into the historical Perspective that existed before that information became available.

---

# 15. Perspective

Perspective represents a contextual view or expectation at a particular Knowledge Time.

Perspective:

- may change
- is not Fact
- must be time-contextualized
- must not rewrite historical observations
- must preserve uncertainty

A later Perspective must not retroactively replace the earlier Perspective.

---

# 16. Scenario

Scenario represents a conditional interpretation rather than a fact or Gate Decision.

A Scenario should be capable of representing:

- supporting conditions
- contradicting conditions
- relevant Logic
- relevant Logic Points
- Time Context
- validation status
- invalidation conditions
- alternative scenarios
- expected conditions
- outcome
- uncertainty

Multiple scenarios may coexist.

A scenario does not become true merely because its outcome occurred.

---

# 17. Price and Time Elements

Price Elements and Time Elements are treated as observations/contextual inputs.

They do not automatically constitute:

- Valid Logic
- Valid Setup
- Gate Condition
- Trade Decision

Their meaning must be evaluated within the applicable context, criteria, Logic, and validation.

Time/Price disagreement must remain explicitly representable.

---

# 18. PD Array Boundary

Presence of a PD Array does not automatically establish:

- valid setup
- valid Logic
- valid entry
- valid Gate condition
- expected outcome

PD Array information is an input that requires contextual interpretation and appropriate criteria/validation.

---

# 19. Convergence and Divergence

### Convergence

Convergence represents a relationship or alignment among relevant observations/elements.

It is not a guaranteed outcome.

### Divergence

Divergence represents a relationship or discrepancy among relevant observations/elements.

It is not itself a decision.

Both must remain interpretable within their Time Context and applicable Logic.

---

# 20. Logic Validation

Logic Validation is owned by C6.

It is distinct from:

### Evidence Validation — C5
Determines whether evidence satisfies Evidence/Truth requirements.

### Logic Validation — C6
Determines whether a Logic satisfies the semantic and operational requirements necessary for its applicable status.

### Gate Evaluation — C4
Determines whether Gate conditions are satisfied and produces the authoritative Gate Decision.

Therefore:

**Valid Evidence ≠ Validated Logic**

and:

**Validated Logic ≠ Gate PASS**

---

# 21. Logic Lifecycle

C6 defines the semantic requirements for Logic status changes.

C3 owns the generic lifecycle mechanics and transition execution.

A conceptual Logic lifecycle is:

CANDIDATE
→ UNDER REVIEW
→ VALIDATED
→ ACTIVE
→ RESTRICTED / SUSPENDED
→ REVIEW
→ ACTIVE or RETIRED

A REJECTED condition may also exist where appropriate.

The exact lifecycle state model must remain aligned with C3.

---

# 22. Logic Evolution

Logic must evolve through controlled versioning.

New evidence or observations must trigger **Logic Impact Review**, not automatic modification.

Possible outcomes include:

- retain current Logic
- create a new version
- narrow applicability
- expand applicability after sufficient validation
- restrict applicability
- suspend Logic
- retire Logic
- reopen/re-evaluate previously retired Logic where justified

No silent mutation is permitted.

Historical versions must remain reconstructable.

---

# 23. Failure Handling

A Logic failure does not automatically prove that the Logic is false.

Possible explanations include:

- incorrect applicability
- incomplete conditions
- insufficient evidence
- contextual difference
- conflicting Logic
- measurement/observation limitations
- genuine Logic weakness

Therefore failure should trigger review rather than automatic deletion or disproof.

---

# 24. Conflicting Logic

Conflicting Logic must be representable as a first-class condition.

TOS must not silently select one Logic merely because an Agent has greater confidence in it.

Conflicts may result in:

- additional investigation
- restricted applicability
- temporary suspension
- comparative validation
- new Logic version
- unresolved status

Human Authority remains the final architectural authority where architectural changes are required.

---

# 25. Ex-Ante and Ex-Post Reasoning

C6 must preserve two distinct reasoning paths.

### Ex-Ante

Knowledge available at T
→ Perspective
→ Scenario
→ Expected Conditions

### Ex-Post

Outcome
→ Investigation
→ New Evidence / Observation
→ Explanation
→ Learning

The second path must never rewrite the first.

Outcome is information about what happened, not permission to alter what was knowable at the earlier time.

---

# 26. Logbook

Logbook is treated as **Historical Memory**.

A Logbook Entry may preserve:

- event
- investigation
- Perspective
- Time Context
- Knowledge Time
- relevant Logic versions
- Logic Points
- scenarios
- supporting evidence
- contradicting evidence
- outcome
- explanations
- hypotheses
- unknowns
- lessons
- limitations

Not every Investigation must produce a Logbook Entry.

A Logbook pattern must not automatically become validated Logic.

---

# 27. Knowledge Repository

Knowledge Repository is broader than the Logbook.

Conceptually:

**Logbook = Historical Memory**

**Knowledge Repository = Reusable Knowledge**

Knowledge may include:

- source-supported knowledge
- observations
- Logic
- validated patterns
- hypotheses
- lessons
- historical references
- conflicts
- unknowns
- retired knowledge

Promotion from historical observation or Logbook material into reusable knowledge must be controlled.

---

# 28. Daily Narrative

Daily Narrative is a structured contextual View/Report rather than a Truth Engine or Logic object.

It may consume:

- Time Mapping
- higher-timeframe context
- current Perspective
- active Logic
- Logic Points
- Scenarios
- Market Events
- validations
- unresolved questions
- historical context

Daily Narrative must remain traceable to its underlying information and must not invent explanations for narrative completeness.

---

# 29. Self-Improvement

Self-improvement must operate through controlled knowledge evolution.

Conceptual flow:

Historical Data
→ Observation
→ Pattern
→ Candidate Logic
→ Validation
→ QC
→ Authorized Promotion
→ New Logic Version

The following flow is prohibited:

Pattern
→ Agent Confidence
→ Automatic Truth
→ Automatic Rule Change

Self-improvement therefore means controlled learning, not uncontrolled self-rewrite.

---

# 30. Performance Traceability

C6 is not the future Performance Engine.

However, C6 must preserve enough identity and version information to support future attribution such as:

Logic Version
→ Scenario
→ Action/Trade
→ Outcome
→ Performance Analysis

PnL, win-rate calculation, risk adjustment, position sizing, and broader performance analytics remain outside C6.

---

# 31. QC Relationship

C6 outputs must remain reviewable by the applicable QC mechanisms defined elsewhere in TOS.

QC may:

- identify unsupported reasoning
- identify contradictions
- reject or return candidate Logic
- require additional evidence
- identify source-attribution errors
- identify lifecycle violations
- identify historical contamination
- identify boundary violations

QC does not become the owner of the underlying Logic domain.

---

# 32. Source Integrity

When TOS attributes a concept or Logic to Michael Huddleston / ICT, that attribution must be supported through the Evidence & Truth architecture.

An Agent-discovered relationship without source support must be classified appropriately, such as:

- Agent Inference
- Candidate Logic
- Hypothesis
- Derived Logic

It must not be presented as ICT Teaching without supporting source evidence.

If later source research contradicts a current interpretation, the source-supported interpretation takes precedence and the architectural impact must be evaluated through the controlled change process.

---

# 33. C6 → C4 Boundary

C6 produces reasoning/Logic results.

C4 performs Gate Evaluation and owns the authoritative Gate Decision.

Therefore:

C6 Result
→ possible Gate Input

but never:

C6 Result
→ automatic Gate Decision

---

# 34. C6 → C3 Boundary

C6 defines semantic requirements for Logic lifecycle states.

C3 owns:

- state
- transition
- transition authority
- lifecycle history
- recovery/rework mechanics

C6 must not implement an independent generic lifecycle engine.

---

# 35. C6 → C5 Boundary

C5 owns Evidence & Truth.

C6 consumes and interprets evidence for Logic purposes.

C6 may trigger a Logic Review when evidence changes or conflicts arise.

C6 cannot modify Evidence authority or Evidence truth status.

---

# 36. C6 → C7 Boundary

C6 determines:

> What was determined, discovered, reasoned, or learned?

C7 determines:

> How that result is transferred to another component/Agent/Stage?

C6 therefore produces handoff-ready results but does not own the Handoff Protocol.

---

# 37. C6 Architectural Invariants

The following invariants are mandatory:

1. Logic ≠ Evidence
2. Logic ≠ Gate Decision
3. Logic ≠ Lifecycle Engine
4. Logic ≠ Handoff
5. Observation ≠ Perspective
6. Perspective ≠ Fact
7. Hypothesis ≠ Knowledge
8. Pattern ≠ Validated Logic
9. Valid Evidence ≠ Validated Logic
10. Convergence ≠ Guaranteed Outcome
11. Divergence ≠ Decision
12. PD Array ≠ Automatically Valid Setup
13. Logbook ≠ Truth Engine
14. Narrative ≠ Evidence
15. Agent Confidence ≠ Logic Validity
16. UNKNOWN ≠ FALSE
17. Failure ≠ Automatic Disproof
18. Retirement ≠ Deletion
19. Outcome Does Not Rewrite the Past
20. Self-Improvement ≠ Self-Rewrite
21. No Silent Logic Mutation
22. No Automatic Promotion
23. No Post-Hoc Contamination
24. Source Teaching ≠ Agent Inference

---

# 38. Open / Source-Dependent Items

The following remain intentionally open and are not treated as architectural failures:

### 38.1 Logic Point
Exact source-defined meaning requires verification from relevant source material.

### 38.2 Open Float Study
Current architectural representation is provisional until the source-defined concept is fully researched.

### 38.3 Detailed Object Schema
Detailed fields and implementation-level structures belong to later specification work.

### 38.4 Interfaces
Detailed interfaces and integration contracts must be defined after the architectural stage.

### 38.5 Performance Integration
Detailed integration with the future Performance Engine remains outside C6.

Open items must not be silently converted into assumptions.

---

# 39. Source-First Concept Resolution

For any specialist concept whose authoritative meaning has not yet been verified:

1. Preserve the candidate concept.
2. Mark it as Source-Dependent.
3. Research the authoritative source.
4. Extract and qualify the source-supported meaning.
5. Compare it with the current architecture.
6. Perform impact analysis.
7. Update architecture only if required.
8. Preserve the previous interpretation historically.

The architecture must adapt to verified source knowledge rather than forcing the source into an unsupported existing model.

---

# 40. Architectural Authority

TOS Engineer / Human Authority / System Owner remains the final authority for:

- architectural approval
- architectural change
- promotion of architectural decisions
- resolution of architectural conflicts
- authorization of changes to frozen architecture

Agents may analyze, propose, investigate, validate within their authority, and identify conflicts, but cannot independently redefine the TOS architecture.

---

# 41. Consistency Status

C6 has been reviewed against:

- C1 — Agent Operational Contract
- C2 — Work Object Specification
- C3 — State & Lifecycle Specification
- C4 — Gate Specifications
- C5 — Evidence & Truth Specification

No architectural contradiction was identified during the Document-Level Consistency Check.

The following boundaries remain intact:

- Agent Authority
- Work Object Ownership
- Lifecycle Ownership
- Gate Decision Authority
- Evidence & Truth Authority
- Handoff Boundary
- Historical Integrity
- Knowledge Time
- Source Integrity
- Controlled Logic Evolution

---

# 42. Final Status

**FINAL / FROZEN / APPROVED**

This document represents the approved Stage C6 architectural specification.

Any future change to this frozen architecture must follow the established TOS change-control and Human Authority approval process.

The next architectural stage is **Stage C7 — Handoff Protocol**, and it must not begin until this Stage C6 document has been independently uploaded to the TOS GitHub repository and the TOS Engineer / Human Authority confirms the upload.

---

## Permanent Stage Workflow

**CURRENT STAGE → FINALIZE DOCUMENT → GITHUB UPLOAD → USER CONFIRMS UPLOAD → NEXT STAGE**
