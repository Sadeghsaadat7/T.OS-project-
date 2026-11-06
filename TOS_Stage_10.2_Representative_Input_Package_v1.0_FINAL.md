# TOS --- Stage 10.2

## Representative Input Package

### Educational Agent Prompt Engineering

**Document ID:** TOS-PE-S10.2-001\
**Version:** 1.0\
**Status:** FINAL --- FOR GITHUB UPLOAD AND HUMAN AUTHORITY
CONFIRMATION\
**Stage:** Stage 10 --- Bounded Educational Agent Prompt Engineering\
**Substage:** S10.2 --- Representative Input Package\
**Authority:** Human Authority / System Owner\
**Prepared by:** Project Designer / System Architect

------------------------------------------------------------------------

## 1. Purpose

This document defines the canonical Representative Input Package for the
initial bounded Educational Agent task established in S10.1.

The package provides a consistent input structure for the five approved
Agents while preserving their individual responsibilities, operational
boundaries, existing Handoff Contracts, Quality Gates, traceability
requirements, and Human Authority decision rights.

This specification supports subsequent Agent Prompt Engineering. It does
not authorize Agent execution, practical prompt validation, or
operational educational processing.

## 2. Governing Baseline

This document is governed by:

-   Stage 6 --- Educational Agent System Design v1.0.
-   Stage 7 --- Educational Agent Implementation Readiness Assessment
    v1.0.
-   Stage 8 --- Educational Agent Operational Specification & Handoff
    Contracts v1.0.
-   Stage 9 --- Educational Agent Pre-Implementation Readiness
    Verification v1.0.
-   Stage 10.1 --- Bounded Educational Agent Task Definition v1.0.

The following approved decisions remain unchanged:

-   The five-Agent architecture.
-   Existing Quality Gates G1--G4.
-   A4 independence in QC.
-   A5 eligibility restriction requiring valid G3 acceptance.
-   Orchestration / Workflow Control as a TOS control mechanism, not an
    additional Agent.
-   Human Authority reserved decisions and approval boundaries.
-   The bounded initial task scope established in S10.1.

No Agent, Gate, architectural layer, or new Handoff Contract is
introduced by this document.

## 3. Package Design Decision

The selected design is:

**One Canonical Representative Input Package with five Agent-specific
Input Views.**

The canonical package is the common reference structure. Each Agent
receives only the information required and authorized for its approved
responsibility.

The design does not require five independent package databases or
independently maintained copies.

The package has two logical layers:

1.  **Shared Context and Control Information:** common identity, source,
    task, evidence, limitation, version, and workflow context.
2.  **Agent-specific Input Views:** bounded subsets of the shared
    package and eligible upstream outputs appropriate to each Agent.

An Agent-specific view does not grant access to information outside that
Agent's approved scope.

## 4. Canonical Package Structure

### P1 --- Source Identity & Metadata

Contains, where available and applicable:

-   Package ID.
-   Source Unit ID.
-   Registry Record Reference.
-   Source ID.
-   Source title.
-   Source type and group.
-   Origin and provenance references.
-   Available metadata.
-   Source locator.
-   Metadata completeness status.
-   Source identity status.
-   Registry relationship.
-   Known identity conflicts.

A missing metadata field must remain explicitly missing or unknown. It
must not be silently inferred.

### P2 --- Source Material References

Contains, where available and applicable:

-   Source material references.
-   File or media references.
-   Source access status.
-   Integrity observations.
-   Relevant source locators.
-   Missing material references.
-   Material restrictions.
-   Source eligibility dependencies.

A source reference or Registry entry does not establish that source
content is accessible, authentic, complete, or eligible for the assigned
task.

### P3 --- Task Definition & Scope

Contains:

-   Task ID and version.
-   Authorized Source Unit.
-   Assigned Agent.
-   Task purpose.
-   Authorized scope.
-   Explicit exclusions.
-   Expected output.
-   Required inputs.
-   Optional or conditional inputs.
-   Dependencies.
-   Applicable criteria references and versions.
-   Stop conditions.
-   Required Human Authority decisions.

Task-level acceptance criteria are defined in S10.3 and are not
established by this document.

### P4 --- Evidence & Traceability Context

Contains, where applicable:

-   Evidence IDs and references.
-   Source locators.
-   Claim and record references.
-   Supporting evidence references.
-   Contradictory evidence references.
-   Evidence availability and status.
-   Evidence limitations.
-   Uncertainty.
-   Provenance relationships.

The package must preserve the distinction between:

-   Source teaching or source assertion.
-   Directly observed source content.
-   Interpretation.
-   Inference or hypothesis.
-   Supporting or contradictory evidence.
-   Validation outcome.

Traceability establishes the origin and relationship of information; it
does not, by itself, prove factual correctness.

### P5 --- Handoff & Workflow Context

Contains:

-   Previous Agent.
-   Current Agent.
-   Intended receiving Agent.
-   Input references.
-   Output references.
-   Handoff ID and version.
-   Handoff disposition.
-   Operational status.
-   Applicable Quality Gate.
-   Gate decision reference, when one exists.
-   Required next action.
-   Return, rework, or escalation history.

Every Handoff must preserve the five elements defined in Stage 8:

1.  Identity.
2.  Traceability.
3.  Status.
4.  Completeness.
5.  Action.

The receiving Agent must record an explicit disposition. Silence or mere
receipt is not acceptance.

### P6 --- Limitations & Exception Context

Contains:

-   Missing information.
-   Unknown information.
-   Conflicting information.
-   Inaccessible or unverified references.
-   Incomplete metadata.
-   Unresolved source discrepancies.
-   Evidence insufficiency.
-   Inherited upstream limitations.
-   Required escalation.
-   Outstanding Human Authority decisions.

Limitations must remain visible in downstream views and records. They
must not be removed merely because information passes from one Agent to
another.

## 5. Agent-Specific Input Views

The following definitions identify the information needed to construct
each Agent's view. They do not replace or expand the approved Agent
specifications or Stage 8 Handoff Contracts.

### 5.1 A1 --- Source Inventory & Reconciliation

**Input view:** P1, P2, P3, and P6, limited to the authorized Source
Unit and task.

Required context:

-   Assigned Source Unit.
-   Registry reference.
-   Available source metadata.
-   Candidate source material references.
-   Provenance information.
-   Reconciliation context.
-   Known discrepancies.
-   Authorized scope and exclusions.

Expected output:

A Source Inventory & Reconciliation Record containing source identity,
provenance, metadata completeness, accessibility and integrity status,
discrepancies, eligibility disposition, rationale, limitations, and next
action.

**Related Gate:** G1 --- Source Readiness.

Boundary: A1 does not determine educational truth, validate educational
claims, or modify the approved Registry without authorization.

### 5.2 A2 --- Educational Review & Extraction

**Input view:** eligible A1 source record and handoff, authorized P2 and
P3 context, relevant P4 references, and inherited P6 limitations.

Required context:

-   A1 Source ID and source record.
-   Explicit A1 eligibility disposition.
-   Authorized and accessible source material.
-   Review scope and exclusions.
-   Source locators.
-   Applicable extraction and classification requirements.
-   Inherited limitations.

Expected output:

Structured Educational Records with stable IDs, source links, locators,
classifications, attribution, extracted-content references, ambiguity
and contradiction notes, limitations, and operational status.

Boundary: Extraction is not evidence validation, independent QC, or
knowledge integration. A2 must not fill gaps in source material with
unsupported assumptions or unrelated general knowledge.

**Related Gate:** G2 --- Educational Record and Validation Readiness, in
coordination with the applicable A3 validation requirements.

### 5.3 A3 --- Evidence & Knowledge Validation

**Input view:** A2 Educational Records and handoff, relevant P1 and P3
context, P4 evidence references, and inherited P6 limitations.

Required context:

-   Claim and educational-record references.
-   Source and evidence references.
-   Supporting evidence.
-   Contradictory evidence, where available.
-   Applicable validation criteria and version.
-   Source attribution.
-   Inherited uncertainty and limitations.

Expected output:

An Evidence & Knowledge Validation Record containing claim-to-evidence
mapping, criteria applied, evidence sufficiency, validation outcome,
rationale, uncertainty, unresolved conflicts, limitations, and status.

Validation outcomes must distinguish at least:

-   SUPPORTED.
-   UNSUPPORTED.
-   INSUFFICIENT_EVIDENCE.

Boundary: A3 must not treat a source assertion as independent proof,
suppress contradictory evidence, manufacture support, or unilaterally
establish doctrine or policy.

**Related Gate:** G2 --- Educational Record and Validation Readiness.

### 5.4 A4 --- Independent QC & Coverage

**Input view:** the original authorized scope and criteria, relevant
A1--A3 outputs, original source and evidence references, and applicable
P1--P6 context needed for independent assessment.

Required context:

-   Original authorized scope and exclusions.
-   Original source references.
-   A1 source records.
-   A2 educational records.
-   A3 validation records.
-   Applicable criteria and versions.
-   Expected outputs and required evidence.
-   Known limitations and unresolved issues.
-   Access sufficient to inspect underlying references where required.

Expected output:

An Independent QC & Coverage Report containing assessed scope, records
and evidence reviewed, traceability findings, evidence consistency
findings, classification and completeness findings, coverage findings,
deficiencies, explicit QC disposition, rationale, limitations, and
required corrective actions.

**Related Gate:** G3 --- Independent QC and Coverage Acceptance.

Independence requirements:

-   A4 must not produce the work it independently assesses.
-   A4 must not own corrective rewriting of the work it assesses.
-   A4 must not blindly adopt A3 conclusions.
-   A4 must not change approved assessment criteria.
-   A4 must not rely solely on summaries when direct source or evidence
    access is necessary.
-   Coverage claims must be limited to the actual scope, records,
    sources, and criteria examined.

If independence, criteria, or source/evidence access is insufficient, A4
must record the limitation and use the applicable stop, return, or
escalation route. It must not manufacture an acceptance decision.

### 5.5 A5 --- Knowledge Integration & Learning Record

**Input view:** eligible upstream outputs, valid A4 QC report and G3
acceptance reference, relevant P4 and P5 records, P6 limitations,
approved integration rules, and required Human Authority decisions.

Mandatory entry requirements:

-   Traceable upstream records.
-   Valid source, evidence, and validation references.
-   Explicit and valid A4 QC acceptance under G3.
-   Applicable approved integration rules.
-   Required Human Authority decisions, where reserved authority
    applies.

Expected output:

An Accepted Knowledge & Learning Record containing stable record ID,
version, source references, evidence and validation references, QC
acceptance reference, classification and validation status, scope and
limitations, integration rationale, learning record, change history, and
governance or activation status where applicable.

**Related Gate:** G4 --- Controlled Knowledge Integration.

Boundary: A5 is not an independent source of truth. It cannot bypass G3,
override A4, or convert PARTIAL, FAIL, UNVERIFIED, or BLOCKED outputs
into accepted knowledge. Integration does not independently prove truth.

## 6. Handoff Compatibility

Existing Stage 8 Handoff Contracts remain authoritative. The package
represents their required information without redefining their
acceptance rules.

  -----------------------------------------------------------------------
  Handoff                             Information that must remain
                                      available
  ----------------------------------- -----------------------------------
  A1 → A2                             Source identity, provenance,
                                      metadata, accessibility,
                                      eligibility, limitations, and
                                      explicit disposition

  A2 → A3                             Educational Record IDs, source
                                      locators, classification,
                                      attribution, extracted-content
                                      references, and limitations

  A3 → A4                             Claim and record IDs, evidence
                                      mapping, criteria and versions,
                                      validation outcomes, rationale, and
                                      uncertainty

  A4 → A5                             QC report, assessed scope, reviewed
                                      records, explicit G3 acceptance,
                                      limitations, and excluded or
                                      returned items

  A5 → TOS / authorized control       Knowledge records, versions,
  process                             source/evidence references,
                                      integration history, governance
                                      status, and pending decisions
  -----------------------------------------------------------------------

The existing Handoff dispositions and handling rules remain in force,
including ACCEPT, ACCEPT WITH LIMITATIONS, RETURN, REJECT, and ESCALATE
where applicable under Stage 8.

Every receiving Agent must record its disposition. A handoff must not be
treated as accepted solely because it was transmitted or received.

No additional Handoff Contract is created by S10.2.

## 7. Metadata and Evidence Exception Handling

The package distinguishes the following conditions:

  -----------------------------------------------------------------------
  Condition                           Required treatment
  ----------------------------------- -----------------------------------
  Missing                             Record that the information is
                                      absent; do not fabricate a value

  Unknown                             Preserve the unresolved status

  Conflicting                         Preserve the competing references
                                      and identify the conflict

  Inaccessible                        Record access as failed or
                                      unverified, as appropriate

  Incomplete                          State which fields, records, or
                                      scope are incomplete

  Unsupported                         Do not represent the claim as
                                      validated

  Insufficient evidence               Preserve the insufficiency and its
                                      effect on downstream eligibility
  -----------------------------------------------------------------------

These conditions must remain visible in downstream Agent views and
records.

The following distinctions are mandatory:

-   Registry presence is not proof of source accessibility.
-   Source identity is not proof of source integrity.
-   Source accessibility is not automatic educational eligibility.
-   Educational extraction is not evidence validation.
-   Evidence validation is not independent QC acceptance.
-   QC acceptance is not, by itself, authorization for integration; G4
    requirements and reserved decisions still apply.

## 8. Representative Fixture

**Fixture ID:** SU-REP-01\
**Classification:** Illustrative / Non-Operational.

The representative fixture is a structural test package, not an executed
Agent task.

It contains:

1.  One reference to an existing Core Content Registry record as a
    source identity anchor.
2.  Source identity and metadata fields.
3.  Source material reference fields with access status left explicit.
4.  Illustrative examples of complete, incomplete, and conflicting
    metadata.
5.  Illustrative educational-record structures.
6.  Illustrative evidence-record structures.
7.  The input structure required for independent QC.
8.  The input structure required for controlled Knowledge Integration.

Illustrative records for A2--A5 are structural examples only. They are
not actual ICT teaching, real evidence, validated findings, actual Agent
outputs, or accepted Knowledge.

The existence of a Registry reference does not establish source
accessibility, source eligibility, or G1 acceptance.

### 8.1 Fixture execution boundary

The fixture does not produce actual Quality Gate acceptance.

-   G1 requires source identity, provenance, accessibility, integrity,
    eligibility, and other applicable evidence under its existing
    contract.
-   G2 requires the applicable educational and validation readiness
    conditions.
-   G3 requires independent QC against the applicable approved criteria.
-   G4 requires eligible upstream outputs, valid G3 acceptance, approved
    integration rules, and required authority decisions.

Task-level acceptance criteria remain reserved for S10.3. This fixture
must not be used to imply that those criteria have already been defined
or passed.

A5 cannot perform actual Knowledge Integration using this illustrative
fixture.

## 9. Traceability and Version Control

The package must preserve, as applicable:

-   Package ID.
-   Package version.
-   Schema version.
-   Source Unit ID.
-   Task ID and version.
-   Source and Registry references.
-   Input and output references.
-   Criteria references and versions.
-   Handoff references and dispositions.
-   Quality Gate decision references.
-   Limitations and unresolved issues.
-   Change history.

Every revised package must identify:

-   Previous version.
-   Revised version.
-   Changed fields or records.
-   Reason for change.
-   Responsible authority or change owner.
-   Affected downstream references.

Previously reviewed versions must remain distinguishable from revised
versions. Broken or unverifiable references must be reported;
replacement references must not be fabricated.

Detailed canonical document-version governance remains reserved for
S10.4. This section states only the traceability and version information
needed by the S10.2 package.

## 10. Known Limitations

The following project limitations remain unresolved and must not be
represented as completed:

1.  The Core Content Source Registry contains 115 records; complete
    educational coverage has not been established.
2.  Record 85 has an unresolved MP4 discrepancy.
3.  Records 112--115 retain historical metadata discrepancies.
4.  A complete Mentorship inventory has not been established.
5.  The Latest Videos inventory and cutoff have not been established.
6.  Comprehensive QC execution evidence is not established.
7.  Complete Knowledge Integration execution evidence is not
    established.
8.  Actual execution of the five Agents has not been demonstrated.
9.  Actual accessibility of the illustrative Source Unit's source
    material has not been verified.

These limitations are inherited context for package design. S10.2 does
not reopen previous audits or attempt to resolve these source-research
matters.

## 11. S10.2 Acceptance Criteria

  -----------------------------------------------------------------------
  ID                                  Acceptance criterion
  ----------------------------------- -----------------------------------
  S10.2-AC-01                         A canonical package structure is
                                      defined

  S10.2-AC-02                         Shared context and Agent-specific
                                      views are distinguished

  S10.2-AC-03                         Input requirements for all five
                                      approved Agents are defined

  S10.2-AC-04                         Existing Stage 8 Handoff Contracts
                                      are preserved

  S10.2-AC-05                         A4 independence and bounded
                                      coverage are maintained

  S10.2-AC-06                         A5's G3 acceptance dependency is
                                      maintained

  S10.2-AC-07                         Source identity and traceability
                                      requirements are preserved

  S10.2-AC-08                         Missing, unknown, conflicting,
                                      incomplete, and inaccessible inputs
                                      are handled explicitly

  S10.2-AC-09                         Representative fixture boundaries
                                      are explicit

  S10.2-AC-10                         No unsupported educational,
                                      evidentiary, or operational claims
                                      are introduced

  S10.2-AC-11                         No Agent, Gate, architectural
                                      layer, or Handoff Contract is added

  S10.2-AC-12                         Dependencies reserved for S10.3 and
                                      S10.4 remain separate
  -----------------------------------------------------------------------

## 12. Explicit Exclusions

This specification does not authorize:

-   Detailed ICT lesson research.
-   Actual execution of any Educational Agent.
-   Practical Prompt Validation.
-   Operational educational processing.
-   Knowledge Base modification.
-   Coding or software implementation.
-   Changes to frozen architecture.
-   Changes to existing Quality Gates.
-   Introduction of additional Agents.
-   Definition or approval of S10.3 task-level acceptance criteria.
-   Completion of S10.4 canonical document-version governance.

## 13. S10.2 Final Determination

The Canonical Representative Input Package and its five Agent-specific
Input Views are defined at the architecture and prompt-design input
level.

The specification preserves the approved five-Agent architecture,
existing Handoff Contracts, G1--G4 Quality Gates, A4 independence, A5's
G3 eligibility restriction, traceability requirements, known
limitations, and Human Authority boundaries.

The representative fixture is explicitly illustrative and
non-operational. It does not establish actual source accessibility,
actual Agent execution, Quality Gate acceptance, or practical validation
readiness.

### Stage 9 Gap Disposition

-   **S9-G02 --- Representative Input Package:** Addressed at the
    package-structure and prompt-design input level. Actual accessible
    source-material confirmation remains unverified and is not claimed
    as completed.
-   **S9-G03 --- Task-Level Acceptance Criteria:** Reserved for S10.3.
-   **S9-G04 --- Canonical Document-Version Consistency:** Reserved for
    S10.4.

No subsequent substage is automatically authorized by this
determination.

## 14. Stage Closure Requirements

S10.2 is considered closed only after all of the following occur:

1.  Human Authority reviews and approves the final document.
2.  The final document is prepared and finalized.
3.  The final document is uploaded to the TOS GitHub repository.
4.  Human Authority explicitly confirms the upload.

**Current document status:** FINAL --- READY FOR GITHUB UPLOAD.\
**Current stage status:** AWAITING GITHUB UPLOAD AND EXPLICIT HUMAN
AUTHORITY CONFIRMATION.

------------------------------------------------------------------------

**End of Document --- TOS Stage 10.2 --- Representative Input Package
v1.0**
