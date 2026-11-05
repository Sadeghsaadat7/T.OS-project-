# TOS — Educational Agent Operational Specification & Handoff Contracts

**Document ID:** TOS-EAS-S8-OPS-SPEC-001  
**Version:** 1.0 — Final  
**Stage:** Stage 8 — Educational Agent Operational Specification & Handoff Contract Definition  
**Status:** FINAL DOCUMENT — PENDING HUMAN AUTHORITY APPROVAL, GITHUB UPLOAD, AND USER CONFIRMATION  
**Date:** 2026-11-05

---

## 1. Purpose

This document translates the approved Stage 6 Educational Agent System Design and the Stage 7 Implementation Readiness Assessment into bounded operational specifications and handoff contracts. It defines what each of the five approved Agents may receive, produce, verify, stop on, and hand off. It does not implement, prompt, configure, or execute any Agent.

## 2. Authority, Scope, and Constraints

### 2.1 Authority

- **Human Authority / System Owner:** the user; final authority for scope, criteria, exceptions, acceptance, and material system decisions.
- **Project Designer / System Architect:** responsible for architecture coherence, stage management, and documentation; not an ICT Educational Agent or ICT Research Agent.

### 2.2 In scope

- Common operational contract shared by the five approved Agents.
- Agent-specific inputs, outputs, entry/exit conditions, stop conditions, and boundaries.
- Handoff contracts and traceability requirements.
- Operational statuses, validation outcomes, source eligibility, acceptance evidence, and rework paths.
- Operationalization of the existing Stage 6 quality gates.

### 2.3 Out of scope

- Agent prompts or prompt engineering.
- Code, software implementation, infrastructure, APIs, databases, or tool configuration.
- Running Agents or performing educational/source research.
- Reopening Stage B, C1–C7, or D0–D14.
- Changing the five-Agent architecture or creating an additional Agent.
- Declaring operational execution readiness.

### 2.4 Governing constraints

Stage 6 retains the approved five-Agent design. Stage 7 concluded **CONDITIONALLY READY FOR A BOUNDED PRE-IMPLEMENTATION SPECIFICATION STAGE**, not readiness for operational educational execution. The known source and evidence limitations remain unresolved unless separately evidenced and formally addressed.

## 3. Baseline and Known Limitations

The operational specification must preserve, not conceal, the following limitations:

1. The Core Content Source Registry contains 115 records; completeness of educational coverage has not been established.
2. Mentorship source coverage and completeness remain unresolved.
3. The cutoff and complete inventory for Latest Videos have not been established.
4. Complete evidence of educational coverage and knowledge integration is not available.
5. The existence of a registry or an accessible file does not prove that the overall source corpus is complete.
6. No evidence in this specification proves that the Agents have been implemented, executed, or operationally tested.

These limitations must travel with affected records and handoffs. They must not be silently converted into assumptions of completeness.

## 4. Approved Educational Agent Set

| ID | Agent | Primary responsibility |
|---|---|---|
| A1 | Source Inventory & Reconciliation | Identify, reconcile, and document sources and their eligibility. |
| A2 | Educational Review & Extraction | Review eligible source material and create structured educational records. |
| A3 | Evidence & Knowledge Validation | Evaluate claims against evidence and defined criteria. |
| A4 | Independent QC & Coverage | Independently assess quality, traceability, and coverage against the approved scope and criteria. |
| A5 | Knowledge Integration & Learning Record | Integrate only eligible, validated, QC-accepted records into controlled knowledge and learning records. |

**No sixth Agent is introduced.** Orchestration and workflow control remain TOS-level control mechanisms, not an additional educational Agent.

## 5. Common Operational Contract

Every task assigned to any Agent must carry the following contract fields:

1. Task ID and task version.
2. Responsible Agent and, where relevant, upstream Agent/output references.
3. Authorized scope and explicit exclusions.
4. Expected outcome and completion criteria.
5. Required inputs and optional inputs.
6. Input references, provenance, and eligibility status.
7. Known limitations and unresolved issues inherited from upstream work.
8. Dependencies and required prior gate decisions.
9. Stop conditions and prohibited actions.
10. Required deliverables and stable output references.
11. Source, evidence, and criteria references supporting the output.
12. Operational status and rationale.
13. Claim-validation outcomes, where applicable.
14. Uncertainty, ambiguity, conflicts, and unresolved deficiencies.
15. Acceptance evidence and QC requirements.
16. Handoff destination, handoff disposition, and next action.
17. Return, reject, escalation, and rework history.
18. Human Authority decisions required or received.
19. Receiving Agent or destination, where a handoff is required.
20. Separate task/output status, handoff disposition, and quality-gate decision references, as applicable.

The producing Agent is responsible for preparing a complete handoff package. The receiving Agent is responsible for recording an explicit disposition and identifying any conditions or deficiencies. A handoff is not accepted by silence, elapsed time, or the producer's own status declaration. Where the receiving party is Human Authority or a TOS-level process, the decision must be recorded by that authority/process rather than inferred by the producing Agent.

### 5.1 Common rules CC-01 to CC-10

- **CC-01:** An Agent must not work outside its approved responsibility and task scope.
- **CC-02:** Required inputs and dependencies must be checked before work begins.
- **CC-03:** Missing, inaccessible, ambiguous, or conflicting information must be explicitly recorded.
- **CC-04:** Outputs must be traceable to their inputs, sources, evidence, and applicable criteria.
- **CC-05:** Source teaching, interpretation, inference, hypothesis, evidence, and validation outcome must remain distinguishable.
- **CC-06:** Incomplete work must not be represented as complete or accepted.
- **CC-07:** Deficiencies must include their cause or observed condition, impact, and required next action where known.
- **CC-08:** An Agent must not change approved acceptance criteria or policy.
- **CC-09:** Changes to previously reviewed outputs must be versioned and traceable.
- **CC-10:** Decisions beyond an Agent's authority must be escalated to Human Authority.

## 6. Status and Decision Vocabulary

These dimensions are separate and must not be conflated.

### 6.1 Operational status

- **PASS:** the defined task or check was completed successfully within its stated scope and criteria.
- **PARTIAL:** some scoped work was completed, but one or more elements remain incomplete or limited.
- **FAIL:** the defined requirement was not met.
- **UNVERIFIED:** available evidence is insufficient to determine whether the requirement is met.
- **BLOCKED:** work cannot proceed because a required input, dependency, decision, or condition is unavailable.

### 6.2 Claim-validation outcome

- **SUPPORTED:** the claim is supported by the evidence evaluated under the stated criteria.
- **UNSUPPORTED:** the evaluated evidence does not support the claim under the stated criteria. This does not automatically prove the claim's opposite.
- **INSUFFICIENT_EVIDENCE:** the available evidence is insufficient to reach a supported or unsupported determination.

Claim-validation outcomes apply only where a claim is being evaluated. They are not operational statuses.

### 6.3 Handoff disposition

- **ACCEPT**
- **ACCEPT WITH LIMITATIONS**
- **RETURN**
- **REJECT**
- **ESCALATE**

A handoff disposition is a decision about a submitted deliverable, not a global operational status. **ACCEPT WITH LIMITATIONS** cannot bypass a mandatory quality gate or required acceptance condition.

### 6.4 Completion distinctions

- **Output Completion:** the producing Agent has completed and recorded its deliverable.
- **Handoff Acceptance:** the receiving party has accepted the handoff for its stated purpose.
- **Quality Acceptance:** the required QC/gate acceptance has been explicitly evidenced.

These are distinct events. An Agent's completed task or operational PASS does not, by itself, authorize downstream knowledge integration.

## 7. Task Entry, Exit, and Stop Rules

A task may start only when its identity, responsible Agent, authorized scope, expected outcome, required inputs, applicable criteria, and dependencies are sufficiently defined and no fundamental blocker prevents valid work.

A non-critical limitation may be carried forward only when it is recorded and does not invalidate the task's bounded objective. The task must be marked PARTIAL or otherwise accurately qualified where appropriate.

A task ends when the producing Agent records its output, status, limitations, and proposed handoff. This does not imply acceptance by the next Agent or quality acceptance by Agent 4.

Stop and escalate or block when a required source/input is unidentified or inaccessible, the scope or mandatory criteria are unclear, essential evidence is unavailable, or a material conflict requires a Human Authority decision.

## 8. Agent-Specific Operational Specifications

### 8.1 A1 — Source Inventory & Reconciliation

**Purpose:** establish source identity, provenance, registry relationship, accessibility, discrepancies, and task-specific eligibility.

**Required inputs:** approved source scope; current registry and relevant prior reconciliation records; source identifiers or candidate references; applicable reconciliation criteria.

**Optional inputs:** candidate files, metadata, alternate editions, archive references, and prior discrepancy notes.

**Required output:** Source Inventory & Reconciliation Record, containing at minimum:

- Stable Source ID; title; source group/type.
- Origin and provenance; source identifier and locator.
- Available metadata and metadata gaps.
- Accessibility and integrity observations.
- Relationship to the existing registry (matched, new candidate, duplicate candidate, conflict, or unresolved).
- Duplicate and identity-conflict findings.
- Known limitations and evidence references.
- Task-specific source eligibility decision and rationale.
- Operational status and next action.

**Entry conditions:** source scope and reconciliation criteria are defined; candidate source information is available for inspection.

**Exit conditions:** each in-scope candidate has a traceable record and an explicit disposition, including unresolved cases.

**Stop conditions:** identity cannot be established; required source cannot be accessed; material provenance conflict exists; registry modification or authority decision is required.

**Boundaries:** A1 does not infer corpus completeness from registry presence, does not silently modify an approved registry, and does not determine educational truth or claim validity.

**Disposition rules:** RETURN for a correctable missing identifier or record field; REJECT for out-of-scope material or an unauthorized request to alter the registry; ESCALATE for material identity/provenance conflict or a registry/authority decision.

### 8.2 A2 — Educational Review & Extraction

**Purpose:** review eligible source material within a defined scope and produce traceable structured educational records.

**Required inputs:** an accessible source deemed eligible for the requested task; A1 Source Record; review scope; source locator; applicable extraction/classification rules.

**Required output:** Structured Educational Records, containing:

- Record ID and Source ID.
- Precise source locator (for example, timestamp, page, section, or other applicable location).
- Review scope and extracted-content reference.
- Classification and attribution.
- Clear separation of source content from interpretation.
- Ambiguities, contradictions, and unresolved questions.
- Limitations and evidence references.
- Operational status and completion rationale.

**Entry conditions:** source identity and access are sufficient for the bounded review; task scope and extraction rules are known.

**Exit conditions:** the requested scoped review is recorded, with locators, classifications, and limitations; incomplete portions are identified.

**Stop conditions:** source is inaccessible or ineligible for the task; locator cannot be established; scope or mandatory classification rules are unclear.

**Boundaries:** extraction completion is not validation, endorsement, proof of correctness, or evidence of full source coverage. A2 does not resolve knowledge conflicts by inventing assumptions.

### 8.3 A3 — Evidence & Knowledge Validation

**Purpose:** evaluate defined claims or knowledge records against available evidence and approved criteria.

**Required inputs:** structured educational records; source references/locators; claim or proposition information; applicable validation criteria.

**Required output:** Evidence & Knowledge Validation Record, containing:

- Claim ID and exact claim/proposition under review.
- Claim classification and relevant record references.
- Source and evidence references, including locators.
- Supporting and contradictory evidence.
- Criteria and how they were applied.
- Evidence sufficiency assessment.
- Validation outcome: SUPPORTED, UNSUPPORTED, or INSUFFICIENT_EVIDENCE.
- Uncertainty, alternative explanations, and unresolved conflicts.
- Rationale and operational status.

**Entry conditions:** the claim is identifiable, relevant records and evidence are traceable, and applicable criteria are available.

**Exit conditions:** the validation record documents the evidence considered, criteria, outcome, rationale, and remaining uncertainty.

**Stop conditions:** claim meaning is materially ambiguous; evidence references are broken or unavailable; required criteria are missing; conflict requires a reserved Human Authority decision.

**Boundaries:** A3 must not treat source assertion as independent proof, suppress contradictory evidence, or convert insufficient evidence into a positive or negative conclusion. It does not unilaterally establish doctrine or policy.

### 8.4 A4 — Independent QC & Coverage

**Purpose:** independently assess the quality, traceability, evidence consistency, classification, completeness, and coverage of scoped educational/validation outputs.

**Required inputs:** A2 educational records; A3 validation records; approved scope and criteria; access to relevant source/evidence references.

**Required output:** Independent QC & Coverage Report, containing:

- QC task ID, assessed scope, and criteria versions.
- Records and evidence reviewed.
- Traceability, evidence consistency, classification, completeness, and coverage findings.
- Missing, duplicate, inconsistent, or nonconforming items.
- Accepted, excluded, returned, partial, unverified, or blocked item references.
- Explicit QC disposition, rationale, limitations, and required corrective actions.

**Entry conditions:** the review package and criteria are identifiable; sufficient source/evidence access exists for independent assessment.

**Exit conditions:** a scoped report and explicit disposition are recorded, including unassessed areas and limitations.

**Independence requirements:** A4 must not be the producer of the output it independently assesses or the owner of its corrective rewrite. It must not change criteria, blindly copy A3 conclusions, or rely solely on summaries where source/evidence access is necessary for the check.

**Coverage boundary:** coverage claims apply only to the actual scope, records, sources, and criteria examined. Failure to find an item in a limited corpus does not prove that the item is absent from all sources.

**Stop conditions:** criteria are missing or ambiguous; source/evidence access is insufficient for the required check; independence is compromised; a material criteria or policy dispute requires Human Authority.

### 8.5 A5 — Knowledge Integration & Learning Record

**Purpose:** create controlled knowledge and learning records from outputs that satisfy the required upstream validation and QC conditions.

**Required inputs:** eligible upstream outputs; documented A4 QC acceptance; source/evidence/validation references; approved integration rules; required Human Authority decisions.

**Required output:** Accepted Knowledge & Learning Records, containing:

- Stable knowledge-record reference and version.
- Source, evidence, validation, and QC acceptance references.
- Classification and validation status.
- Scope, limitations, exclusions, and unresolved issues.
- Learning record and integration rationale.
- Previous version and change history.
- Pending decisions and activation/governance status, where applicable.

**Entry conditions:** required upstream records are traceable and have the explicit acceptance required by the applicable gate; mandatory decisions are available.

**Exit conditions:** integration record and its references/history are recorded, or a clear blocked/returned disposition is issued.

**Stop conditions:** QC acceptance is absent or invalid; required references are broken; material limitations are missing; policy or authority decisions are pending.

**Boundaries:** A5 is not an independent source of truth. It cannot override A4, convert PARTIAL/FAIL/UNVERIFIED/BLOCKED outputs into accepted knowledge, or collapse learning, validation, governance, versioning, and activation into one status. Integration does not independently prove truth.

## 9. Handoff Contracts

Every handoff must include five elements: **Identity, Traceability, Status, Completeness, and Action**. The receiving Agent must record acceptance, limitation, return, rejection, or escalation; silence is not acceptance.

### 9.1 A1 → A2: Source Readiness Handoff

Minimum payload: Source ID and group; identity/provenance; metadata and locator; accessibility/integrity; registry relationship; discrepancies and limitations; source eligibility decision and rationale; task-specific authorized scope; operational status; next action.

### 9.2 A2 → A3: Educational Record Handoff

Minimum payload: structured educational record IDs; source IDs and locators; review scope; classification and attribution; source content versus interpretation; ambiguity/contradiction notes; limitations; unresolved questions; status and traceable references.

### 9.3 A3 → A4: Validation & Evidence Handoff

Minimum payload: claim/record IDs; claim-to-evidence links; classification; criteria and application; supporting and contradictory evidence; validation outcome and rationale; uncertainty; limitations; unresolved conflicts; status.

### 9.4 A4 → A5: QC Acceptance Handoff

Minimum payload: QC report reference; assessed scope and criteria; explicit acceptance decision and rationale; accepted record references; excluded/returned/partial/unverified/blocked items; limitations; corrective actions and approvals; status.

### 9.5 A5 → Human Authority / TOS

Minimum payload: prepared knowledge and learning record references; source/evidence/validation/QC references; integration and change history; limitations; unresolved and excluded items; pending decisions; governance/activation state; requested Human Authority action.

### 9.6 Handoff change history

Where a handoff or its output is revised, retain the previous and revised output references, reason for change, responsible party, supporting evidence, affected downstream references, and before/after status or disposition. Never silently overwrite a reviewed record.

## 10. Source Eligibility and Input Qualification

Eligibility is a task-specific input decision, not a global operational status:

- **ELIGIBLE:** identity, access, and required attributes are sufficient for the requested bounded task.
- **ELIGIBLE WITH LIMITATIONS:** the task can validly proceed, provided the stated limitations are carried forward.
- **INELIGIBLE:** the input cannot support the requested task (for example, an unidentified or inaccessible source for extraction from that source).
- **REQUIRES HUMAN DECISION:** a material conflict, authority question, exception, or scope decision is unresolved.

Examples:

- An identified and accessible source may be eligible for a bounded inventory or review task.
- Non-critical metadata gaps may permit eligibility with limitations if they do not invalidate the task.
- An unidentified source is ineligible for source-specific educational extraction until identity is resolved.
- An inaccessible file is ineligible for extraction from that file; a separately verified alternate source may be assessed as a distinct input.
- Conflicting editions or identifiers may require reconciliation or a Human Authority decision.
- An incomplete corpus may support analysis of the available subset, but cannot support a claim that the corpus is complete.

Eligibility must state the task it applies to, its rationale, limitations, and the evidence used. Eligibility for one task does not automatically confer eligibility for another.

## 11. Acceptance Evidence and Quality Gates

### 11.1 Minimum acceptance evidence by Agent

| Agent | Minimum evidence required |
|---|---|
| A1 | Source record, identity/provenance, access/integrity observations, registry relationship, discrepancy and eligibility disposition. |
| A2 | Structured extraction record, source locator, scope, classification/attribution, and limitations. |
| A3 | Claim-to-evidence links, criteria/application, validation outcome, rationale, and uncertainty. |
| A4 | Independent QC report, assessed scope/criteria, findings, limitations, and explicit disposition. |
| A5 | Knowledge/learning record, valid QC acceptance reference, source/evidence/validation links, and change history. |

### 11.2 Existing Stage 6 quality gates

This document does not create new architectural gates. It operationalizes the existing Stage 6 gates. For each task, the applicable validation requirements and any justified non-applicability must be identified from the approved scope and criteria before the work is performed; an Agent may not waive a required validation step after the fact.

- **G1 — Source Readiness:** A1 establishes and records source readiness for the bounded task.
- **G2 — Educational Record & Validation:** A2 produces traceable educational records; A3 records evidence-based validation for claims/knowledge propositions within the approved task scope. Any non-applicability must be explicit, justified against approved criteria, and must not be used to bypass a mandatory validation requirement.
- **G3 — Independent QC & Coverage:** A4 independently assesses the scoped package and records its disposition.
- **G4 — Controlled Knowledge Integration:** A5 integrates only records satisfying the applicable upstream acceptance requirements.

Passing one gate does not imply passing the next. Operational PASS at an earlier gate does not substitute for later evidence or acceptance. A5 must not integrate records where mandatory QC acceptance is absent.

## 12. Return, Reject, Escalation, and Rework

- **RETURN:** a specific, correctable deficiency prevents acceptance for the intended handoff.
- **REJECT:** the submission is outside authorized scope or violates a mandatory requirement in a way that makes it unacceptable for the requested purpose.
- **ESCALATE:** a decision is outside the Agent's authority or requires Human Authority interpretation/approval.
- **REWORK:** targeted correction by the responsible producer in response to a recorded deficiency.

Every RETURN must identify: affected output/record; deficiency; criterion or contract requirement not met; supporting evidence; responsible correction owner; required action; and reassessment requirement.

No arbitrary fixed maximum number of rework cycles is imposed. Repeated rework must record recurrence and cause where known. A change affecting a previously validated or QC-accepted output requires reassessment of all affected controls and downstream references.

Routing guidance:

- Source identity, provenance, or registry reconciliation issue → A1.
- Extraction, attribution, classification, or locator issue → A2.
- Claim/evidence/validation issue → A3.
- QC report or independence issue → independent reassessment; criteria/policy dispute → Human Authority.
- Integration or learning-record issue → A5.
- Scope, policy, criteria, role-boundary, or exception decision → Human Authority.

## 13. Human Authority Reserved Decisions

The following remain reserved to Human Authority:

1. Changes to approved scope or coverage boundaries.
2. Changes to acceptance criteria or governing policy.
3. Material source identity, provenance, or authority conflicts.
4. Exceptions to mandatory requirements.
5. Knowledge policy, doctrine, and authoritative interpretation decisions.
6. Changes to Agent responsibilities or role boundaries.
7. Material changes to previously accepted knowledge.
8. Decisions that cannot be resolved within an Agent's delegated authority.

Agents may present evidence, options, and implications, but may not assume or exercise these reserved powers.

## 14. Traceability and Change Integrity

- Every task, source, educational record, claim, validation record, QC report, knowledge record, and handoff must have a stable reference appropriate to its type.
- Outputs must identify the exact input and criteria versions used.
- Changes must identify the changed item, reason, responsible party, evidence, and affected downstream references.
- Previously reviewed versions must remain distinguishable from revised versions.
- Downstream records must preserve inherited limitations and unresolved issues.
- Broken or unverifiable references must be reported; replacement references must not be fabricated.
- Traceability supports auditability but does not, by itself, prove factual correctness.

This section operationalizes traceability for Stage 8 and does not redefine or reopen frozen D9 architecture.

## 15. Stage 8 Acceptance Criteria

The Stage 8 deliverable is acceptable only when all applicable criteria below are explicitly checked:

- **S8-AC-01:** The approved five-Agent structure is preserved without adding or removing Agents.
- **S8-AC-02:** Each Agent has defined responsibilities, required inputs, outputs, entry/exit conditions, and boundaries.
- **S8-AC-03:** Return, Reject, Escalation, and Rework are defined and distinguishable.
- **S8-AC-04:** All five handoffs have minimum required payloads and acceptance dispositions.
- **S8-AC-05:** Operational status, claim-validation outcome, and handoff disposition are separate.
- **S8-AC-06:** Source eligibility and inherited limitations are defined and task-specific.
- **S8-AC-07:** Acceptance evidence is defined for every Agent and existing quality gate.
- **S8-AC-08:** A4 independence and coverage boundaries are explicit.
- **S8-AC-09:** A5 integration restrictions and upstream acceptance dependencies are explicit.
- **S8-AC-10:** Human Authority reserved decisions are explicit.
- **S8-AC-11:** Known source/corpus limitations from Stage 7 are preserved.
- **S8-AC-12:** No conflict is introduced with Stage 6, Stage 7, or frozen architecture.
- **S8-AC-13:** The document does not claim implementation, operational execution, complete source coverage, or readiness for operational educational execution; prompts, code, and execution remain out of scope.

## 16. Explicit Non-Claims and Readiness Boundary

This specification does not establish that:

- any Agent has been implemented, configured, or executed;
- any real handoff has been successfully completed;
- operational QC has been performed;
- the source corpus or educational coverage is complete;
- extraction, validation, or integration work has been completed;
- the system is ready for operational educational execution;
- detailed Agent prompts or implementation specifications are automatically authorized as the next stage.

Any subsequent readiness decision, prompt engineering, implementation planning, or execution authorization must be handled as a separate, explicitly approved stage.

## 17. Stage 8 Completion and Closure Conditions

Stage 8 may be considered complete only after:

1. The integrated document passes a final consistency and completeness review.
2. Human Authority reviews and explicitly approves the final document.
3. The approved final file is uploaded to the TOS GitHub repository.
4. The user confirms the upload.

Until all four conditions are met, the status remains **OPEN**. No next stage is initiated automatically.

---

## 18. Final Review Record and Approval Boundary

The final consistency review confirms that this specification:

- preserves the approved five-Agent architecture and the Stage 6 gate structure;
- defines common and Agent-specific operational contracts, minimum handoff payloads, task/input eligibility, stop conditions, and rework paths;
- separates operational status, claim-validation outcome, handoff disposition, and quality acceptance;
- preserves the Stage 7 readiness boundary and all known source/corpus limitations;
- does not authorize prompts, implementation, operational execution, or a subsequent stage.

This is the finalized Stage 8 document prepared for Human Authority review. **Document finalization is not equivalent to Human Authority approval, GitHub upload, or Stage 8 closure.** Those remain pending until explicitly completed and confirmed.

---

**End of Final Document — Human Authority Approval and GitHub Upload Pending**
