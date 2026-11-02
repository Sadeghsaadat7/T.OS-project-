# TOS Educational Agent System Design v1.0

**Document ID:** TOS-EWS-ASD-001\
**Version:** 1.0\
**Status:** DRAFT --- FOR HUMAN AUTHORITY REVIEW\
**Workstream:** Educational Workstream\
**Stage:** Stage 6 --- Educational Agent System Design\
**Authority:** Human Authority / System Owner\
**Prepared by:** Project Designer / System Architect

------------------------------------------------------------------------

## 1. Purpose

This document defines the architecture-level design of the educational
Agent system within the TOS --- Trading Operations System.

Its purpose is to establish the required Agent roles, responsibility
boundaries, collaboration flow, handoff conditions, quality controls,
high-level inputs and outputs, and human approval boundaries needed to
support a controlled educational lifecycle from source management
through knowledge integration.

This is a system-design document. It does not perform educational
research, analyze ICT lessons, define executable prompts, or specify
implementation details.

## 2. Authority and Design Principles

### 2.1 Authority

-   **Human Authority / System Owner:** The user is the final authority
    for scope, acceptance criteria, exceptions, knowledge-policy
    decisions, and approval of changes.
-   **Project Designer / System Architect:** The assistant is
    responsible for architecture-level design, workflow integrity,
    documentation, and Agent requirements. The assistant is not an ICT
    educational or research Agent.
-   **Educational Agents:** Agents perform only their assigned
    responsibilities under approved scope, rules, and workflow controls.
    They do not acquire independent authority to change system policy or
    knowledge doctrine.

### 2.2 Governing principles

1.  Accuracy and completeness take priority over speed.
2.  Practicality and buildability take priority over unnecessary design
    complexity.
3.  Use the minimum detail necessary to define reliable responsibilities
    and controls.
4.  Do not convert assumptions, interpretations, or hypotheses into
    established source claims.
5.  Preserve provenance, traceability, uncertainty, limitations, and
    change history.
6.  Separate content production, evidence validation, independent
    quality control, and knowledge integration.
7.  Do not claim source coverage or educational completion without
    supporting evidence.
8.  Reuse approved historical work and existing registries; do not
    rebuild them without a documented reason.
9.  Preserve Human Authority over scope, acceptance criteria,
    exceptions, and consequential knowledge decisions.
10. Keep the design within the approved current TOS architecture. This
    document does not reopen or modify frozen C1--C7 or D0--D14
    architecture.

## 3. Scope

### 3.1 Included

Stage 6 defines:

-   The required educational Agent roles and their high-level
    responsibilities.
-   Responsibility boundaries and separation of duties.
-   Collaboration sequence and handoff relationships.
-   High-level inputs, outputs, and dependencies.
-   Quality-control and acceptance gates.
-   Stop, return, rework, escalation, and unresolved-item handling.
-   Human Authority decision boundaries.
-   Coverage of the ten previously established educational-workstream
    dimensions.

### 3.2 Excluded

The following are outside this stage:

-   Detailed study, interpretation, or extraction of Michael Huddleston
    / ICT lessons.
-   Executable Agent prompts or prompt engineering.
-   Coding, implementation, APIs, databases, infrastructure, or
    deployment details.
-   Reopening frozen TOS architecture.
-   Claims that the educational source corpus is complete or that all
    educational work has been executed.
-   Defining a sixth or seventh Agent without evidence of a distinct,
    necessary responsibility that cannot be handled by the five-role
    design and existing TOS workflow controls.

## 4. Design Decision: Five Educational Agents

The current educational scope is covered by five functional Agent roles.
No additional standalone Agent is justified by the current
architecture-level requirements.

This is a bounded design decision, not a claim that the five-role
structure can never change. Evidence from actual execution may justify a
future revision, but such a revision must identify a real responsibility
gap, explain why workflow controls or an existing Agent cannot own it,
and receive Human Authority approval.

### 4.1 Agent 1 --- Source Inventory & Reconciliation

**Purpose:** Establish and maintain a traceable, qualified inventory of
educational sources and their known status.

**Responsibilities:**

-   Maintain and reconcile source inventory records.
-   Record source identity, provenance, and available metadata.
-   Record accessibility, integrity, and known availability limitations.
-   Reconcile incoming sources with approved registries and historical
    records.
-   Identify and record duplicates, discrepancies, missing items, and
    metadata conflicts.
-   Preserve existing approved inventories and prior work unless a
    documented reason requires correction or extension.
-   Communicate source readiness and limitations to downstream work.

**Primary output:** Source Inventory & Reconciliation Records.

**Boundary:** Agent 1 establishes source identity and status. It does
not perform detailed educational extraction, decide the validity of
educational claims, or independently certify overall educational
coverage.

### 4.2 Agent 2 --- Educational Review & Extraction

**Purpose:** Produce structured educational records from sources that
are eligible for review under the approved scope.

**Responsibilities:**

-   Review eligible sources in the approved source sequence.
-   Extract and structure source content while preserving source
    locators.
-   Distinguish source teaching from interpretation, inference, and
    hypothesis.
-   Record ambiguity, contradiction, missing context, and extraction
    limitations.
-   Maintain traceability from each record to its source and relevant
    location.
-   Mark uncertainty explicitly rather than silently resolving it.

**Primary output:** Structured Educational Records.

**Boundary:** Agent 2 records what the source communicates and
classifies the nature of the record. It does not grant final evidence
validation, independent QC acceptance, or authority to change knowledge
policy.

### 4.3 Agent 3 --- Evidence & Knowledge Validation

**Purpose:** Evaluate whether educational records and their claims are
adequately supported, attributed, and epistemically classified.

**Responsibilities:**

-   Examine the relationship between claims and their cited source
    evidence.
-   Assess source attribution and evidence sufficiency within the
    approved validation rules.
-   Identify unsupported claims, conflicts, gaps, and overextended
    interpretations.
-   Review whether uncertainty and epistemic classifications are
    justified.
-   Record validation status, rationale, limitations, and unresolved
    issues.
-   Return specific deficiencies to the responsible upstream role when
    correction is required.

**Primary output:** Evidence & Validation Records.

**Boundary:** Agent 3 validates evidence and claim support; it does not
replace Agent 2 by conducting a second full extraction, and it does not
perform the independent end-to-end QC function assigned to Agent 4. It
cannot independently amend approved doctrine or system policy.

### 4.4 Agent 4 --- Independent QC & Coverage

**Purpose:** Independently assess the quality, traceability, process
compliance, and coverage status of the outputs against approved
criteria.

**Responsibilities:**

-   Conduct independent checks of records and their supporting
    references.
-   Assess traceability, classification, contradiction handling, and
    compliance with approved requirements.
-   Compare expected scope with completed and accepted scope.
-   Identify missing, incomplete, duplicated, inconsistent, or
    non-compliant outputs.
-   Issue explicit QC and coverage outcomes with supporting reasons.
-   Return failed or incomplete work to the responsible Agent for
    correction.
-   Preserve independence by not becoming the producer or owner of the
    work being checked.

**Primary output:** Independent QC & Coverage Reports.

**Boundary:** Agent 4 assesses quality and coverage independently. It
does not take ownership of upstream corrections, perform knowledge
integration, or redefine the scope and acceptance criteria established
by Human Authority.

### 4.5 Agent 5 --- Knowledge Integration & Learning Record

**Purpose:** Prepare and maintain controlled learning and knowledge
records using only outputs that have passed the required acceptance
controls.

**Responsibilities:**

-   Receive only eligible outputs that have passed the required QC
    acceptance gate.
-   Prepare accepted records for controlled integration under approved
    rules.
-   Preserve source links, evidence status, limitations, provenance, and
    change history.
-   Keep accepted knowledge distinct from unresolved, partial, or
    unverified material.
-   Record integration actions and identify items requiring Human
    Authority decisions.
-   Prevent integration activity from silently changing source meaning,
    evidence status, or approved knowledge policy.

**Primary output:** Accepted Knowledge & Learning Records.

**Boundary:** Agent 5 is not an independent source of truth, does not
override QC outcomes, and cannot convert rejected, partial, blocked, or
unverified material into accepted knowledge. Any consequential policy or
doctrine change remains subject to Human Authority approval.

## 5. Responsibility Separation

The following boundaries are mandatory:

### 5.1 Agent 2 versus Agent 3

-   Agent 2 produces structured records from source material and
    preserves source locators.
-   Agent 3 evaluates the evidence support, attribution, and epistemic
    status of those records.
-   Agent 3 must not silently replace or rewrite the extraction process
    assigned to Agent 2.
-   A validation finding that requires source re-examination or record
    correction must be returned to the appropriate upstream owner.

### 5.2 Agent 3 versus Agent 4

-   Agent 3 validates claims and their evidence support.
-   Agent 4 independently evaluates the quality and completeness of the
    outputs and the process against approved criteria.
-   Agent 4 must not merely copy Agent 3's validation result; it must be
    able to inspect the relevant records and references independently.
-   Agent 4 must not become responsible for correcting the work it
    audits.

### 5.3 Agent 4 versus Agent 5

-   Agent 4 reports QC and coverage status and determines whether the
    defined acceptance criteria have been met.
-   Agent 5 prepares eligible, accepted outputs for controlled
    integration.
-   Agent 5 cannot alter, waive, or override Agent 4's QC decision.
-   Integration must preserve the accepted record's provenance, evidence
    status, limitations, and change history.

### 5.4 Workflow coordination

Workflow orchestration, status tracking, routing, and rework
coordination belong to the applicable TOS workflow/control mechanisms.
They do not justify a separate educational Agent under the current
scope.

Coverage expectations and acceptance criteria are established or
approved by Human Authority. Agent 1 maintains source inventory status;
Agent 4 independently assesses coverage against the approved
expectations.

## 6. Collaboration Flow

The default controlled flow is:

1.  **Human Authority** approves the educational scope, expected
    coverage, and acceptance criteria.
2.  **Agent 1** inventories and reconciles sources and records readiness
    and limitations.
3.  **Agent 2** reviews eligible sources and produces structured
    educational records.
4.  **Agent 3** validates evidence, attribution, and epistemic
    classification.
5.  **Agent 4** independently assesses QC and coverage.
6.  **Agent 5** prepares only eligible accepted outputs for controlled
    knowledge integration.
7.  **Human Authority** makes decisions reserved to the System Owner and
    approves exceptions or consequential changes.

This is the normal sequence, not a requirement that every issue must
travel through every Agent again. Rework should return to the role
responsible for the identified deficiency.

## 7. High-Level Handoff Contracts

Each handoff must carry sufficient information for the receiving role to
understand what was done, what evidence supports it, what remains
uncertain, and what action is expected.

  -----------------------------------------------------------------------
  Handoff                             Minimum handoff content
  ----------------------------------- -----------------------------------
  Agent 1 → Agent 2                   Source identifier; identity and
                                      provenance; available metadata;
                                      accessibility and integrity status;
                                      known limitations and discrepancies

  Agent 2 → Agent 3                   Structured educational records;
                                      source locators; content
                                      classification; ambiguity and
                                      contradiction notes; extraction
                                      limitations

  Agent 3 → Agent 4                   Validation records;
                                      claim-to-evidence links; validation
                                      status and rationale; unresolved
                                      conflicts and limitations

  Agent 4 → Agent 5                   Independent QC and coverage report;
                                      acceptance status; accepted record
                                      references; excluded or unresolved
                                      items and reasons

  Agent 5 → Human Authority / TOS     Prepared accepted knowledge
                                      records; integration/change
                                      history; source and evidence links;
                                      limitations; decisions requiring
                                      human approval
  -----------------------------------------------------------------------

These are conceptual contracts. File formats, schemas, technical fields,
and implementation mechanisms are not defined in Stage 6.

## 8. Status Model

The following status terms must remain distinct and must be used with
explicit reasons:

-   **PASS:** The applicable stage criteria have been met.
-   **PARTIAL:** Some requirements are met, but defined portions remain
    incomplete or limited.
-   **FAIL:** One or more required criteria have not been met.
-   **UNVERIFIED:** Available evidence is insufficient to determine
    whether the relevant requirement is met.
-   **BLOCKED:** Progress cannot continue until a dependency, source,
    access issue, or authorized decision is resolved.

Interpretation rules:

-   UNVERIFIED is not equivalent to FAIL.
-   PARTIAL is not full acceptance.
-   BLOCKED does not imply that the underlying content is false or
    invalid.
-   A PASS at one stage does not automatically mean that the full
    educational workstream is complete.
-   Every non-PASS outcome must include the reason, affected scope, and
    required next action where known.

## 9. Quality and Acceptance Gates

### G1 --- Source Readiness

**Primary owner:** Agent 1\
**Purpose:** Establish whether a source is sufficiently identified and
qualified for the next activity.

The source record must identify the source and record known provenance,
availability, integrity, and limitations. Unresolved discrepancies must
remain visible. A source may be usable with explicit limitations where
the approved rules permit; unresolved status must not be represented as
full readiness.

### G2 --- Educational Record and Validation Readiness

**Primary owners:** Agent 2 and Agent 3\
**Purpose:** Ensure that structured records and their evidence status
are sufficiently documented for independent QC.

Records must preserve source locators, content classification,
claim-to-evidence relationships, uncertainty, and unresolved issues.
Agent 2's completion of extraction does not constitute evidence
validation. Agent 3's validation does not constitute independent QC
acceptance.

### G3 --- Independent QC and Coverage Acceptance

**Primary owner:** Agent 4\
**Purpose:** Determine whether the relevant outputs meet approved
quality and coverage criteria.

The report must distinguish accepted, partial, failed, unverified, and
blocked items. It must state the assessed scope, criteria applied,
findings, and reasons. Agent 4 must remain independent of the production
and correction of the work it reviews.

### G4 --- Controlled Knowledge Integration

**Primary owner:** Agent 5\
**Human authority:** Retained for reserved decisions and exceptions\
**Purpose:** Prepare accepted outputs for controlled integration.

Only outputs that satisfy the required acceptance conditions may proceed
as accepted knowledge. Agent 5 must preserve provenance, evidence
status, limitations, and change history. Any integration action that
requires a policy, doctrine, scope, or exception decision must be paused
and referred to Human Authority.

## 10. Stop, Rework, and Escalation Rules

Rework must be targeted to the origin of the deficiency:

  -----------------------------------------------------------------------
  Identified issue                    Default return / action
  ----------------------------------- -----------------------------------
  Source identity, provenance,        Return to Agent 1
  accessibility, integrity, or        
  inventory discrepancy               

  Missing source locator, incomplete  Return to Agent 2
  extraction, or record-structure     
  defect                              

  Unsupported claim, attribution      Return to Agent 3, or to the
  defect, or                          upstream record owner if the source
  validation/classification issue     record itself requires correction

  QC failure or coverage gap          Agent 4 documents the finding and
                                      routes it to the responsible owner;
                                      Agent 4 does not perform the
                                      correction

  Scope ambiguity, disputed           Stop and refer to Human Authority
  acceptance criteria, consequential  
  unresolved conflict, or             
  policy/doctrine decision            

  Missing external dependency or      Mark BLOCKED or UNVERIFIED as
  unavailable source                  appropriate; preserve the
                                      limitation and do not imply
                                      completion
  -----------------------------------------------------------------------

Additional controls:

1.  Every rework request must identify the affected record or scope, the
    deficiency, the responsible owner, and the expected correction or
    decision.
2.  Rework must be limited to the identified issue; unrelated completed
    work should not be repeated without reason.
3.  Repeated failure must be recorded with its history and cause.
    Indefinite, untracked rework is not permitted.
4.  If a correction changes a previously checked output, the affected
    output must return through the relevant validation and independent
    QC checks.
5.  Unresolved issues must remain visible in downstream records and must
    not be silently dropped.
6.  Agent 4 may reject or return work but must not become the correction
    owner.
7.  No Agent may bypass a required gate or unilaterally waive an
    approved criterion.

## 11. Human Authority Reserved Decisions

The following remain under Human Authority:

-   Approval or change of educational scope and expected coverage.
-   Approval or change of acceptance criteria.
-   Resolution of consequential ambiguity or conflict affecting
    knowledge meaning or validity.
-   Approval of exceptions or acceptance decisions involving unresolved
    limitations.
-   Approval of changes to knowledge policy, integration rules, or Agent
    responsibility boundaries.
-   Approval of any material change to this Agent system design.
-   Final decisions that exceed an Agent's explicitly assigned
    authority.

Agents may provide evidence, analysis, recommendations, and status
reports. They do not acquire final decision authority through their
workflow position or output status.

## 12. Coverage Mapping

The five-Agent design is mapped against the ten established
educational-workstream dimensions:

  -----------------------------------------------------------------------
  Educational coverage    Primary responsibility  Complementary control
  dimension                                       
  ----------------------- ----------------------- -----------------------
  1\. Inventory           Agent 1                 Agent 4

  2\. Provenance /        Agent 1                 Agents 3 and 4
  Identity                                        

  3\. Accessibility /     Agent 1                 Agent 4
  Integrity                                       

  4\. Review              Agent 2                 Agent 4

  5\. Extraction          Agent 2                 Agents 3 and 4

  6\. Evidence            Agents 2 and 3          Agent 4
  Traceability                                    

  7\. Epistemic           Agent 2                 Agents 3 and 4
  Classification                                  

  8\. Ambiguity /         Agents 2 and 3          Agent 4; Human
  Contradiction Handling                          Authority for reserved
                                                  decisions

  9\. Independent QC      Agent 4                 Human Authority
                                                  oversight

  10\. Acceptance /       Agent 5                 Agent 4 and Human
  Integration                                     Authority
  -----------------------------------------------------------------------

This mapping demonstrates architectural responsibility coverage only. It
does not prove that the source corpus is complete, that all records have
been processed, or that any specific educational coverage target has
been achieved.

## 13. Consistency with Prior Educational Readiness Findings

This design must preserve the limitations and findings established in
prior educational-workstream stages.

In particular:

-   The existing 115-record Core Content Source Registry is an
    established baseline and must be reused rather than rebuilt without
    documented justification.
-   Known registry discrepancies and metadata limitations must remain
    visible and must not be treated as resolved merely because the Agent
    system has been designed.
-   Mentorship source inventory and collection completeness remain
    subject to evidence-based reconciliation.
-   The latest official-channel source inventory must not be described
    as complete without a defensible cutoff and supporting inventory
    evidence.
-   Historical research and pilot work already completed must be
    preserved and reused where applicable; unnecessary repetition is
    prohibited.
-   No claim of complete educational source coverage, complete
    extraction, or complete knowledge integration is authorized by this
    design.
-   The conditional readiness decision from Stage 5 permits bounded
    Agent design; it does not remove the need to manage incomplete or
    uncertain source evidence during later execution.

## 14. Five-Agent Sufficiency Decision

**Decision:** Five functional Agent roles are sufficient for the
currently approved educational Agent system design scope.

Rationale:

-   The five roles cover the full conceptual lifecycle from source
    inventory to controlled knowledge integration.
-   The key separation-of-duty risks between extraction, evidence
    validation, independent QC, and integration are explicitly
    addressed.
-   Workflow orchestration and status routing are responsibilities of
    TOS workflow/control mechanisms, not a reason to create another
    Agent.
-   Coverage expectations and acceptance criteria remain under Human
    Authority; source inventory and independent coverage assessment have
    distinct owners.
-   Deficiency handling and rework can be managed through the defined
    workflow and handoff contracts.

This decision is limited to the current scope. A future proposal for an
additional Agent must demonstrate a distinct recurring responsibility
that cannot be adequately assigned to an existing Agent or handled
through workflow/control mechanisms. Any such proposal requires
documented architectural justification and Human Authority approval.

## 15. Stage 6 Completion Criteria

Stage 6 may be considered complete only when all of the following are
satisfied:

1.  The five Agent roles and their high-level responsibilities are
    documented.
2.  Responsibility boundaries and separation-of-duty controls are
    explicit.
3.  The collaboration flow and handoff relationships are documented.
4.  High-level inputs, outputs, and dependencies are identified.
5.  Status, stop, rework, escalation, and acceptance rules are defined.
6.  QC and integration gates are documented.
7.  Human Authority decision boundaries are explicit.
8.  The design is mapped against all ten established
    educational-workstream dimensions.
9.  Consistency with Stage 5 readiness findings and known source
    limitations is confirmed.
10. The final document is reviewed and approved by Human Authority.
11. The approved document is uploaded to the TOS GitHub repository.
12. Human Authority explicitly confirms the upload.

Until the final three conditions are met, Stage 6 remains open.

## 16. Out of Scope for the Next Stage Decision

This document does not authorize automatic transition to prompt
engineering, coding, or implementation. After Stage 6 is finalized,
uploaded, and explicitly confirmed, the next stage must be selected as a
separate decision based on the official TOS status and the remaining
project priorities.

No subsequent stage is opened or approved by this document alone.

------------------------------------------------------------------------

## 17. Final Design Determination

The current five-Agent educational system provides a coherent
architecture-level allocation of responsibilities across source
inventory, educational review and extraction, evidence validation,
independent QC and coverage, and controlled knowledge integration.

The design establishes separation of duties, traceable handoffs,
explicit status handling, targeted rework, independent acceptance
controls, and retained Human Authority.

No additional Agent is currently justified. No architectural change to
frozen TOS components is proposed.

**Stage 6 status:** DRAFT --- FOR HUMAN AUTHORITY REVIEW\
**Finalization condition:** Human Authority review and approval,
followed by GitHub upload and explicit upload confirmation.
