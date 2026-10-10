# TOS — Operational Specification Baseline
## Integrated Operational Specifications OP-01 to OP-13
**Document status:** FINALIZATION CANDIDATE  
**Version:** v1.0  
**Workstream:** Architecture-to-Execution Transition  
**Scope:** Operational specification; not architecture redesign, software implementation, or proof of trading validity.

---

## 1. Purpose

This document consolidates the basic operational contracts for TOS operations OP-01 through OP-13. It establishes common fields, responsibilities, preconditions, inputs, outputs, state rules, evidence requirements, failure paths, and next-operation rules.

Acceptance in this document means the specification is coherent at the **basic operational contract level**. It does not prove that any ICT/Michael Huddleston claim is true, that domain-specific validation criteria are complete, or that an implementation has been built or tested.

## 2. Governing boundaries

- Frozen architecture C1–C7 and D0–D14 remains closed. No new architecture stage is created without a proven Architectural Reason.
- D10 validates claims and manages learning; learning does not automatically become validated knowledge.
- D11 governs knowledge changes; approval, version creation, and activation are separate events.
- D12 controls qualification and context-specific application.
- D13 issues Operational Determinations; a Determination is not a Runtime Permission.
- D2 independently controls Runtime Permission and Runtime activity.
- D5 controls recovery of operational flow; Recovery cannot change knowledge, governance, or a Determination by itself.
- D8 provides Identity/Correlation controls across relevant activity.
- D9 controls traceability/observability; traces are not, by themselves, proof of truth.
- Human Authority remains the final authority where the established governance rules require human approval.
- Historical records are not silently overwritten. Corrections and updates must remain traceable.
- No runtime activity may proceed without a valid permission for the specific activity and scope.
- Missing required information must be represented explicitly; it must not be invented.

## 3. Shared operational contract

Each operation is specified using the following fields:

1. **Trigger**
2. **Responsible domain**
3. **Inputs**
4. **Preconditions and authority**
5. **Processing responsibility**
6. **Control gate**
7. **Outputs and state transition**
8. **Evidence, identity, and traceability**
9. **Failure path**
10. **Next operation**
11. **Acceptance criteria**

Shared contract rules:

- **CR-01 — Object/version/context linkage:** Each material result must link to the relevant object, version, and context where applicable.
- **CR-02 — Separate decisions and state changes:** A decision, a state transition, version creation, and activation are distinct events unless an explicit rule defines otherwise.
- **CR-03 — Re-evaluation after material change:** Relevant changes in context, version, or conditions trigger re-evaluation where required.
- **CR-04 — Permission validity at use:** Runtime Permission is checked at the time of use and against the requested activity/scope.
- **CR-05 — Explicit evidence/trace gaps:** Missing, incomplete, conflicting, or unverified evidence and traces must be recorded as such.
- **CR-06 — Safe retry:** Before retrying an operation with an unknown result, inspect prior state/result to avoid duplicate or contradictory activity.
- **CR-07 — Unknown is neither success nor failure:** An unknown outcome must remain unknown until adequately resolved.
- **CR-08 — Historical integrity:** Corrections are represented by traceable new records or versions, not silent mutation.

## 4. Operational specifications

### OP-01 — Capture & Structure Knowledge
**Responsible domain:** Knowledge/Research  
**Trigger:** Source material is selected for capture, extraction, or structuring.

**Inputs**
- Source content or reference
- Research context and provenance available
- Links to existing claims or content, if any

**Preconditions and processing**
- Record the source and provenance available.
- Extract claims and Logic Points.
- Distinguish explicit source teaching from interpretation, hypothesis, and inference.
- Record ambiguity, contradictions, and missing information.
- Create or link records without silently mutating historical records.

**Outputs**
- Operational Knowledge Record
- Logic Points and provenance references
- Epistemic status, ambiguity, and evidence needs

**State rule**
- A newly captured record may be `PROPOSED`.
- OP-01 cannot directly mark it `VALIDATED`, `ADMITTED`, or `ACTIVE`.

**Failure / next operation**
- If provenance is unavailable, record the limitation and do not present the claim as confirmed source teaching.
- Proceed to OP-02 when a claim is ready for validation; otherwise collect missing source information.

**Acceptance criteria**
- Source and epistemic status are distinguishable.
- Outputs and allowed states are clear.
- Missing information is not fabricated.
- Validation and governance boundaries are preserved.

### OP-02 — Validate Knowledge Claim
**Responsible domain:** D10 — Operational Knowledge Validation & Learning  
**Trigger:** A Knowledge Claim or Hypothesis is submitted for evaluation.

**Inputs**
- Claim and relevant version
- Source and provenance
- Evidence and counterevidence
- Criteria appropriate to the claim type
- Context and limitations

**Preconditions and processing**
- If claim type or criteria are unspecified, do not declare validation complete.
- Check provenance and the relation between claim and evidence.
- Check that criteria fit the claim type.
- Record contradictions, counterevidence, limitations, result, and rationale.
- Preserve the distinction between explicit source teaching, evidence-supported result, and inference.

**Outputs**
- Validation Record
- Result, rationale, Evidence references, limitations, and contradictions
- Links to the Knowledge Record/version

**Allowed hypothesis/inference results**
- `SUPPORTED`
- `UNSUPPORTED`
- `INSUFFICIENT_EVIDENCE`

**State rule**
- A hypothesis result does not automatically change the Knowledge Record state.

**Failure / next operation**
- Missing criteria or evidence results in incomplete/blocked validation, not automatic approval or rejection.
- Proceed to OP-03 when governance is needed; otherwise gather additional evidence.

**Acceptance criteria**
- Claim type, criteria, evidence, contradictions, limitations, and result are explicit.
- A result for a hypothesis is not confused with the lifecycle state of a Knowledge Record.
- Acceptance of this contract does not validate any specific market claim.

### OP-03 — Govern Knowledge or Change
**Responsible domain:** D11 — Governance / Evolution  
**Trigger:** A Knowledge Record or Change Proposal requires a governance decision.

**Inputs**
- Decision subject and version
- Validation result and evidence, as required
- Change Proposal and impact information
- Relevant governance rules and prior decisions

**Preconditions and processing**
- Confirm subject, scope, and decision authority.
- Obtain Human Authority where established rules require it.
- Review validation basis, impact, conflicts, and proposed change.
- Record decision, rationale, scope, conditions, and permitted follow-up.

**Outputs**
- Governance Decision
- Decision reason and scope
- Conditions, rejection reason, and links to affected Knowledge Version(s)

**Allowed outcomes**
- `APPROVED`
- `REJECTED`
- Review remains open or pending information

**State rule**
- Governance approval, Knowledge Version creation, and activation are separate events.

**Failure / next operation**
- Missing authority, information, or required basis must not result in automatic approval.
- Proceed to OP-04 if a governed candidate is proposed for application; return to OP-01/OP-02 if further source information or validation is needed; stop the proposed path if rejected.

**Acceptance criteria**
- Decision, authority, rationale, scope, and effects are recorded.
- Approval does not silently activate a version.

### OP-04 — Qualify Operational Candidate
**Responsible domain:** D12 — Controlled Operational Application  
**Trigger:** A governed Knowledge Object is proposed as an operational candidate.

**Inputs**
- Identifiable Knowledge Version
- Applicable Governance Decision and status
- Intended use
- Qualification Criteria
- Relevant limitations and Evidence

**Preconditions and processing**
- Confirm version and governance status.
- Ensure criteria are defined before evaluation.
- Evaluate against criteria and record met/unmet criteria, limitations, and missing information.

**Outputs**
- Qualification Record
- Result and rationale
- Links to version and Evidence
- Use limitations

**Allowed outcomes**
- `QUALIFIED`
- `DISQUALIFIED`
- Open/blocked evaluation where required information is insufficient

**Failure / next operation**
- Missing required criteria, version, or governance status blocks a final result.
- Proceed to OP-05 only when qualified and its preconditions are satisfied.

**Acceptance criteria**
- Result is reviewable against explicit criteria and a specific version.
- `QUALIFIED` does not mean applicable in every context.

### OP-05 — Evaluate Applicability
**Responsible domain:** D12 — Controlled Operational Application  
**Trigger:** A qualified candidate is assessed for a specific operational context.

**Inputs**
- Application Candidate
- Qualification Record
- Knowledge Version
- Application Context
- Applicability criteria, conditions, limitations, and relevant Evidence

**Preconditions and processing**
- Confirm required qualification.
- Ensure context and criteria are sufficiently specified.
- Compare context with applicability conditions and non-applicability conditions.
- Record evidence, rationale, limitations, and conditions for re-evaluation.

**Outputs**
- Applicability Record
- Result and assessed context
- Links to Knowledge Version and Qualification Record
- Limitations and re-evaluation triggers

**Allowed outcomes**
- `APPLICABLE`
- `NOT_APPLICABLE`
- Pending/undetermined where information is insufficient

**Failure / next operation**
- Incomplete context or insufficient evidence must not default to `APPLICABLE`.
- Material changes to context, version, or conditions may require re-evaluation.
- Proceed to OP-06 only when applicable and other Determination preconditions are met.

**Acceptance criteria**
- Every result is tied to a specific context and version.
- Applicability in one context is not automatically generalized to another.

### OP-06 — Produce Determination
**Responsible domain:** D13 — Operational Determination  
**Trigger:** A defined operational question requires a Determination and required inputs are available.

**Inputs**
- Application Context
- Applicable Applicability Record
- Relevant Knowledge Version and limitations
- Evidence references
- Conditions, constraints, and Correlation
- Relevant prior Determinations, where needed

**Preconditions and processing**
- Confirm context, applicability, criteria, authority, and required evidence.
- Evaluate the information, constraints, contradictions, and unresolved conditions.
- Record the result, rationale, conditions, and limits.
- Issue only when required conditions are met.

**Outputs**
- Determination Record
- Result, rationale, conditions, and constraints
- Evidence references and links to context/version

**Lifecycle**
`INITIATED → CONTEXT_ESTABLISHED → EVALUATED → DETERMINED → ISSUED`
Other established terminal states include `CANCELLED`, `EXPIRED`, and `SUPERSEDED`.

**Failure / next operation**
- Missing context, evidence, or required criteria prevents an unjustified final Determination.
- Proceed to OP-07 if Runtime activity is requested.

**Acceptance criteria**
- The Determination states what was determined, for which context, on what basis, and under what constraints.
- Issuance does not grant Runtime Permission.

### OP-07 — Evaluate Runtime Permission
**Responsible domain:** D2 — Runtime Control  
**Trigger:** A requested Runtime Activity requires permission evaluation.

**Inputs**
- Relevant Determination
- Requested activity and scope
- Conditions and constraints
- Current Runtime context/state
- Identity/Correlation and permission validity requirements

**Preconditions and processing**
- Identify the activity and scope.
- Check alignment with the Determination.
- Evaluate conditions, scope, time validity, and required controls.
- Record result, rationale, scope, and validity.

**Outputs**
- Runtime Permission Record
- `GRANTED` or `DENIED`
- Scope, conditions, validity, and links to the Determination/request

**Lifecycle**
`REQUESTED → EVALUATED → GRANTED / DENIED`
A granted permission may proceed through `ACTIVE` and then `CONSUMED`, `EXPIRED`, or `REVOKED`, as applicable.

**Failure / next operation**
- Expired, revoked, out-of-scope, or unverified permission blocks the activity.
- Proceed to OP-08 only with a valid permission.

**Acceptance criteria**
- Permission is independent of Determination and checked at use.
- Permission is limited to its specified activity, scope, and validity.

### OP-08 — Execute Authorized Activity
**Responsible domain:** Runtime under D2 control  
**Trigger:** An activity is ready for execution and its permission must be verified.

**Inputs**
- Runtime Permission
- Determination reference
- Activity definition and scope
- Execution conditions
- Identity/Correlation
- Trace and outcome-recording requirements

**Preconditions and processing**
- Verify permission validity, scope, and conditions at use.
- Record activity start and links to permission/Determination.
- Control activity state within the authorized scope.
- Record completion, interruption, failure, or unknown outcome.
- Provide necessary information for traceability and recovery.

**Outputs**
- Runtime Activity Record
- Execution state and outcome
- Evidence and related references
- Failure/recovery information, where applicable

**Lifecycle**
`AUTHORIZED → STARTED → ACTIVE → COMPLETED`
Failure path: `ACTIVE → FAILED → RECOVERY_REQUIRED`

**Failure / next operation**
- Unknown result remains unknown until resolved; check prior state before retrying.
- Failure invokes OP-10 and, where needed, OP-11.
- Relevant activity events are recorded through OP-09.

**Acceptance criteria**
- Execution stays within permission scope.
- Outcome is recorded and unknown outcomes are not treated as success or failure by assumption.
- Runtime cannot grant itself new authority.

### OP-09 — Record Operational Trace
**Responsible domain:** D9 — Operational Traceability & Observability  
**Trigger:** A material operational event or state change requires a trace.

**Inputs**
- Activity/event identity and Correlation
- Event type, observed state, and temporal context
- Related Determination and Permission references, where applicable
- Evidence/outcome and failure/recovery references, where applicable

**Preconditions and processing**
- Identify event and origin to the extent possible.
- Link trace to related activity and control references.
- Preserve temporal ordering and correlation.
- Distinguish recorded facts from interpretation.
- Explicitly record incomplete, delayed, or unavailable trace information.

**Outputs**
- Trace Record
- Event/activity links
- Trace completeness status
- Related Evidence, Failure, and Recovery references

**Failure / next operation**
- Missing Correlation or incomplete trace is recorded as a traceability gap.
- Historical activity is not erased or falsely represented as fully traced.

**Acceptance criteria**
- Relevant activity can be traced.
- Trace gaps are visible.
- A Trace alone does not establish the truth of a claim or Determination.

### OP-10 — Detect & Record Failure
**Responsible domain:** Operational failure detection/recording, coordinated with D2, D5, and D9  
**Trigger:** Failure, violated precondition, unexpected interruption, or inconsistent state is detected.

**Inputs**
- Activity and current state
- Expected versus observed conditions
- Time and Correlation
- Related permission/Determination, where applicable
- Available failure Evidence

**Preconditions and processing**
- Describe the observed event.
- Separate observed facts from suspected causes.
- Record context, activity, evidence, and recovery need.
- Trigger applicable controls for stop, containment, or recovery.

**Outputs**
- Failure Record
- Failure description/state
- Evidence and Correlation
- Confirmed cause or explicitly labelled hypothesis
- Recovery requirement

**Lifecycle**
`DETECTED → RECORDED → CLOSED`

**Failure / next operation**
- Incomplete information is recorded as a limitation.
- Proceed to OP-11 when recovery is needed; ensure traceability through OP-09.

**Acceptance criteria**
- Suspected and confirmed causes are distinguished.
- Closing a Failure Record does not prove Recovery succeeded.

### OP-11 — Recover Operational Flow
**Responsible domain:** D5 — Failure & Recovery  
**Trigger:** A recorded Failure requires operational recovery.

**Inputs**
- Failure Record
- Last reliable activity state
- Recovery Context
- Recovery constraints and safety conditions
- Related Determination/Permission references
- Information needed to prevent unintended duplicate activity

**Preconditions and processing**
- Identify the activity and failure.
- Assess actual prior state and possible prior effects.
- Select an authorized recovery path: continue, resume, stop, or refer to Human Authority as applicable.
- Record recovery actions and result.
- Do not change knowledge, governance, or Determination through Recovery itself.

**Outputs**
- Recovery Record
- Recovery status and actions
- Limitations and final result/failure reason
- Information needed for controlled continuation

**Lifecycle**
`INITIATED → IN_PROGRESS → SUCCESSFUL / FAILED`

**Failure / next operation**
- Insufficient Recovery Context blocks unsafe recovery.
- Unknown prior result must be resolved as far as possible before retry.
- Return to Runtime only when required conditions are satisfied; record results in D9 and pass relevant observations to D10.

**Acceptance criteria**
- Recovery is based on reliable state/context.
- `RECOVERY_FAILED` is not equivalent to `RECOVERED`.
- Recovery does not create Runtime Permission.

### OP-12 — Convert Observations into Learning
**Responsible domain:** D10 — Operational Knowledge Validation & Learning  
**Trigger:** Observation, Runtime result, Failure, Recovery, or new Evidence is available for review.

**Inputs**
- Observation or recorded result
- Related Trace and Correlation
- Available Evidence and provenance
- Related Knowledge Version/Hypothesis, where applicable
- Limitations, contradictions, and uncertainty

**Preconditions and processing**
- Distinguish observation from interpretation.
- Identify provenance and Evidence limitations.
- Examine relation to existing claims/hypotheses.
- Record Learning and, if useful, a new Hypothesis or research question.
- Refer claims requiring validation to OP-02.

**Outputs**
- Learning Record
- Linked Observation and Evidence
- Hypothesis/research question, if needed
- Limitations and validation needs

**Failure / next operation**
- Insufficient or conflicting Evidence is recorded as such.
- Proceed to OP-02 for claim validation or OP-13 if a knowledge/change proposal is raised.

**Acceptance criteria**
- Observation, Evidence, and Learning are distinguishable.
- Learning does not automatically validate, activate, or replace knowledge.

### OP-13 — Govern Resulting Knowledge Change
**Responsible domain:** D11 — Governance / Evolution  
**Trigger:** Learning or validation results lead to a proposal to change knowledge, rules, or an active version.

**Inputs**
- Learning Record and provenance
- Change Proposal
- Validation Record and Evidence, where required
- Current Knowledge Version/history
- Impact/risk information
- Required governance authority

**Preconditions and processing**
- Define subject and change scope.
- Check evidence/validation basis and authority.
- Assess impact, contradictions, and dependent applications.
- Record governance decision and authorized follow-up.
- Preserve separation between approval, version creation, and activation.

**Outputs**
- Governance Decision
- Rationale and scope
- Links to Learning, Validation, and Change Proposal
- Conditions for subsequent versioning/activation

**Allowed outcomes**
- `APPROVED`
- `REJECTED`
- Pending/review open

**Failure / next operation**
- Missing Evidence or authority does not produce automatic approval.
- If approved, proceed through OP-03 and the established controlled versioning/activation path.

**Acceptance criteria**
- Learning-driven change is governed.
- OP-13 reuses D11 responsibility; it creates no new architectural owner.

## 5. Cross-operation acceptance checks

The integrated review found the following logical controls coherent at the basic contract level:

- Validation result does not automatically alter Knowledge Record state.
- Governance approval, version creation, and activation remain separate.
- Qualification does not imply applicability.
- Applicability is context- and version-dependent.
- Applicability does not automatically issue a Determination.
- Determination does not grant Runtime Permission.
- Runtime Permission is checked at use and within scope.
- Runtime outcomes and traces do not automatically establish truth.
- Failure and Recovery results remain separate.
- Unknown outcomes are neither success nor failure by assumption.
- Missing Correlation/Trace is explicitly represented.
- Learning does not automatically become validated Knowledge.
- Knowledge changes follow validation and governance controls.
- Historical records are not silently overwritten.

Representative logical scenarios reviewed: normal end-to-end flow; not-applicable context; permission denied; runtime failure and recovery; unknown outcome and retry; contradictory evidence; missing Correlation/Trace. These are specification-level logical checks, not software execution tests.

## 6. Acceptance status and limitations

**Integrated Operational Specification Baseline: ACCEPTED at basic contract level.**

- OP-01 through OP-13: accepted at basic operational specification level.
- Consistency review: PASS.
- Authority boundary review: PASS.
- Lifecycle/historical integrity review: PASS.
- Representative end-to-end logical contract scenarios: PASS.
- New architectural gap: none demonstrated.
- New architecture stage: not justified by current evidence.

**Not established by this acceptance:**
- Truth or validity of any specific ICT/market claim.
- Completeness of all domain-specific criteria.
- Implementation readiness for every operation.
- Correctness or safety of a future software implementation.
- Live-market or trading performance.

Domain-specific validation, detailed operational requirements where genuinely needed, implementation verification, and runtime testing remain future workstreams. They must not be mislabeled as architectural gaps without evidence.

## 7. Next workflow boundary

This document is a finalization candidate for the current operational specification workstream. The required project workflow remains:

**CURRENT STAGE → FINALIZE DOCUMENT → GITHUB UPLOAD → USER CONFIRMS UPLOAD → NEXT STAGE**

No next stage begins before the user confirms the GitHub upload. No frozen architecture stage is reopened without a proven Architectural Reason.
