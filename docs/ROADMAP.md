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
- Second-review hardening: Constraint/Publication Authority split
  (`CONSTITUTION.md` §4A), a named Conflict Resolution Protocol (§4B), a
  Human Override recording contract (§6A), computable Risk formulas
  (`RISK_ENGINE.md` §7A/§8A/§24A), a Confidence/Uncertainty split
  (`OUTPUT_CONTRACT.md` §5, `MASTER_DECISION_ENGINE.md` §14A), a fixed
  Red Team attack-category framework (`RED_TEAM_ENGINE.md` §4A), a
  Committee Consensus Score formula (`COMMITTEE_ENGINE.md` §10), a bounded
  State Machine revision cycle (§6A, `SYSTEM REVIEW REQUIRED`), an explicit
  per-state Owner/Input/Result/Failure contract (`STATE_MACHINE.md` §3), a
  locked data-criticality and conflict-resolution rule
  (`DATA_SOURCE_POLICY.md`), Portfolio Construction limits
  (`PORTFOLIO_ENGINE.md` §9A), Long Call time-risk rules
  (`OPTIONS_ENGINE.md` §8A), an explicit Execution Gate
  (`EXECUTION_ENGINE.md` §1A), a Playbook Lifecycle
  (`PLAYBOOK_LIBRARY.md` §5), and three new reference documents —
  `system/PARAMETER_REGISTRY.md`, `system/DOCUMENTATION_GOVERNANCE.md`, and
  the round-1 Decision Snapshot / Engine Interface / Failure Taxonomy
  documents, extended with Decision ID / Human Action / Outcome fields.
- Third- and fourth-review hardening (multi-pass fresh-eyes audits): a
  two-layer Risk Score model separating Hard Reject Flags from point
  deductions (`RISK_ENGINE.md` §24A); a Verification Confidence Score
  formula (`VERIFICATION_POLICY.md` §4A) and a Resilience Score formula
  (`RED_TEAM_ENGINE.md` §19) closing two previously-undefined Confidence
  Model inputs; an explicit WCS-to-Confidence band table
  (`MASTER_DECISION_ENGINE.md` §7) with the missing-value caps that never
  raise a reading, only lower it; a state-ordering fix so Options Review
  (State 11) gates on Scanner's Candidate Quality Score rather than a
  not-yet-computed Opportunity Score; a formal schema-conventions section
  in `ENGINE_INTERFACE_CONTRACT.md` §1A; a narrowly-scoped "not applicable"
  finding value for Red Team's Valuation/Execution attack categories; and a
  full terminology reconciliation pass removing stray outcome vocabulary
  (ADD/ROTATE/BUY as if they were canonical outcomes) and duplicate/
  unreconciled score names (Portfolio Score, Option Score, Market
  Confidence) across `PORTFOLIO_ENGINE.md`, `DECISION_ENGINE.md`,
  `MARKET_ENGINE.md`, and their dependent templates and examples.

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

- Human-supplied trade journal schema and immutable decision-to-outcome link,
  built on the Decision Snapshot's Human Action and Outcome fields
  (`system/DECISION_SNAPSHOT_POLICY.md` §2).
- A Post-Trade Review process (prediction vs. actual outcome, mistake type,
  improvement) — this is the "learning loop" the document architecture
  intentionally does not close in v1.0: Analysis → Decision → Execution
  exists today; Outcome → Review → Improve requires the journal data this
  version produces first.
- A Historical Validation / backtesting layer for the Playbook Library: win
  rate, drawdown, sample size, and market-regime-conditional performance per
  playbook, with explicit sampling-size caveats. Until this exists, no
  playbook may be described as validated — `PLAYBOOK_LIBRARY.md` §4 already
  prohibits representing an untested result as historical edge, and
  `OUTPUT_CONTRACT.md` §5 prohibits treating Confidence as a calibrated
  probability for the same reason.
- Confidence calibration: once sufficient journal data exists, compare
  stated Confidence against actual outcome frequency and report the
  divergence — this is what finally lets `OUTPUT_CONTRACT.md` §5's current
  "not yet calibrated" caveat be lifted, and not before.
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
- A Research Memory layer surfacing prior thesis, prior decision, and
  recorded outcome for a ticker/setup at analysis time — built on the
  append-only Decision Snapshot history accumulated since v1.0; this is a
  retrieval feature over existing records, not a new authority or veto.

## v2.1 — Implementation-Layer Concerns (once a tool-level system exists)

These items only become relevant once something in `workflows/` actually
executes this document system automatically; none are meaningful for a
purely document-driven v1.0–v2.0 and are listed here so they are not lost,
not because they are scheduled:

- Event-driven triggering (price breakout, volatility spike, earnings,
  macro release) as an alternative to running the full pipeline on a fixed
  schedule.
- An Evaluation Framework measuring each engine's accuracy, consistency,
  calibration, and false-positive rate release over release — distinct from
  Confidence Calibration above, which measures the *system's* stated
  confidence, not each engine's individual reliability.
- A Security / Access Model (read/execute/admin permissions) — relevant only
  if a future version connects to a broker or external data API, which
  itself requires a `CONSTITUTION.md` amendment before any such connection,
  not merely an engineering task.
- Environment separation (development/testing/production) so a Playbook or
  parameter change cannot silently affect a live analysis mid-edit.

## Promotion Criteria

An item moves from roadmap to implementation only when its scope, data needs,
risk impact, audit record, failure modes, output contract changes, and
consistency checks are documented and approved. Implementation must preserve
past records and never widen broker or execution authority implicitly.

---

TRX Trading OS v1.0
