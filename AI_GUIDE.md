# TOS — AI GUIDE
## External AI Entry Point | Version 1.1

> **Purpose:** This file is the entry point for any AI system that receives access to the TOS repository.
> Read and understand this guide before modifying, extending, interpreting, or operationalizing the project.

---

## 1. What Is TOS?

**TOS (Trading Operations System)** is a modular, multi-agent system being designed to organize and operationalize trading knowledge and analysis.

The system keeps the human engineer in control while specialized AI agents perform clearly bounded research, analysis, validation, measurement, timing, and monitoring tasks.

TOS is developed **layer by layer**. Agents must not operate outside their assigned scope.

---

## 2. The Human Engineer

The human remains the:

- Engineer
- System architect
- Final decision-maker
- Main monitor / supervisor

AI agents are specialized components of the system. They must not silently redefine the architecture, rules, knowledge, or objectives.

---

## 3. Current Project Mission

### Current priority: ICT Knowledge Acquisition

The project is **not yet at the stage of finalizing the complete TOS trading-agent inventory**.

The immediate priority is to build a reliable knowledge base of the teachings of **Michael J. Huddleston (ICT / The Inner Circle Trader)**.

The intended sequence is:

```text
ICT Source Discovery
        ↓
ICT Knowledge Research
        ↓
Evidence Extraction
        ↓
Independent Research
        ↓
Audit / Validation
        ↓
Master ICT Knowledge Base
        ↓
Knowledge Coverage Assessment
        ↓
Derive TOS Agent Inventory
        ↓
Agent Specifications
        ↓
Agent Prompts
        ↓
Orchestration
        ↓
Testing
        ↓
Operational TOS
```

### Critical rule

**Do not prematurely finalize TOS trading agents before the ICT Knowledge Acquisition and validation stages are sufficiently complete.**

Agent architecture should be derived from the validated knowledge structure rather than from assumptions made beforehand.

---

## 4. Required Reading Order

An AI entering the project should read these files in order.

### Step 1 — Bootstrap Context
`TOS_External_Memory/12_AI_BOOTSTRAP_CONTEXT.md`

Purpose: obtain the compressed project context.

### Step 2 — Project Overview
`TOS_External_Memory/01_PROJECT_OVERVIEW.md`

Purpose: understand the project's purpose, philosophy, and human role.

### Step 3 — TOS Architecture
`TOS_External_Memory/02_TOS_ARCHITECTURE.md`

Purpose: understand the layered architecture, separation of concerns, and agent boundaries.

### Step 4 — Rules and Decisions
`TOS_External_Memory/10_RULES_AND_DECISIONS.md`

Purpose: understand established project rules and decisions.

Treat established decisions as constraints unless a justified project-level change is explicitly made.

### Step 5 — Prompt Engineering Standard
`TOS_External_Memory/05_PROMPT_ENGINEERING_STANDARD.md`

Purpose: understand the prompt-engineering methodology, GCAO+V, validation, evidence, scope, and structured outputs.

### Step 6 — ICT Knowledge Acquisition
`TOS_External_Memory/04_ICT_KNOWLEDGE_ACQUISITION.md`

Purpose: understand the current mission, ICT research team, source hierarchy, evidence, and validation requirements.

### Step 7 — Supporting Specifications

When designing or modifying agents, also consult:

- `06_MASTER_PROMPT_TEMPLATE.md`
- `07_AGENT_SPECIFICATION.md`
- `08_OUTPUT_AND_HANDOFF_SCHEMA.md`
- `09_RESEARCH_WORKFLOW.md`
- `11_ROADMAP.md`

---

## 5. Core Project Principle

The objective of the ICT research phase is **not** to create the AI's preferred interpretation of ICT.

The objective is:

> **To reconstruct, as accurately and traceably as possible, what Michael Huddleston actually taught.**

---

## 6. ICT Source Hierarchy

Use this priority:

1. Direct original teaching by Michael Huddleston.
2. Official ICT publications and channels.
3. Archived copies of original ICT teaching.
4. Third-party transcripts, indexes, or archives — only as discovery or verification aids.
5. Third-party explanations — secondary reference only.

A third-party explanation must **never** be presented as Michael Huddleston's teaching unless independently supported by a primary source.

Community consensus is not equivalent to primary-source evidence.

---

## 7. Research Integrity Rules

AI agents must:

- Separate fact from interpretation.
- Separate assumptions from evidence.
- Identify sources of important claims.
- Preserve chronology where relevant.
- Preserve terminology accurately.
- Identify uncertainty and missing evidence.
- Preserve contradictions rather than silently resolving them.
- Say `I DON'T KNOW` when evidence is insufficient.

AI agents must not:

- Invent missing rules.
- Fill evidence gaps with assumptions.
- Attribute unsupported claims to ICT.
- Mix third-party frameworks into ICT without explicit evidence.
- Rewrite ICT concepts according to another trading methodology.
- Treat community terminology as official ICT terminology without verification.
- Hide contradictions between sources.
- Claim completeness when important evidence remains inaccessible or unresolved.

---

## 8. Time and Price Separation

A fundamental TOS design principle is the separation of:

- **Time Alignment**
- **Price Alignment**

They may interact when the source material explicitly establishes a relationship, but they must not be merged prematurely into one analytical domain.

Analysis must also remain distinct from:

- Risk Measurement
- Timing / Scheduling
- Monitoring

This separation preserves clear agent responsibilities and prevents analytical contamination.

---

## 9. Agent Scope Is a Contract

Every TOS agent must have explicit:

- Identity
- Role
- Mission
- Scope
- Non-scope
- Inputs
- Outputs
- Upstream dependencies
- Downstream handoff
- Authority
- Validation rules
- Escalation rules

An agent must not silently perform another agent's job.

If a task is outside scope:

1. State that it is outside scope.
2. Do not improvise.
3. Identify the appropriate agent when known.
4. Escalate when necessary.

---

## 10. Evidence and Knowledge Status

Where applicable, use:

- `PRIMARY-VERIFIED`
- `SECONDARY-VERIFIED`
- `UNVERIFIED`
- `INTERPRETATION`
- `CONFLICT`

These labels describe knowledge status, not the confidence of a trading prediction.

Confidence should be reported separately where required.

---

## 11. Standard Output Philosophy

TOS outputs should be:

- Structured
- Traceable
- Machine-readable where appropriate
- Evidence-aware
- Explicit about uncertainty
- Suitable for downstream handoff

Recommended result structure:

```xml
<agent_result>
  <status>COMPLETE|PARTIAL|BLOCKED|CONFLICT</status>
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

This standard may be refined through versioned iteration.

---

## 12. Prompt Engineering Standard

TOS prompts should follow:

```text
SYSTEM ID
IDENTITY
ROLE
MISSION / GOAL
CONTEXT
INPUTS
ACTION
RULES
DO
DON'T
SOURCE POLICY
EVIDENCE POLICY
KNOWLEDGE STATUS
VALIDATION
OUTPUT FORMAT
HANDOFF
ESCALATION
ITERATION
VERSION
```

Core methodology:

- Be clear and direct.
- Provide context and why.
- Specify Do / Don't.
- Specify output format.
- Assign an explicit role.
- Use examples when useful.
- Separate data from instructions.
- Use structured tags where useful.
- Distinguish fact / interpretation / assumption.
- Allow `I DON'T KNOW`.
- Design for validation.
- Work outcome-first.
- Iterate from observed failures.

See:
`TOS_External_Memory/05_PROMPT_ENGINEERING_STANDARD.md`

---

## 13. Do Not Confuse Research With Interpretation

### Fact
Directly supported by the source.

### Interpretation
A conclusion or explanation produced by the research team.

### Assumption
Something required to proceed but not established by evidence.

### Conflict
Two or more sources or teaching periods appear to differ.

When sources conflict, do not select the version that merely "sounds right." Record the competing claims, sources, dates, context, and unresolved status.

---

## 14. Completeness Rule

Never claim the ICT knowledge base is complete unless the project's operational definition of completeness has been satisfied.

At minimum:

- Relevant primary sources have been inventoried.
- Major knowledge domains have coverage assessment.
- Important claims have evidence status.
- Inaccessible material is explicitly recorded.
- Unresolved gaps are explicitly recorded.
- Contradictions are preserved.
- The final TOS agent inventory can be traced to knowledge domains.

Absolute completeness cannot be assumed.

---

## 15. Change Control

Do not silently change:

- Project objectives
- Core architecture
- Established rules
- Source hierarchy
- Agent boundaries
- Knowledge status definitions
- Prompt standards

If a change is necessary:

1. Explain why.
2. Identify affected artifacts.
3. Propose the change.
4. Preserve traceability.
5. Version the change.
6. Update relevant documentation.

---

## 16. Before Starting Any New Task

The AI should determine:

1. What project layer does this task belong to?
2. What is the current project stage?
3. Is the task inside the requested agent's scope?
4. What evidence or source is required?
5. Which project document governs this task?
6. What output format is required?
7. Which agent or process receives the result next?
8. Is there uncertainty or missing evidence?

If these cannot be answered, pause and clarify rather than improvising.

---

## 17. Entry Checklist for a New AI

Before substantive work, the AI should be able to answer:

- What is TOS?
- What is the current mission?
- What role does the human engineer have?
- What are the main TOS layers?
- Why are Time and Price separated?
- Why is ICT Knowledge Acquisition currently prioritized?
- What is the ICT source hierarchy?
- How are evidence and interpretation separated?
- What should happen when evidence is missing?
- What should happen when sources conflict?
- What is the prompt architecture?
- What is the agent scope rule?
- What is the next expected project stage?

If these are not clear, continue reading the repository before designing or changing system components.

---

## 18. Repository Entry Point

Repository:

https://github.com/Sadeghsaadat7/T.OS-project-

`AI_GUIDE.md` is the **AI entry point**.

`README.md` is the primary **human-facing project introduction**.

`TOS_External_Memory/` contains the structured external memory.

---

## 19. Final Operating Principle

> **Understand before modifying.**
>
> **Evidence before interpretation.**
>
> **Scope before action.**
>
> **Validation before acceptance.**
>
> **Knowledge before architecture.**
>
> **Human engineer remains in control.**

---

**Document:** AI_GUIDE.md  
**Version:** 1.1  
**Status:** ACTIVE  
**Project:** TOS — Trading Operations System
