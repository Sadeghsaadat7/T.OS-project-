# TOS — Stage B Handoff
## ICT Knowledge Acquisition & Research Operations Specification
**Version:** 1.0  
**Status:** HANDOFF / FROZEN BASELINE EXTENSION  
**Stage:** B

## 1. Mission
Stage B systematically acquires, verifies, organizes, audits, and integrates Michael Huddleston / ICT knowledge for later use inside TOS.

The objective is to reconstruct what ICT actually teaches, video-by-video and evidence-by-evidence. It is not to create a better version of ICT, reinterpret ICT through another methodology, or prematurely design trading agents.

## 2. Core Architecture
```text
TOS ENGINEER / HUMAN AUTHORITY
            |
            v
SOURCE SCOUT / CORPUS CONTROLLER
            |
            v
MASTER SOURCE REGISTRY
            |
   +--------+---------+---------+----------------+
   |                  |                   |
CURRICULUM         CONCEPT           TIME & PRICE     MODEL/STRATEGY
RESEARCHER         RESEARCHER          RESEARCHER       RESEARCHER
   +------------------+-------------------+-------------+
                              |
                              v
                       EVIDENCE PACKAGE
                              |
                              v
                    ICT AUDITOR / TRUTH
                         CONTROLLER
                              |
                         PASS / REWORK
                              |
                              v
                    KNOWLEDGE ARCHITECT
                              |
                              v
                    MASTER ICT KNOWLEDGE
                         BASE + COVERAGE
```

An independent **Test Engineer / Quality Control Agent** operates across the research team and tests the work products of the other agents.

## 3. Human Authority
The TOS Engineer is system owner, architect, final decision-maker, change-control authority, final reviewer, and final monitor.

Agents may research, classify, audit, test, recommend, and report. They may not silently change architecture, rules, source policy, scope, or other frozen decisions.

## 4. Research Team

### 4.1 ICT Source Scout / Corpus Controller
**Mission:** Build and maintain the authoritative ICT source corpus and deterministic research batches.

Responsibilities:
- discover official ICT sources, playlists, and videos
- maintain source registry
- preserve chronology
- detect duplicates
- track accessibility/transcript/archive status
- assign deterministic corpus batches
- prevent uncontrolled browsing
- track research status

Non-scope: no interpretation of ICT concepts or creation of trading rules.

### 4.2 ICT Curriculum Researcher
**Mission:** Recover ICT teaching sequence, prerequisites, curriculum structure, evolution, and chronology.

For each video:
- curriculum position
- prerequisites
- new vs continuation vs recap/revision
- terminology evolution
- teaching chronology
- explicit changes/refinements
- supported relationships between lessons

### 4.3 ICT Concept Researcher
**Mission:** Extract concepts from primary evidence.

For each video:
- concepts
- definitions
- terminology
- purpose
- conditions
- relationships
- distinctions
- explicit rules
- examples
- exceptions
- limitations
- contradictions
- unknowns

**Critical rule:** An example is not a rule unless ICT explicitly generalizes it as a rule.

No external theory injection or silent SMC/Wyckoff/Elliott merging.

### 4.4 ICT Time & Price Researcher
**Mission:** Extract ICT's treatment of time and price while preserving their separation until primary evidence establishes a relationship.

Time ledger:
- time, session, day/week/month
- timeframe
- temporal conditions
- sequence
- timing relationships

Price ledger:
- price, highs/lows, ranges
- liquidity, structure
- displacement, imbalance/FVG
- premium/discount
- reference levels
- price conditions

TIME <-> PRICE relationships are recorded only when supported by evidence.

### 4.5 ICT Model & Strategy Researcher
**Mission:** Identify ICT models, frameworks, setups, entry conditions, confirmations, invalidations, and applications.

Use these as research classifications:
- Concept
- Definition
- Framework
- Model
- Setup
- Entry Condition
- Confirmation
- Invalidation
- Example/Application

Do not present this classification as an official ICT hierarchy unless ICT explicitly establishes it.

### 4.6 ICT Auditor / Truth Controller
**Mission:** Gate information before integration.

Audit:
- source origin
- evidence support
- terminology accuracy
- context preservation
- chronology
- fact vs interpretation
- external contamination
- unsupported inference
- contradictions
- missing evidence
- duplicates

Outcomes: PASS, REWORK, CONFLICT, BLOCKED, UNKNOWN.

### 4.7 ICT Knowledge Architect
**Mission:** Integrate only validated/audited knowledge.

Outputs:
- Master ICT Source Map
- Concept Inventory
- Terminology Dictionary
- Time Framework
- Price Framework
- Models & Setups Library
- Evolution Map
- Evidence Database
- Knowledge Graph
- Coverage Matrix

May not invent missing knowledge.

## 5. Independent Test Engineer / Quality Control
The QC Agent is an independent quality-control function, not another content researcher.

It tests whether:
1. assigned work was actually performed
2. output satisfies the agent contract
3. evidence supports claims
4. required fields exist
5. scope was respected
6. unsupported assumptions entered
7. agents produced conflicts
8. videos/sections were skipped
9. reports are reproducible and inspectable
10. handoffs contain required information

A completion claim without sufficient evidence may fail QC.

QC status:
- COMPLETE
- PARTIAL
- BLOCKED
- FAILED
- CONFLICT

## 6. Mandatory Reporting
Every research agent must produce a report after its assigned work. No silent completion.

Reports must be inspectable by both the TOS Engineer and the QC Agent.

Minimum report:
```yaml
report_id:
agent_id:
agent_version:
batch_id:
video_id:
video_title:
source_url:
execution_timestamp:
scope_assigned:
scope_completed:
scope_not_completed:
source_status:
evidence_records:
facts:
interpretations:
assumptions:
contradictions:
unknowns:
errors_or_limitations:
confidence:
validation_performed:
handoff:
qc_status:
```

Every material claim must be traceable to evidence.

## 7. Evidence Policy
Source hierarchy:
1. Direct original ICT teaching
2. Official ICT material
3. Archived original ICT teaching
4. Third-party transcripts/archives for discovery or verification
5. Third-party explanations as secondary references

Status labels:
- PRIMARY-VERIFIED
- SECONDARY-VERIFIED
- UNVERIFIED
- INTERPRETATION
- CONFLICT
- UNKNOWN

Agents must never claim to have watched or verified material they could not access.

## 8. Formal Batch Protocol
A Research Batch is a deterministic corpus segment.

Example:
```text
BATCH 001
Corpus: ICT 2022 Mentorship
Videos: Introduction -> Episode N
Coverage requirement: 100% of assigned videos
```

Per-video lifecycle:
```text
DISCOVERED -> INVENTORIED -> ASSIGNED -> RESEARCHED
-> REPORTED -> QC TESTED -> AUDITED -> INTEGRATED
```

A video is not complete if any mandatory track is incomplete.

Track statuses:
- Curriculum
- Concept
- Time/Price
- Model/Strategy
- QC
- Audit
- Integration

## 9. Anti-Dispersion Rules
1. Agents work only on assigned corpus/batch.
2. No uncontrolled jumps to unrelated videos.
3. No premature architecture design.
4. No premature trading-agent design.
5. No external theory contamination.
6. No unsupported synthesis.
7. Contradictory evidence is preserved.
8. Examples are not rules without explicit support.
9. No COMPLETE status without a report.
10. No COMPLETE status without required QC/audit gates.
11. Missing evidence remains visible.
12. Every handoff lists unresolved items.

## 10. Daily Operational Update
The final TOS must support a daily trading-cycle report.

Each trading day, where applicable, the system should study and report:
- market context
- higher-timeframe context
- time conditions
- price conditions
- liquidity events
- structure changes
- displacement / imbalance events
- relevant ICT concepts
- sessions and timing
- models/setups observed
- expected vs actual behavior
- invalidated hypotheses
- important deviations
- evidence
- unresolved questions
- lessons for future monitoring

Every report must distinguish:
**OBSERVED FACTS -> INTERPRETATION -> HYPOTHESIS -> VALIDATED LESSON**

Hindsight must not silently become fact.

## 11. Weekly Update
Every week the system must:
1. detect newly published ICT material
2. register new videos
3. assign them to the appropriate research/update stream
4. research and QC them
5. compare them with the existing knowledge base
6. detect additions, refinements, changes, contradictions, and terminology changes
7. update knowledge structures without silently deleting history
8. produce a Weekly ICT/TOS Update Report

Historical teaching and later revisions must remain traceable by chronology.

## 12. Controlled Continuous Improvement
TOS must support self-improvement, but agents must not freely rewrite their own rules.

```text
DAILY OPERATION
      -> DAILY REPORT
      -> ERROR / GAP / MISS DETECTION
      -> QC ANALYSIS
      -> IMPROVEMENT CANDIDATE
      -> VALIDATION
      -> ENGINEER REVIEW
      -> APPROVED CHANGE
      -> VERSIONED UPDATE
      -> NEXT CYCLE
```

Improvement inputs may include:
- missed evidence
- false positives
- classification errors
- incomplete reports
- repeated QC failures
- recurring market observations
- new ICT explanations
- contradictions
- terminology changes
- handoff failures
- operational bottlenecks

Every improvement must be explicit, traceable, testable, versioned, reversible, and approved through Change Control.

## 13. Knowledge Layers
The final system maintains three complementary layers:

```text
HISTORICAL ICT KNOWLEDGE
        |
CURRENT ICT KNOWLEDGE / NEW TEACHING
        |
CURRENT MARKET OBSERVATIONS
        |
DAILY + WEEKLY REPORTS
        |
VALIDATED OPERATIONAL LESSONS
```

These layers must not be conflated. Market observation is not automatically an ICT rule, and a hypothesis is not automatically validated knowledge.

## 14. Change Control
This Stage B handoff introduces/clarifies:
- mandatory research-agent reporting
- independent Test Engineer / QC function
- deterministic corpus/batch control
- daily operational reporting
- weekly ICT update cycle
- controlled continuous-improvement loop

These are Stage B design additions and must be incorporated into the repository through normal version/change control. No frozen baseline rule is silently overwritten.

## 15. Standard Agent Result Contract
```xml
<agent_result>
  <status>COMPLETE|PARTIAL|BLOCKED|CONFLICT|FAILED</status>
  <facts>...</facts>
  <interpretation>...</interpretation>
  <assumptions>...</assumptions>
  <evidence>...</evidence>
  <confidence>
    <level>HIGH|MEDIUM|LOW|UNKNOWN</level>
    <reason>...</reason>
  </confidence>
  <conclusion>...</conclusion>
  <validation>
    <passed>...</passed>
    <failed>...</failed>
    <unresolved>...</unresolved>
  </validation>
  <handoff>
    <next_agent>...</next_agent>
    <required_inputs>...</required_inputs>
    <warnings>...</warnings>
  </handoff>
</agent_result>
```

## 16. Engineering Execution Order
```text
1. Freeze Baseline
2. Stage B Handoff
3. Research Team Specification v1.0
4. Batch Control Protocol
5. Test Engineer / QC Specification
6. Agent Prompt Specifications
7. Prompt Validation
8. Pilot Batch
9. QC + Audit
10. Fix Prompt/Process Failures
11. Formal Corpus Batches
12. Knowledge Integration
13. Coverage Assessment
14. Continuous Update Pipeline
15. Daily/Weekly Operational Integration
```

Do not begin large-scale corpus research before the research infrastructure is specified.

## 17. Definition of Done for Stage B Infrastructure
Infrastructure is ready only when:
- every agent has explicit scope and non-scope
- batch boundaries are deterministic
- reporting is mandatory
- evidence is traceable
- QC is independent
- audit gates are defined
- handoffs are structured
- failures are recoverable
- coverage is measurable
- new ICT videos can enter the system
- daily market reports can feed the operational layer
- weekly updates can feed the knowledge layer
- improvement proposals can be versioned and tested

## 18. Long-Term TOS Loop
```text
ICT KNOWLEDGE
      -> MARKET CONTEXT
      -> DAILY OBSERVATION
      -> AGENT ANALYSIS
      -> QC / VALIDATION
      -> DAILY REPORT
      -> WEEKLY REVIEW
      -> NEW ICT INFORMATION
      -> KNOWLEDGE UPDATE
      -> CONTROLLED IMPROVEMENT
      -> NEXT TRADING CYCLE
```

The system must continuously move forward while preserving evidence, history, uncertainty, and human control.

## HANDOFF STATUS
**Stage B architecture direction:** ACCEPTED FOR ENGINEERING  
**Next deliverable:** Research Team Specification v1.0, followed by detailed agent prompts and QC/Test Engineer specifications.
