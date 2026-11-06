# TOS — Stage 10.3: Task-Level Acceptance Criteria v1.0

**Document ID:** TOS-S10.3-TLAC  
**Version:** 1.0  
**Status:** FINAL — READY FOR GITHUB UPLOAD  
**Stage:** 10.3 — Task-Level Acceptance Criteria  
**Predecessor:** Stage 10.2 — Representative Input Package v1.0  
**Next authorized stage:** S10.4 — Canonical Document-Version Consistency  
**Authority:** Human Authority / System Owner

---

## 1. Purpose

This document defines task-level acceptance criteria for the five approved Educational Agents. It establishes how task outputs are assessed, what evidence is required, how incomplete or uncertain information is handled, and how task acceptance relates to existing Handoff Contracts and Quality Gates.

The purpose is to make acceptance decisions explicit, evidence-based, traceable, and bounded by the authorized task scope.

This document defines acceptance requirements. It does not execute Agents, validate ICT educational content, establish actual source accessibility, or demonstrate operational readiness.

## 2. Authority and Architectural Boundaries

### 2.1 Roles

- **Human Authority / System Owner:** retains final authority over architecture, acceptance of project documents, reserved decisions, exceptions, and authorized changes.
- **Project Designer / System Architect:** defines and maintains architectural coherence, stage boundaries, and project specifications.
- **Educational Agents A1–A5:** operate only within their approved responsibilities and authorized task scopes.

Orchestration / Workflow Control remains a system control mechanism, not a sixth Agent.

### 2.2 Approved Agents

1. **A1 — Source Inventory & Reconciliation**
2. **A2 — Educational Review & Extraction**
3. **A3 — Evidence & Knowledge Validation**
4. **A4 — Independent QC & Coverage**
5. **A5 — Knowledge Integration & Learning Record**

No Agent is added, removed, or reassigned by this document.

### 2.3 Existing Quality Gates

- **G1 — Source Readiness**
- **G2 — Educational Record and Validation Readiness**
- **G3 — Independent QC and Coverage Acceptance**
- **G4 — Controlled Knowledge Integration**

The existing Gate ownership, purposes, and authority boundaries remain unchanged.

### 2.4 Explicit Exclusions

This document does not authorize:

- Detailed ICT lesson research or lesson-level educational analysis.
- Actual execution or deployment of any Agent.
- Prompt Engineering or writing individual Agent prompts.
- Coding, software implementation, database, API, or infrastructure design.
- Changes to frozen architecture, existing Handoff Contracts, or Quality Gates.
- Changes to the approved Agent count or responsibilities.
- Unrestricted Knowledge Base modification or automatic doctrine/policy decisions.
- Claims of complete ICT source coverage, corpus-wide QC, or completed Knowledge Integration.

## 3. Source Documents and Precedence

This specification is bounded by the approved project artifacts, particularly:

- Stage 6 — Educational Agent System Design v1.0.
- Stage 8 — Educational Agent Operational Specification & Handoff Contracts v1.0.
- Stage 9 — Educational Agent Pre-Implementation Readiness Verification v1.0.
- Stage 10.1 — Bounded Educational Agent Task Definition v1.0.
- Stage 10.2 — Representative Input Package v1.0.

The approved five-Agent architecture, Stage 8 Handoff Contracts, and G1–G4 remain authoritative. This document adds task-level acceptance criteria; it does not replace or silently amend those artifacts.

Where a conflict appears, the conflict must be recorded and referred to Human Authority through the existing escalation path. An Agent must not resolve an architectural or authority conflict by inventing a new rule.

## 4. Acceptance Model

### 4.1 Three Distinct Layers

The following concepts must remain separate:

| Layer | Meaning | Governing model |
|---|---|---|
| Task Acceptance Result | Whether the defined task output meets its applicable acceptance criteria | S10.3: PASS / FAIL / INCOMPLETE / BLOCKED |
| Operational Status | Operational state of an Agent stage or output | Existing Stage 8: PASS / PARTIAL / FAIL / UNVERIFIED / BLOCKED |
| Handoff Disposition | Receiving party's explicit decision on a transmitted handoff | Existing Stage 8 dispositions |

A Quality Gate decision is a separate controlled decision under G1–G4. A task result does not automatically constitute a Gate decision.

### 4.2 Task-Level Acceptance Results

| Result | Definition | Required treatment |
|---|---|---|
| **PASS** | Every applicable mandatory criterion is satisfied and supported by sufficient, traceable evidence. | Task may be recorded as accepted within its defined scope. |
| **FAIL** | Evidence establishes that one or more mandatory criteria have not been met, or a prohibited action/claim has occurred. | Task is not accepted; record the failed criteria and use the existing return, rework, reject, or escalation route as applicable. |
| **INCOMPLETE** | Required output, assessment, or evidence is missing or unfinished, and the available information does not establish a substantive failure. | No acceptance is issued; identify the missing elements and next action. |
| **BLOCKED** | A necessary dependency, access condition, approved input, or reserved decision prevents valid task completion or assessment. | Stop the affected work; identify the blocking condition and route it through existing controls. |

### 4.3 Decision Rules

1. PASS requires all applicable mandatory criteria to pass.
2. A mandatory criterion cannot be treated as satisfied merely because its evidence is absent.
3. Missing evidence does not automatically prove that the underlying educational claim is false.
4. FAIL requires an evidenced nonconformity, not merely uncertainty.
5. INCOMPLETE is used when the task or evidence package is unfinished without an established substantive failure.
6. BLOCKED is used when a necessary condition prevents valid progress or assessment.
7. If both a substantive failure and an unrelated blocker exist, the report must record both; the task must not be represented as PASS. The primary result must reflect the condition that currently prevents acceptance, with all secondary conditions disclosed.
8. Acceptance applies only to the identified Task ID, version, scope, output version, and applicable criteria version.
9. A PASS for one task does not establish workstream completion, full source coverage, or acceptance of another task or Gate.
10. The task result must not overwrite the operational status or Handoff Disposition.

### 4.4 Relationship to Stage 8 Statuses and Handoff Dispositions

Stage 8 statuses remain available and unchanged:

- PASS
- PARTIAL
- FAIL
- UNVERIFIED
- BLOCKED

The four S10.3 Acceptance Results are an additional evaluation classification, not a replacement.

Stage 8 Handoff Dispositions remain unchanged:

- ACCEPT
- ACCEPT WITH LIMITATIONS
- RETURN
- REJECT
- ESCALATE

An ACCEPT or ACCEPT WITH LIMITATIONS disposition does not independently establish that all task criteria or a Quality Gate have passed. Likewise, mere transmission or receipt of a handoff is not acceptance.

An INCOMPLETE or BLOCKED task cannot be converted to PASS through an ACCEPT WITH LIMITATIONS handoff disposition. Any permitted limited acceptance must remain within the rules and authority already established by Stage 8 and the applicable Gate.

## 5. Common Acceptance Criteria

The following criteria apply to every Agent task where relevant. Applicability must be recorded; a criterion may be marked **Not Applicable** only when its reason is explicit and consistent with the approved task scope. Mandatory criteria cannot be waived by the Agent being assessed.

| ID | Criterion | Verification requirement | Minimum evidence |
|---|---|---|---|
| COM-01 | Scope Compliance | Compare completed work with the authorized Task ID, scope, exclusions, and stop conditions. | Approved task definition and output scope statement. |
| COM-02 | Input Traceability | Verify that required inputs and their versions are identified and traceable. | Input references, IDs, and version identifiers. |
| COM-03 | Output Identity | Verify that the output has a stable identity and can be distinguished from other tasks or versions. | Task ID, output ID, Agent identity, and version. |
| COM-04 | Required Output Coverage | Compare the delivered output against the expected output components in the task definition and applicable input view. | Output inventory or structured completion report. |
| COM-05 | Evidence Traceability | Verify that material findings and claims link to identifiable supporting references where required. | Evidence IDs, source locators, and relationship references. |
| COM-06 | Limitation Disclosure | Verify that known limitations, unresolved issues, and unavailable information are disclosed. | Limitation and exception record. |
| COM-07 | Uncertainty Preservation | Verify that Unknown, Inference, Hypothesis, and unverified information are not presented as established facts. | Classification labels and supporting rationale. |
| COM-08 | Conflict Disclosure | Verify that known conflicting references or findings are recorded and not silently resolved. | Conflict references and disposition or next action. |
| COM-09 | Version Integrity | Verify that input, output, and applicable criteria versions are identifiable. | Version references and change record where applicable. |
| COM-10 | Handoff Compliance | Verify preservation of Stage 8 handoff elements: Identity, Traceability, Status, Completeness, and Action. | Handoff record and explicit disposition. |
| COM-11 | Authority Compliance | Verify that the Agent has not made a decision reserved to Human Authority or another role. | Decision references and escalation record where applicable. |
| COM-12 | Unsupported-Claim Prevention | Inspect material conclusions for claims lacking the required source or evidence basis. | Claim-to-source/evidence mapping and review findings. |
| COM-13 | Next-Action Clarity | Verify that unresolved work has an identified next action or is explicitly recorded as having no authorized next action. | Action, owner/role, dependency, or escalation reference. |

A common criterion is passed only when its verification requirement is met for the applicable scope. A general statement of compliance without supporting references is not sufficient evidence.

## 6. Agent-Specific Acceptance Criteria

### 6.1 A1 — Source Inventory & Reconciliation

**Purpose:** Establish a traceable and bounded source inventory/reconciliation result and determine source readiness within the assigned task.  
**Related Gate:** G1 — Source Readiness.

| ID | Criterion | Verification requirement | Minimum evidence |
|---|---|---|---|
| A1-AC-01 | Source Identity | Verify that each in-scope source candidate has an identifiable source reference or is explicitly marked unidentified. | Source ID or candidate reference; title/identifier where available. |
| A1-AC-02 | Provenance | Verify that known origin and provenance information are recorded and unknown provenance is explicitly marked. | Origin/provenance references or explicit Unknown status. |
| A1-AC-03 | Registry Reconciliation | Verify that the source candidate's relationship to the authorized Registry entry is recorded without unauthorized Registry modification. | Registry reference and reconciliation result. |
| A1-AC-04 | Metadata Assessment | Verify that required and available metadata are distinguished from missing or unverified fields. | Metadata record and missing-field list. |
| A1-AC-05 | Accessibility Assessment | Verify that access was assessed and the result is recorded as accessible, inaccessible, or unverified as appropriate. | Access reference, observation, or explicit access limitation. |
| A1-AC-06 | Integrity Assessment | Verify that integrity observations are recorded and are not inferred solely from Registry presence or accessibility. | Integrity observations and their basis. |
| A1-AC-07 | Discrepancy Identification | Verify that known identity, metadata, version, or source discrepancies are listed with references. | Discrepancy entries and competing references. |
| A1-AC-08 | Eligibility Disposition | Verify that an explicit source eligibility disposition is provided for the assigned task. | Eligibility status, scope, and rationale. |
| A1-AC-09 | Eligibility Rationale | Verify that the disposition is supported by the recorded source identity, provenance, accessibility, integrity, and limitations relevant to the task. | Linked source record fields and rationale. |
| A1-AC-10 | Limitation Preservation | Verify that unresolved discrepancies and access limitations are carried into the handoff. | Limitation list and handoff reference. |
| A1-AC-11 | Next Action | Verify that each unresolved source issue has an appropriate next action, return, stop, or escalation route where known. | Action/disposition record. |

**A1 acceptance boundary**

A1 does not validate educational truth, validate educational claims, perform educational extraction, or modify the approved Registry without authorization. Source identity, Registry presence, and accessibility are not interchangeable and do not independently establish educational eligibility.

A1 task acceptance does not automatically mean G1 has passed. G1 remains a separate Gate decision under its existing rules.

### 6.2 A2 — Educational Review & Extraction

**Purpose:** Produce structured, source-attributed Educational Records within the authorized review scope.  
**Related Gate:** G2 — Educational Record and Validation Readiness, in coordination with A3 requirements.

| ID | Criterion | Verification requirement | Minimum evidence |
|---|---|---|---|
| A2-AC-01 | Source Eligibility Reference | Verify that the task uses the applicable A1 source record and explicit eligibility disposition. | A1 source record and handoff references. |
| A2-AC-02 | Authorized Review Scope | Verify that reviewed material is within the authorized Source Unit, scope, and exclusions. | Task scope and reviewed-section inventory. |
| A2-AC-03 | Record Identity | Verify that each produced Educational Record has a stable, unique record identifier within the task context. | Educational Record IDs and output inventory. |
| A2-AC-04 | Source Locator | Verify that each extracted record has a locator sufficient to return to the relevant source location, where the source permits. | Timestamp, page, section, segment, or equivalent locator. |
| A2-AC-05 | Attribution | Verify that extracted content is attributed to the correct source and location. | Source reference and locator per record. |
| A2-AC-06 | Classification | Verify that each record uses the applicable approved classification requirements and records unresolved classification where necessary. | Classification value and applicable rule/reference. |
| A2-AC-07 | Source/Interpretation Separation | Verify that source content, interpretation, inference, and hypothesis are distinguishable. | Record fields or labels showing the distinction. |
| A2-AC-08 | Extraction Traceability | Verify that each record can be traced to the extracted source content and that no unsupported content has been inserted. | Source-to-record mapping and extracted-content reference. |
| A2-AC-09 | Ambiguity Disclosure | Verify that material ambiguities are recorded rather than silently normalized. | Ambiguity note and source locator. |
| A2-AC-10 | Contradiction Disclosure | Verify that observed contradictions within the reviewed material are recorded with references. | Contradiction note and relevant locators. |
| A2-AC-11 | Scope Completion Accounting | Verify that the report distinguishes reviewed, not reviewed, inaccessible, and otherwise unprocessed portions of the authorized scope. | Scope coverage inventory and exclusions. |
| A2-AC-12 | Limitation Handoff | Verify that extraction limitations and unresolved items are preserved for A3. | Limitation record and A2→A3 handoff. |

**A2 acceptance boundary**

Extraction completion is not evidence validation, independent QC acceptance, or Knowledge Integration. A2 must not fill gaps in source material with unsupported assumptions or unrelated general knowledge.

A2 task acceptance does not automatically mean G2 has passed; G2 must account for the applicable A2 and A3 requirements.

### 6.3 A3 — Evidence & Knowledge Validation

**Purpose:** Assess claims against identified evidence and applicable criteria while preserving uncertainty and unresolved conflicts.  
**Related Gate:** G2 — Educational Record and Validation Readiness.

| ID | Criterion | Verification requirement | Minimum evidence |
|---|---|---|---|
| A3-AC-01 | Claim Identification | Verify that each assessed claim has a stable identifier and an identifiable claim statement. | Claim ID and claim text/reference. |
| A3-AC-02 | Record Traceability | Verify that each claim links to its originating Educational Record or other authorized origin. | Claim-to-record reference. |
| A3-AC-03 | Evidence Mapping | Verify that relevant supporting and contradictory evidence references are mapped to the claim. | Claim-to-evidence mapping. |
| A3-AC-04 | Source Attribution | Verify that evidence origin and source attribution are identifiable. | Source IDs, locators, and provenance references. |
| A3-AC-05 | Supporting Evidence Review | Verify that identified supporting evidence is assessed and not merely listed. | Evidence reference and assessment rationale. |
| A3-AC-06 | Contradictory Evidence Review | Verify that known contradictory evidence is disclosed and considered where available. | Contradictory evidence references and treatment. |
| A3-AC-07 | Criteria Identification | Verify that the applicable validation criteria and their versions are identified. | Criteria ID and version. |
| A3-AC-08 | Criteria Application | Verify that the rationale shows how the applicable criteria were applied to the evidence. | Criterion-to-finding explanation. |
| A3-AC-09 | Evidence Sufficiency | Verify that evidence sufficiency is explicitly assessed and limitations are stated. | Sufficiency assessment and rationale. |
| A3-AC-10 | Validation Outcome | Verify that each assessed claim receives an outcome consistent with the available evidence. | Outcome and linked evidence/rationale. |
| A3-AC-11 | Uncertainty Preservation | Verify that uncertainty, alternative interpretations, and unverified propositions remain visible. | Uncertainty labels and explanation. |
| A3-AC-12 | Validation Rationale | Verify that another reviewer can trace the conclusion from claim through criteria to evidence. | Claim/evidence/criteria chain. |
| A3-AC-13 | Unresolved Conflict Disclosure | Verify that unresolved evidence conflicts are explicitly recorded and not represented as resolved. | Conflict record and next action. |

**Minimum validation outcomes**

The validation record must distinguish at least:

- **SUPPORTED**
- **UNSUPPORTED**
- **INSUFFICIENT_EVIDENCE**

These are validation outcomes, not Task Acceptance Results or Stage 8 Operational Statuses.

**A3 acceptance boundary**

A source assertion is not automatically independent proof. UNSUPPORTED does not, by itself, prove the opposite claim. INSUFFICIENT_EVIDENCE does not establish truth or falsity. A3 must not suppress contradictory evidence, manufacture support, or unilaterally establish doctrine or policy.

A3 task acceptance does not constitute independent QC acceptance under G3.

### 6.4 A4 — Independent QC & Coverage

**Purpose:** Independently assess the in-scope outputs against the approved scope, criteria, evidence, and quality requirements.  
**Related Gate:** G3 — Independent QC and Coverage Acceptance.

| ID | Criterion | Verification requirement | Minimum evidence |
|---|---|---|---|
| A4-AC-01 | Independence | Verify that A4 did not produce or own corrective rewriting of the work it assesses. | Role/task history and reviewed-output provenance. |
| A4-AC-02 | Assessment Scope | Verify that the QC scope, exclusions, and expected coverage are explicitly identified. | Approved scope and QC scope statement. |
| A4-AC-03 | Input and Version Verification | Verify that A4 identifies the exact A1–A3 outputs, source/evidence references, and criteria versions reviewed. | Input manifest and version references. |
| A4-AC-04 | Traceability QC | Independently sample or inspect the required source-to-record and claim-to-evidence links within the approved assessment method. | QC findings with reviewed IDs and locators. |
| A4-AC-05 | Evidence Consistency | Verify that material evidence relationships, contradictions, and validation conclusions are internally consistent with the references examined. | Evidence consistency findings and references. |
| A4-AC-06 | Classification QC | Verify that classifications are consistent with the approved classification requirements and evidence available. | Reviewed records, criteria references, and findings. |
| A4-AC-07 | Completeness QC | Compare delivered items against the approved expected-output inventory for the assessed scope. | Expected-versus-observed item inventory. |
| A4-AC-08 | Missing-Item Detection | Identify missing records, references, evidence, or required output components within the assessed scope. | Missing-item list with scope references. |
| A4-AC-09 | Conflict Detection | Identify material conflicts, inconsistencies, or unresolved issues visible in the reviewed material. | Finding IDs and conflicting references. |
| A4-AC-10 | Coverage Assessment | State the approved assessment scope and the actual scope examined; identify unexamined or excluded portions. | Scope-to-assessed-item mapping. |
| A4-AC-11 | Limitation Assessment | Verify that limitations affecting QC confidence or coverage are disclosed. | Limitation and access record. |
| A4-AC-12 | Corrective Action Identification | Link each actionable deficiency to the responsible upstream role or authorized escalation path. | Finding, responsible role, and action reference. |
| A4-AC-13 | Independent Disposition | Provide an explicit, reasoned QC disposition based on the approved criteria and evidence reviewed. | QC report and criterion-level findings. |
| A4-AC-14 | Coverage Claim Boundary | Ensure every coverage statement is limited to the actual approved scope, records, sources, and criteria examined. | Coverage basis and scope denominator/definition. |

**A4 independence requirements**

A4 must:

- Not produce the work it independently assesses.
- Not own corrective rewriting of the work it assesses.
- Not blindly adopt A3 conclusions.
- Not change approved assessment criteria.
- Not rely solely on summaries when direct source or evidence access is necessary.
- Record access, criteria, or independence limitations and use the applicable stop, return, or escalation route.
- Never manufacture an acceptance decision when required evidence or independence is insufficient.

**Coverage requirements**

Coverage must be reported against an explicitly defined approved scope. The report must identify what was assessed, what was not assessed, and the basis used to determine coverage.

A percentage must not be reported unless the denominator, counting unit, inclusion/exclusion rules, and underlying evidence are defined and reproducible. A coverage statement for one Source Unit or Task must not be generalized to the complete Registry, ICT corpus, or educational workstream.

**A4 / G3 boundary**

A4 task acceptance and a G3 acceptance decision are related but distinct. G3 may be recorded as accepted only through the existing G3 decision process, with explicit scope, criteria, evidence, rationale, and accepted/excluded item references. A QC report's existence alone is not G3 acceptance.

### 6.5 A5 — Knowledge Integration & Learning Record

**Purpose:** Prepare and record only eligible, accepted upstream outputs for controlled Knowledge Integration under G4.  
**Related Gate:** G4 — Controlled Knowledge Integration.

| ID | Criterion | Verification requirement | Minimum evidence |
|---|---|---|---|
| A5-AC-01 | Upstream Eligibility | Verify that each proposed input is within the applicable upstream eligibility and acceptance conditions. | Upstream output IDs, versions, and eligibility references. |
| A5-AC-02 | G3 Acceptance Verification | Verify an explicit, valid G3 acceptance reference covering the exact records and versions proposed for integration. | G3 decision ID, scope, accepted item list, and version references. |
| A5-AC-03 | Source Traceability | Verify that each integrated knowledge record retains its source references. | Source IDs and locators. |
| A5-AC-04 | Evidence Traceability | Verify that evidence references remain linked to the relevant knowledge records. | Evidence IDs and relationship references. |
| A5-AC-05 | Validation Traceability | Verify that validation outcomes, criteria references, and relevant rationale are preserved. | A3 record and criteria references. |
| A5-AC-06 | Classification Preservation | Verify that source/content classifications and validation outcomes are not silently changed during integration. | Before/after field comparison or integration record. |
| A5-AC-07 | Limitation Preservation | Verify that limitations, uncertainty, unresolved conflicts, and exclusions remain visible. | Carried-forward limitation references. |
| A5-AC-08 | Integration Rationale | Verify that the integration action and its basis are recorded. | Integration rationale and applicable rule reference. |
| A5-AC-09 | Knowledge Record Identity | Verify that each resulting record has a stable identity and version. | Knowledge Record ID and version. |
| A5-AC-10 | Change History | Verify that changes are distinguishable and traceable to their source and authority. | Change record and prior-version reference. |
| A5-AC-11 | Governance Status | Verify that governance, activation, and pending decision states are explicit where applicable. | Governance status and decision references. |
| A5-AC-12 | G4 Compliance | Verify that the proposed integration complies with approved integration rules and reserved Human Authority decisions. | G4 assessment and required approvals. |

**Mandatory G3 entry condition**

A5 must not integrate an item as accepted knowledge unless a valid, explicit G3 acceptance reference covers the exact item and version proposed for integration.

A4's report, a handoff receipt, a prior version's G3 decision, or an ambiguous acceptance statement is not a substitute for a valid G3 acceptance reference.

If the G3 reference is absent, invalid, mismatched, incomplete, or does not cover the proposed item/version, A5 must stop the affected integration and use the applicable INCOMPLETE, BLOCKED, return, or escalation route. A5 must not self-certify G3.

A5 must not convert PARTIAL, FAIL, UNVERIFIED, BLOCKED, or otherwise ineligible upstream outputs into accepted knowledge. Any permitted handling of limited or excluded material must follow existing approved governance and must not mislabel it as G3-accepted knowledge.

**A5 / G4 boundary**

G3 acceptance is necessary but not sufficient for integration. G4 requirements and reserved Human Authority decisions still apply. Integration does not independently prove the truth of the underlying knowledge.

## 7. Evidence Requirements

### 7.1 Evidence Categories

| Evidence category | Purpose |
|---|---|
| Source Evidence | Establish source identity, origin, and source location. |
| Input Evidence | Establish which authorized inputs and versions were received. |
| Output Evidence | Establish which required records, reports, or components were delivered. |
| Traceability Evidence | Establish relationships among source, record, claim, evidence, and output. |
| Validation Evidence | Establish criteria applied, evidence sufficiency, outcome, and rationale. |
| QC Evidence | Establish independent review scope, method, findings, and disposition. |
| Gate Evidence | Establish the explicit decision and scope of G1, G2, G3, or G4. |
| Change Evidence | Establish what changed, between which versions, and under whose authority. |

### 7.2 Minimum Evidence Record

Where applicable, each evidence reference must identify:

1. Evidence ID or stable reference.
2. Origin or source.
3. Locator sufficient to retrieve or inspect the evidence.
4. Criterion or finding it supports.
5. Relevant task/output/version.
6. Access or verification status.
7. Known limitations or uncertainty.
8. Change/version information where applicable.

Not every evidence type requires every field, but any omitted field must not undermine identification, retrieval, or assessment. If evidence cannot be uniquely identified or inspected where inspection is required, its limitation must be recorded and the affected criterion cannot be marked PASS.

### 7.3 Evidence Sufficiency

Evidence is sufficient only when it is relevant to the criterion, traceable to its origin, adequate for the specific conclusion, and consistent with material contrary evidence known within the assessed scope.

The presence of a reference is not proof that the reference supports the claim. Traceability is not the same as validity. A source assertion is not automatically independent corroboration.

## 8. Exception and Uncertainty Handling

The following conditions must remain distinct and visible throughout all Agent handoffs.

| Condition | Required treatment | Acceptance implication |
|---|---|---|
| **Missing** | Record the expected information that is absent; do not fabricate a value. | INCOMPLETE when required output/evidence is absent; BLOCKED if the missing item is a necessary external dependency. |
| **Unknown** | Preserve the unresolved status and identify what would be needed to determine it. | Cannot support PASS for a criterion requiring the unknown fact to be established. |
| **Conflicting** | Preserve competing references/findings and identify the unresolved conflict. | Affected criterion cannot PASS while the conflict prevents the required conclusion. |
| **Inaccessible** | Record failed or unverified access and the affected reference/scope. | BLOCKED when access is a necessary prerequisite; otherwise INCOMPLETE or UNVERIFIED operationally, as applicable. |
| **Insufficient Evidence** | State why the available evidence does not support the required determination. | No PASS for the affected criterion; do not infer that the claim is false. |

### 8.1 Required Distinctions

- **Missing** means expected information is absent from the relevant package or output.
- **Unknown** means the actual state has not been established.
- **Conflicting** means two or more references or findings are materially inconsistent.
- **Inaccessible** means a referenced item cannot be inspected under the current access conditions.
- **Insufficient Evidence** means available evidence does not meet the burden required for the specific assessment.

A condition may have more than one applicable label; the report must preserve each material condition rather than force a false single classification.

### 8.2 Downstream Preservation

Limitations and exception states must remain visible in downstream Agent views and records. They must not be removed merely because information is passed to another Agent.

An Agent may update an exception only when new evidence or an authorized decision supports the change. The previous state and reason for change must remain traceable.

### 8.3 Non-PASS Reporting

Every non-PASS Task Acceptance Result must include, at minimum:

- Reason for the result.
- Affected criterion IDs.
- Affected scope or records.
- Evidence references or missing evidence description.
- Required next action, where known.
- Responsible role or escalation destination, where known.

## 9. Quality Gate Compatibility

| Gate | Primary owner(s) | Acceptance focus under existing Gate |
|---|---|---|
| G1 — Source Readiness | A1 | Source identity, provenance, availability/accessibility, integrity, discrepancies, limitations, and eligibility. |
| G2 — Educational Record and Validation Readiness | A2 and A3 | Structured record completeness, source locators, attribution, claim-to-evidence relationships, validation status, uncertainty, and unresolved issues. |
| G3 — Independent QC and Coverage Acceptance | A4 | Independent assessment against approved scope and criteria, evidence consistency, completeness, coverage, findings, and explicit disposition. |
| G4 — Controlled Knowledge Integration | A5, with Human Authority retained for reserved decisions | Eligible accepted inputs, valid G3 reference, provenance/evidence preservation, approved integration rules, change history, and governance compliance. |

### 9.1 Gate Rules

1. A task PASS does not automatically pass its related Quality Gate.
2. A Gate decision must be explicit, traceable, and bounded to its approved scope.
3. No Agent may bypass its applicable Gate or unilaterally amend Gate criteria.
4. Existing Gate ownership and Handoff Contracts remain unchanged.
5. G3 acceptance is mandatory for A5's accepted Knowledge Integration.
6. G3 acceptance alone does not waive G4 requirements or Human Authority decisions.
7. A Gate decision for one scope/version cannot be assumed to cover another scope/version.

## 10. Compatibility with Stage 10.2 Representative Input Package

S10.2 defines the Canonical Representative Input Package and Agent-specific Input Views. S10.3 uses those inputs as the context in which task acceptance is assessed; it does not redesign the package.

| S10.2 section | S10.3 acceptance use |
|---|---|
| P1 — Source Identity & Metadata | Verify identity, provenance, metadata, and source references. |
| P2 — Source Material References | Verify source locators, access state, and source material references. |
| P3 — Task Definition & Scope | Establish Task ID, assigned Agent, authorized scope, exclusions, expected output, dependencies, stop conditions, and applicable criteria. |
| P4 — Evidence & Traceability Context | Assess evidence mapping, source locators, claim/record links, uncertainty, and evidence limitations. |
| P5 — Handoff & Workflow Context | Verify handoff identity, status, disposition, Gate reference, next action, and rework/escalation history. |
| P6 — Limitations & Exception Context | Preserve missing, unknown, conflicting, inaccessible, incomplete, unverified, and insufficient-evidence conditions. |

### 10.1 Input-to-Criterion Traceability

Each task must identify which P1–P6 elements are applicable to its criteria. A criterion must not be treated as operationally verifiable if the required input or evidence is not available.

If a representative input fixture does not contain the evidence needed to verify a criterion, the correct result is to record the limitation or unavailability. The fixture must not be supplemented with invented facts.

### 10.2 Fixture Boundary

The S10.2 representative fixture is **Illustrative / Non-Operational**. It may demonstrate package structure and criterion mapping. It does not demonstrate actual Agent execution, actual source accessibility, a real Task PASS, a Quality Gate decision, practical validation readiness, or completed Knowledge Integration.

## 11. Handoff Contract Compatibility

Existing Stage 8 Handoff Contracts remain authoritative. S10.3 does not create a new handoff, change the receiving Agent, or replace the existing handoff dispositions.

Every handoff must preserve the five existing elements:

1. Identity.
2. Traceability.
3. Status.
4. Completeness.
5. Action.

The receiving Agent must record an explicit disposition. Silence, transmission, or receipt is not acceptance.

The existing handoff sequence remains:

- A1 → A2
- A2 → A3
- A3 → A4
- A4 → A5
- A5 → TOS / authorized control process

Rework must be routed to the role responsible for the identified deficiency, consistent with existing Stage 8 rules. S10.3 does not require every issue to restart the entire Agent sequence.

## 12. Task Acceptance Record — Minimum Reporting Content

For an acceptance decision to be auditable, the task assessment record must identify, as applicable:

| Field | Required content |
|---|---|
| Task ID and version | Exact task identity. |
| Agent | Assigned Agent. |
| Input references | Applicable input package sections and versions. |
| Output references | Exact output IDs and versions assessed. |
| Scope | Authorized and assessed scope, including exclusions. |
| Criteria reference | S10.3 version and applicable criterion IDs. |
| Criterion results | PASS / FAIL / INCOMPLETE / BLOCKED or justified Not Applicable at criterion level, where the task assessment format supports it. |
| Evidence references | Evidence supporting each material criterion result. |
| Exceptions | Missing, Unknown, Conflicting, Inaccessible, and Insufficient Evidence conditions. |
| Overall Acceptance Result | One of PASS / FAIL / INCOMPLETE / BLOCKED. |
| Operational Status | Existing Stage 8 status, recorded separately. |
| Handoff Disposition | Existing Stage 8 disposition, recorded separately when a handoff occurs. |
| Gate reference | Applicable Gate decision reference, if one exists. |
| Findings and rationale | Reasons for the overall result and material criterion outcomes. |
| Next action | Rework, return, escalation, wait for dependency, or other authorized action. |
| Authority reference | Human Authority decision where required. |
| Change/version history | Prior version and changes where applicable. |

This is a minimum information requirement, not a technical file schema or implementation design. Detailed canonical document/version governance remains reserved for S10.4.

## 13. Boundary with S10.5 — Individual Agent Prompt Engineering

S10.3 defines **what must be achieved and how task outputs are assessed**. S10.5 will translate approved requirements into individual Agent prompts.

| Concern | S10.3 responsibility | S10.5 responsibility |
|---|---|---|
| Task acceptance requirements | Define | Implement in prompt instructions |
| Common and Agent-specific criteria | Define | Translate into Agent-specific guidance |
| Evidence expectations | Define | Instruct Agent how to produce and reference evidence |
| Acceptance result semantics | Define | Instruct Agent to report relevant status without conflating models |
| Exception handling requirements | Define required treatment | Translate into operational prompt behavior |
| A4 independence and G3 protection | Preserve as binding constraints | Encode as Agent-specific prompt constraints |
| A5 G3 prerequisite | Define as mandatory eligibility condition | Encode as a hard integration restriction |
| Prompt wording, structure, and techniques | Out of scope | In scope |

S10.3 does not write, test, or optimize Agent prompts. S10.5 must not weaken, omit, or reinterpret the approved acceptance criteria without an authorized change.

## 14. S10.3 Document Acceptance Criteria

This document is acceptable when all the following conditions are met:

| ID | Document-level acceptance criterion |
|---|---|
| S10.3-DAC-01 | Task-level acceptance criteria are defined for A1 through A5. |
| S10.3-DAC-02 | Common criteria are distinguished from Agent-specific criteria. |
| S10.3-DAC-03 | Each criterion has a verification requirement and minimum evidence expectation. |
| S10.3-DAC-04 | PASS / FAIL / INCOMPLETE / BLOCKED are defined for Task Acceptance. |
| S10.3-DAC-05 | Existing Stage 8 Operational Statuses and Handoff Dispositions remain distinct and unchanged. |
| S10.3-DAC-06 | Missing, Unknown, Conflicting, Inaccessible, and Insufficient Evidence are handled explicitly. |
| S10.3-DAC-07 | A4 independence and bounded coverage are preserved. |
| S10.3-DAC-08 | G3 acceptance is an explicit prerequisite for A5's accepted Knowledge Integration. |
| S10.3-DAC-09 | G1–G4 ownership and purposes remain unchanged. |
| S10.3-DAC-10 | S10.2 P1–P6 compatibility and illustrative fixture boundaries are preserved. |
| S10.3-DAC-11 | Existing Stage 8 Handoff Contracts and five handoff elements are preserved. |
| S10.3-DAC-12 | The boundary with S10.5 Prompt Engineering is explicit. |
| S10.3-DAC-13 | No new Agent, Gate, architectural layer, or technical implementation design is introduced. |
| S10.3-DAC-14 | No actual Agent execution, source accessibility, corpus-wide coverage, or operational success is claimed. |
| S10.3-DAC-15 | Human Authority decision rights and the required stage closure workflow are preserved. |

## 15. Final Architectural Determination

The S10.3 Task-Level Acceptance Criteria specification establishes a bounded and traceable acceptance framework for the five approved Educational Agents.

It distinguishes Task Acceptance Results from existing Operational Statuses, Handoff Dispositions, and Quality Gate decisions. It defines common and Agent-specific criteria, evidence expectations, exception handling, independent QC requirements, G3 protection for A5, and compatibility with the S10.2 input package and Stage 8 Handoff Contracts.

No Agent, Quality Gate, or architectural layer is added or changed.

No actual Agent execution or educational corpus acceptance is claimed.

**Document status:** FINAL — READY FOR GITHUB UPLOAD.  
**Stage status:** AWAITING GITHUB UPLOAD AND EXPLICIT HUMAN AUTHORITY CONFIRMATION.  
**Next stage:** S10.4 — Canonical Document-Version Consistency, only after S10.3 closure is confirmed.

---

**End of Document — TOS Stage 10.3 — Task-Level Acceptance Criteria v1.0**
