# TOS — Operational Requirements Elaboration
## Research & Knowledge Validation First
### Version 1.0

**Document ID:** TOS-ORE-RKV-001  
**Status:** FINAL — READY FOR GITHUB UPLOAD  
**Document Type:** Non-Architectural Operational Requirements  
**Workstream:** Operational Requirements Elaboration — Minimum Necessary Detail  
**Scope Priority:** Research & Knowledge Validation First  
**Supersedes:** Nothing  
**Architecture Stage:** None — this document does not create a new architecture stage

---

## 1. Purpose

This document formalizes the minimum additional operational requirements identified after the **TOS Operational Execution Readiness & Specification Sufficiency Assessment v1.0**.

Its purpose is to make the first intended TOS use cases sufficiently explicit for controlled research, source-based knowledge capture, claim validation, evidence review, market-study recording, and learning-state control.

This is a bounded requirements-elaboration document. It does not redesign the TOS architecture, replace the Operational Specification Baseline, or reopen any frozen stage.

The work prioritizes accuracy, traceability, epistemic discipline, practical buildability, and the user's authority over knowledge-state and governance decisions.

## 2. Status and Interpretation

The requirements in this document are **operational requirements for the defined initial scope**. They are not proof that:

- any ICT/Michael Huddleston teaching or interpretation is true;
- any market behavior, order-flow explanation, algorithmic mechanism, or causal claim has been established;
- software has implemented these requirements correctly;
- the system is ready for live trading or autonomous execution;
- all future operational requirements have been identified.

The current readiness disposition remains **READY WITH EXPLICIT LIMITATIONS** for the bounded scope. This document reduces identified specification gaps; it does not remove the limitations recorded in the readiness assessment.

## 3. Relationship to Existing TOS Baseline

This document supplements, and does not replace, `TOS_Operational_Specification_Baseline_v1.0.md`.

It elaborates only the minimum details materially required by the five initial use cases. Existing operational contracts and controls remain in force, including:

- **OP-01 — Capture & Structure Knowledge**
- **OP-02 — Validate Knowledge Claim**
- **OP-12 — Convert Observations into Learning**
- **OP-13 — Govern Resulting Knowledge Change**
- **CR-01 through CR-08**, including object/version/context linkage, separation of decisions and state transitions, reevaluation after material changes, permission validity at use, explicit evidence/trace gaps, safe retry, treatment of unknown outcomes, and historical integrity.

Existing architecture boundaries remain authoritative. In particular:

- D9 traceability supports reconstruction and review; traceability alone does not prove truth.
- D10 separates source teaching, evidence, inference, hypothesis, validation, learning, and knowledge state.
- D11 governs knowledge evolution; it does not independently establish source truth or validation.
- D12 controls qualification and context-specific applicability.
- D13 determines operational outcomes within its defined boundary; a determination is not runtime permission.
- Runtime, recovery, identity/correlation, and other frozen controls retain their existing boundaries.

No requirement in this document authorizes runtime activity, trading execution, knowledge activation, or governance approval by implication.

## 4. Scope

### 4.1 Included initial use cases

- **UC-01 — Capture and structure source teaching**
- **UC-02 — Validate ICT-related claims**
- **UC-03 — Review supporting and contradictory evidence**
- **UC-04 — Record market-study findings**
- **UC-05 — Control learning and knowledge status**

The selected initial orientation is **Research & Knowledge Validation First**. The initial scope is deliberately limited to research and knowledge work, rather than live trading execution.

### 4.2 Excluded from this workstream

Unless a later, explicitly approved scope extension demonstrates a material need, this document does not define:

- live trade execution or autonomous order placement;
- full runtime permission rules for trading;
- agent roles, agent-team topology, or agent design;
- prompt engineering;
- coding or software implementation;
- API, database, infrastructure, or deployment design;
- a new architecture stage or a new system state model;
- a complete domain taxonomy for every possible ICT concept or market claim.

These exclusions do not declare such matters unnecessary for all future TOS use. They are outside the present bounded scope.

## 5. Epistemic and Operational Rules

### ER-01 — Source attribution is separate from claim validity

A source-attribution question asks whether a source actually teaches or states a particular idea. A broader claim-validation question asks whether the idea is supported under suitable evidence and criteria.

Confirmation that a source states an idea does **not** establish that the idea is empirically true, causally established, or universally applicable.

### ER-02 — Preserve the distinction between evidence and interpretation

Records must distinguish, where applicable:

- what a source explicitly states;
- what was directly observed in data;
- an analyst's interpretation;
- an inference;
- a hypothesis or research question;
- an evaluation result;
- a learning record;
- the governed state of a knowledge record.

An analyst's perception or confidence is not, by itself, evidence sufficient to establish a claim.

### ER-03 — Match evidence to the claim

Evidence must be assessed for relevance to the exact claim, its type, its context, and the criteria being applied. Evidence of one phenomenon must not be silently treated as evidence of a different phenomenon.

For example, price behavior alone must not be represented as direct observation of order flow. Repeated patterns or co-occurrence alone do not establish an algorithmic mechanism or causality.

### ER-04 — Make uncertainty explicit

Missing, inaccessible, ambiguous, incomplete, low-quality, or contradictory evidence must be recorded as a limitation when material. Missing information must not be filled by guesswork.

### ER-05 — Preserve historical integrity

Corrections, new evidence, revised interpretations, and reevaluations must remain traceable to the earlier record or version. Historical findings must not be silently overwritten.

### ER-06 — Separate learning from validation and activation

A market observation or learning record does not automatically validate a claim, approve a knowledge change, create a new knowledge version, or activate knowledge. Those decisions remain subject to the existing validation and governance boundaries.

## 6. Claim Classification for the Initial Scope

The following operational classification is a practical starting set for the five use cases. It is **not** presented as an official, complete, or source-authored taxonomy of ICT/Michael Huddleston concepts. A claim may need more than one classification when it makes multiple distinct assertions; those assertions should be separated where needed for meaningful evaluation.

### CT-01 — Source-attribution claim

**Question:** Did the identified source actually teach, state, or demonstrate the attributed idea?

Minimum requirements:
- identify the source and provenance;
- identify the relevant passage, video segment, lesson, or other reviewable location where available;
- preserve the exact statement or a faithful, clearly labeled summary;
- record access, context, transcription, translation, or completeness limitations;
- distinguish direct source content from later interpretation.

If the original source cannot be reviewed or provenance is insufficient, source attribution must remain unconfirmed or explicitly limited.

### CT-02 — Interpretation claim

**Question:** Is this interpretation reasonably supported by the source content?

Minimum requirements:
- link to the underlying source content;
- state the interpretation separately from the source's explicit words;
- provide reviewable reasoning connecting source content to interpretation;
- record plausible ambiguity, alternative readings, or contradictory source material;
- do not label an interpretation as direct teaching unless that attribution is separately supported.

### CT-03 — Market-behavior claim

**Question:** Did the specified behavior occur in the market data and context being studied?

Minimum requirements:
- identify the instrument or market, relevant data, time period, and context as applicable;
- define the event or behavior in terms that another reviewer can apply;
- retain the observations or references needed to review the conclusion;
- disclose data gaps, sampling limitations, and relevant contextual constraints;
- separate observed behavior from its proposed explanation.

A claim about one sample or context must not automatically be generalized to all instruments, sessions, conditions, or periods.

### CT-04 — Order-flow claim

**Question:** Does the available evidence support the specific order-flow assertion being made?

Minimum requirements:
- define exactly what order-flow proposition is being claimed;
- identify the evidence source and whether it directly measures order flow or only provides a proxy;
- explain why that evidence is relevant to the proposition;
- record limitations and alternative explanations;
- do not describe price-only evidence as direct order-flow observation.

The mere appearance of a price pattern does not, by itself, establish a particular order-flow event.

### CT-05 — Algorithmic or causal claim

**Question:** Does the evidence establish the proposed mechanism or causal relationship, rather than merely a repeated pattern or association?

Minimum requirements:
- state the proposed mechanism or causal proposition precisely;
- define what evidence would support or challenge it;
- assess whether the evidence can distinguish the proposed explanation from plausible alternatives;
- record counterevidence, limitations, and unresolved uncertainty;
- do not infer a proven algorithm or cause solely from repetition, visual similarity, timing coincidence, or co-occurrence.

If available evidence can establish only a recurring association, the result must not overstate it as proven mechanism or causation.

## 7. Claim-Specific Criteria and Evaluation Controls

### CRIT-01 — Criteria appropriate to the claim type

Every claim submitted for a completed validation must have criteria appropriate to its claim type. Criteria must be specific enough to guide a review and explain why the evidence supports, fails to support, or cannot resolve the claim.

### CRIT-02 — Criteria set before evaluation

The criteria used for an evaluation should be established before the result is determined. If criteria materially change during review, the change and its rationale must be traceable. A result obtained under changed criteria must not be presented as if the original criteria had been used unchanged.

### CRIT-03 — No completed validation without defined criteria

If the claim type or required criteria are unspecified, the validation must not be represented as complete. The missing requirement should be recorded and the review held incomplete or blocked under the existing process conventions.

This document does not introduce a new global process-state enumeration.

### CRIT-04 — Evaluation outcomes remain claim-appropriate

For hypothesis/inference evaluations under OP-02, retain the existing permitted results:

- `SUPPORTED`
- `UNSUPPORTED`
- `INSUFFICIENT_EVIDENCE`

Use the result that is justified by the evidence and applicable criteria. Do not add a new result state through this document.

`UNSUPPORTED` means the claim is not supported under the evaluated evidence and criteria; it does not necessarily prove the claim's negation. If the evidence cannot reasonably resolve the question, use `INSUFFICIENT_EVIDENCE`.

For source-attribution, empirical, or other claim types, use the existing process and record conventions appropriate to that claim. Do not assume that the three hypothesis/inference outcomes form a universal state model for every claim type.

### CRIT-05 — Evidence relevance and sufficiency

Reviewers must record why evidence is relevant to the claim and whether it is sufficient under the criteria. The existence or volume of evidence alone does not establish sufficiency.

### CRIT-06 — Supporting and contradictory evidence

Material supporting and contradictory evidence must both be considered and recorded. A contradiction must not be omitted merely because it conflicts with the preferred interpretation. The evaluation should explain how material counterevidence affects the result or why it remains unresolved.

### CRIT-07 — Unknown and incomplete cases

Where evidence is missing, inaccessible, ambiguous, incomplete, or inadequate to distinguish plausible explanations, record the limitation and avoid automatic acceptance or rejection. Incomplete validation is not equivalent to a negative finding.

### CRIT-08 — Re-evaluation after material change

Material new evidence, a changed claim, changed context, changed criteria, or a discovered source problem must trigger consideration of reevaluation under the existing baseline controls. The earlier result remains historically traceable; it must not be silently replaced.

## 8. Minimum Validation Record

For the initial use cases, a reviewable validation record must contain, or explicitly link to, at least:

1. **Claim identity:** claim ID, exact claim text, claim type, and relevant version.
2. **Evaluation criteria:** criteria appropriate to the claim type, including when they were established and traceable changes.
3. **Source and provenance:** source identifiers, locations, data references, or explicit statements of unavailable provenance.
4. **Evidence:** relevant supporting evidence, contradictory evidence, and contextual information needed to review the claim.
5. **Quality and limitations:** access limitations, data gaps, ambiguity, uncertainty, and material evidence-quality concerns.
6. **Evaluation:** result under the applicable existing conventions, rationale, and explanation of how the evidence relates to the criteria.
7. **Unresolved matters:** material contradictions, missing information, alternative explanations, or reasons the review cannot reach a stronger conclusion.
8. **Traceability:** links to the relevant knowledge record/version, evidence/context records, and review history as supported by the existing baseline.

This is a minimum information requirement, not a mandated database schema or implementation design. Existing record conventions should be reused where they already satisfy these requirements.

## 9. Minimum Learning Record

For UC-04 and UC-05, when an observation is converted into a learning record under OP-12, the record must contain, or explicitly link to, at least:

1. **Observation:** what was actually observed or recorded, expressed separately from explanation.
2. **Provenance:** source, market data, time/context, or other origin information needed for review.
3. **Interpretation:** the proposed meaning or explanation, explicitly labeled as interpretation rather than direct observation.
4. **Knowledge relationship:** links to relevant existing claims, hypotheses, validation records, or knowledge versions where applicable.
5. **Limitations and uncertainty:** missing data, evidence-quality limits, contradictory observations, and unresolved questions.
6. **Learning outcome:** what was learned at the current level of confidence, without overstating certainty.
7. **Next research step:** a research question or next check where useful, or a brief reason why no additional check is currently proposed.
8. **Traceability:** linkage to the relevant context and history under the existing baseline controls.

Do not invent missing observation details. If a field is unavailable or inapplicable, record that limitation where it materially affects interpretation.

A Learning Record is not, by itself, a validation decision, governance approval, knowledge-version creation, or activation decision.

## 10. Minimum Operational Requirements by Use Case

### UC-01 — Capture and structure source teaching

The process must:
- record source and provenance information;
- preserve the source statement or a faithful, clearly labeled representation;
- distinguish explicit teaching from interpretation, inference, and hypothesis;
- record ambiguity, contradictions, missing content, and access limitations;
- preserve traceable historical corrections;
- prevent a newly captured or proposed record from being directly treated as validated or active merely because it was captured.

If provenance is insufficient, the record must not be presented as confirmed source teaching.

### UC-02 — Validate ICT-related claims

The process must:
- identify the exact claim and classify it sufficiently for appropriate evaluation;
- define claim-appropriate criteria before determining the result;
- link source, evidence, context, and relevant knowledge version;
- record the evidence-to-claim rationale, counterevidence, limitations, and unresolved issues;
- prevent missing criteria or insufficient evidence from being treated as automatic approval or rejection;
- keep evaluation result separate from knowledge-state change.

### UC-03 — Review supporting and contradictory evidence

The process must:
- retain reviewable links or references to material evidence on both sides;
- identify material contradictions rather than hiding or averaging them away;
- explain the effect of counterevidence on the evaluation;
- record unresolved conflict or insufficiency explicitly;
- preserve prior records when new evidence changes the assessment.

### UC-04 — Record market-study findings

The process must:
- record what the data show separately from the analyst's explanation;
- retain the data source and relevant instrument/time/context details as applicable;
- describe the event or behavior sufficiently for review;
- record sampling, data-quality, and contextual limitations;
- avoid presenting price-only patterns as direct order-flow evidence;
- avoid treating repeated patterns alone as proof of algorithmic mechanism or causality.

### UC-05 — Control learning and knowledge status

The process must:
- create learning records under OP-12 without silently promoting them to validated knowledge;
- link proposed learning to relevant claims, evidence, and existing knowledge versions;
- preserve uncertainty and contradictions;
- use OP-02 and OP-13 and the applicable governance controls when validation or a knowledge change is proposed;
- keep validation, governance approval, version creation, and activation distinct;
- preserve the historical path of changes and decisions.

## 11. Acceptance Criteria

The requirements elaboration is acceptable for this bounded workstream when the following criteria are satisfied:

- **AC-01:** Each claim being evaluated has an identified claim type and criteria appropriate to that type.
- **AC-02:** Evaluation criteria are established before the result; material changes to criteria are traceable.
- **AC-03:** Sources, supporting evidence, contradictory evidence, material limitations, and the evaluation rationale are reviewable or their unavailability is explicit.
- **AC-04:** Missing criteria or insufficient evidence do not trigger automatic acceptance or rejection.
- **AC-05:** Source teaching, interpretation, market observation, inference/hypothesis, and empirical evaluation result remain distinguishable.
- **AC-06:** A Learning Record does not automatically validate, govern, version, replace, or activate knowledge.
- **AC-07:** Corrections, new evidence, and reevaluations preserve historical integrity and traceable links.
- **AC-08:** Requirements already adequately covered by the baseline are reused rather than duplicated as new controls.
- **AC-09:** The completion of this requirements review is not represented as proof of ICT teaching accuracy, market validity, causality, algorithmic mechanism, software correctness, or live-trading readiness.

## 12. Consistency Review

A targeted logical consistency review was performed against the five initial use cases and relevant baseline boundaries.

| Review item | Result | Condition / rationale |
|---|---|---|
| UC-01 — Source teaching capture | PASS WITH CONDITION | Source/provenance limitations remain explicit; interpretation is separated from direct teaching. |
| UC-02 — Claim validation | PASS WITH CONDITION | Criteria must be appropriate to claim type and established before evaluation. |
| UC-03 — Evidence conflict | PASS WITH CONDITION | Supporting and contradictory evidence, uncertainty, and unresolved conflict remain reviewable. |
| UC-04 — Market-study findings | PASS WITH CONDITION | Observation is separate from explanation; price patterns alone do not prove order flow, algorithm, or causality. |
| UC-05 — Learning and knowledge state | PASS WITH CONDITION | Learning, validation, governance, versioning, and activation remain distinct. |
| Edge case A — Source verified, interpretation uncertain | PASS | Record source teaching and interpretation separately; evaluate the interpretation independently. |
| Edge case B — Price pattern without direct order-flow data | PASS | Record the price observation without claiming direct order-flow observation. |
| Edge case C — New evidence conflicts with an earlier result | PASS | Preserve the earlier record, link the new evidence, and consider reevaluation. |
| Edge case D — Learning suggests a knowledge change | PASS | Record learning first; validation and governance controls still apply to subsequent changes. |

**Overall review result: PASS WITH LIMITED REQUIREMENTS REMAINING.**

This is a logical requirements-consistency review only. It is not software testing, implementation verification, validation of ICT/Michael Huddleston teachings, or proof of market performance.

## 13. Gap Disposition

The review identified three material requirement details to formalize:

- **G-01 — Claim-type-specific criteria:** addressed in Sections 6 and 7.
- **G-02 — Evidence sufficiency, contradiction, and uncertainty:** addressed in Sections 7 and 8.
- **G-03 — Minimum Learning Record:** addressed in Section 9.

Traceability was considered but was not established as a separate new gap because existing controls (including D9 and CR-01 through CR-08) already provide the relevant baseline obligations. This document specifies that the initial use cases must retain the required links; it does not duplicate or replace the existing traceability architecture.

No additional requirements are introduced for other operational contracts unless one of the defined use cases demonstrates a material need. In particular, this review does not claim that excluded execution/runtime use cases have been fully specified.

## 14. Explicit Limitations and Future Reassessment Triggers

This document is intentionally limited to the initial research and knowledge-validation scope.

A future scope review may be required if the intended use expands to live execution, additional operational decisions, new claim families requiring materially different evaluation criteria, new evidence modalities, or other use cases that expose a real unresolved requirement.

A future review must identify the concrete unmet need and assess it against the existing baseline before proposing any new requirement or architectural work. A new stage or architecture must not be created merely to continue numbering.

This document does not authorize reopening C1–C7, Architectural Closure, or D0–D14. Frozen stages remain FINAL / FROZEN / CLOSED unless a proven, real Architectural Reason is established through the project's controlled process.

## 15. Final Disposition

**Document status: FINAL — READY FOR GITHUB UPLOAD**

The bounded operational requirements elaboration for **Research & Knowledge Validation First** is complete for the requirements identified in this workstream, subject to the explicit limitations above.

The document:
- supplements the existing operational specification baseline;
- formalizes only the identified minimum requirement gaps;
- preserves the established architecture and governance boundaries;
- does not create D15 or any other architecture stage;
- does not establish ICT/market truth or implementation readiness;
- does not authorize live trading execution.

### Required workflow

1. Current workstream finalized.
2. **GitHub upload — pending user action.**
3. User confirms the upload.
4. Only after confirmation may the next workstream be considered.

**Recommended filename:** `TOS_Operational_Requirements_Elaboration_Research_Knowledge_Validation_v1.0.md`
