# 05 — TOS Prompt Engineering Standard

This standard consolidates the seven-page handwritten prompt-engineering notes supplied for the project.

## 1. Core principles
- **Be Clear and Direct:** state exactly what the agent must do.
- **Context and Why:** explain project context, purpose and constraints.
- **Do / Don't:** explicitly define allowed and prohibited behavior.
- **Output Format:** define fields, structure and data expectations.
- **Assign a Role:** give the agent a professional identity appropriate to the task.
- **Role Prompting:** use role to constrain behavior, expertise and responsibility.
- **System Prompt:** define persistent mission, rules, boundaries, validation and output behavior.
- **Use Example:** demonstrate desired behavior when the task is subtle.
- **Few-shot:** use several high-quality examples when pattern learning is useful.
- **Zero-shot:** use when the task can be specified precisely without examples.
- **Separate Data:** keep instructions and research data clearly separated.
- **XML Tags:** use semantic sections to improve prompt structure.

## 2. Reliability principles

### Chain of Thought
Step-by-step reasoning can help internally, but TOS should not require disclosure of private chain-of-thought. Instead require structured reasoning summaries, evidence, validation and uncertainty.

### Allow “I don't know”
Agents must be allowed to report unknown, insufficient evidence, inaccessible source, conflicting evidence or need for review.

### Hallucination awareness
Never invent facts, sources, timestamps, quotations, definitions, relationships or missing data.

### Outcome-first
Define the desired result before describing the process.

### Fact vs Interpretation vs Assumption
- **Fact:** directly observed/sourced.
- **Interpretation:** analytical meaning assigned to facts.
- **Assumption:** temporary premise used because evidence is incomplete.

## 3. Anatomy of a great prompt
Base structure from the notes:
1. Role
2. Context
3. Instruction
4. Format
5. Example
6. Iterate

TOS extension:
7. Mission / Goal
8. Inputs
9. Rules
10. Do / Don't
11. Source Policy
12. Validation
13. Handoff
14. Version

## 4. GCAO+V
- **G — Goal:** what must ultimately be achieved?
- **C — Context:** what background and constraints are required?
- **A — Action:** what exactly should the agent do?
- **O — Output:** what must it return and in what format?
- **V — Validation:** how will correctness be checked?

## 5. Canonical TOS prompt architecture
```text
1. Identity
2. Role
3. Mission / Goal
4. Context
5. Inputs
6. Action
7. Rules
8. Do / Don't
9. Source Policy
10. Validation
11. Output Format
12. Handoff
13. Iteration / Escalation
14. Version
```

## 6. Prompt iteration
```text
Draft → Test → Observe failure → Identify ambiguity → Improve → Retest → Version
```

Prompt changes should be driven by observed failures, not random rewriting.

## 7. Prompt improver
A prompt-improver agent may improve clarity, ordering and ambiguity while preserving project intent, scope, non-negotiable rules, output contract and source policy. It must not introduce new domain assumptions.
