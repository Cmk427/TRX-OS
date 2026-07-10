# TRX Trading OS Roadmap

## Guiding Rule

TRX progresses only when a change improves verifiability, auditability, risk
discipline, or human decision quality. More trading activity is not a success
metric. Every roadmap item remains subject to the Core Principles and human
execution authority.

## v1.0 — Document Architecture Foundation

Status: completed in this repository.

- Governance layer: system contract, constitution, data-source policy, core
  principles, verification, state machine, and output contract.
- Specialist engines: market, portfolio, scanner, playbook, options, risk,
  decision, committee, Red Team, Master Decision, and execution-plan boundary.
- Repeatable playbook library for the original 15 playbook categories.
- Architecture map, authority hierarchy, workflow recovery, and consistency
  checks, plus a standalone Responsibility Matrix and Dependency Map
  (`docs/RESPONSIBILITY_MATRIX.md`, `docs/DEPENDENCY_MAP.md`).
- Fill-in Markdown templates covering the full pipeline
  (`templates/ANALYSIS_TEMPLATE.md`, `templates/DECISION_TEMPLATE.md`,
  `templates/REPORT_TEMPLATE.md`) and two fictional worked examples
  (`examples/NO_TRADE_EXAMPLE.md`, `examples/EXECUTE_LONG_CALL_EXAMPLE.md`).
- A reserved, rule-bound `workflows/` folder for future tool-level
  orchestration, with no workflow implemented yet.
- No live broker integration, autonomous trading, or fabricated market data.

## v1.1 — Operational Research Schemas

Potential next step after user approval and evaluation:

- Promote the v1.0 Markdown templates into structured (e.g. JSON/YAML) input,
  evidence-ledger, and report schemas that a tool can validate mechanically,
  while keeping the Markdown templates as the human-readable form of record.
- Define calculation conventions for scores, risk budgets, position sizing, and
  timestamp / timezone handling.
- Add validation examples and non-live test cases for workflow failures,
  vetoes, `NO TRADE`, and stale data.
- Establish a documented review process for approved data providers.

No score calibration or claim of predictive performance should be introduced
without recorded data, methodology, and verification.

## v1.2 — Journal and Performance Review

Potential next step after v1.1:

- Human-supplied trade journal schema and immutable decision-to-outcome link.
- Post-trade review of thesis quality, execution discipline, risk, and
  invalidation handling.
- Playbook tracking and performance analytics with explicit sampling limits and
  no retrospective alteration of original decisions.

## v2.0 — Controlled Learning and Strategy Extensions

Potential future work:

- Research governance for adaptive weighting based on sufficiently documented
  historical outcomes.
- Additional approved strategy modules, such as covered calls, debit spreads,
  LEAPS, and sector rotation research, each with separate risk and data rules.
- Comparative research of personalised strategy weighting without weakening the
  constitutional vetoes or creating automated execution.

## Promotion Criteria

An item moves from roadmap to implementation only when its scope, data needs,
risk impact, audit record, failure modes, output contract changes, and
consistency checks are documented and approved. Implementation must preserve
past records and never widen broker or execution authority implicitly.

---

TRX Trading OS v1.0
