# 02 — TOS Architecture

```text
TOS
├── Layer 1 — Analysis
│   ├── ICT knowledge interpretation
│   ├── Context / Content
│   ├── Time Alignment
│   ├── Price Alignment
│   ├── Multi-timeframe / macro review
│   └── Model / setup analysis
├── Layer 2 — Risk Measurement
├── Layer 3 — Timing / Scheduling
└── Layer 4 — Monitoring
```

This is a working architecture, not yet the final agent inventory.

## Separation of concerns
**Analysis:** market context, ICT-relevant information, time alignment, price alignment, models/setups and evidence.

**Risk:** measurable risk, exposure, invalidation/risk boundaries and risk constraints. It should not silently rewrite analysis.

**Timing:** temporal windows, sessions, schedules and actionability. It should not redefine price analysis without evidence.

**Monitoring:** post-analysis state tracking, changes, confirmation/invalidation and alerts. Monitoring is not the same as initial analysis.

## Agent boundaries
Each agent must define what it owns, receives, produces, refuses and escalates.

Agents must not absorb another agent's function merely because they can perform it. Scope is contractual.

## Maturity levels
0. Knowledge acquisition
1. Knowledge validation
2. Agent decomposition
3. Agent prompts
4. Orchestration
5. Operational testing
6. Production governance
