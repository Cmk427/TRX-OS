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
- Sixth-review hardening: every one of the 11 `ENGINE_INTERFACE_CONTRACT.md`
  example payloads now carries its own mandated `schema_version` field, and
  several categorical fields (Market Engine's regime/trend/breadth/
  volatility, Options Engine's assignment risk) are typed as the exact
  enums their owning documents already define instead of bare strings;
  `PARAMETER_REGISTRY.md` no longer verbatim-copies the Risk Score Layer
  1/2 tables (cross-reference only); the Portfolio Heat formula
  (`RISK_ENGINE.md` §7A) now states its ratio-to-percent unit conversion
  explicitly; Options Engine gained numeric profit-taking and loss-cutting
  review triggers (§8B) alongside its existing time-risk triggers; and
  `MASTER_DECISION_ENGINE.md` §7 now states explicitly that a Risk Engine
  Hard Reject's Risk Score of 0 is a valid computed WCS input, not
  `UNKNOWN`.
- Seventh-review hardening: `EXECUTION_ENGINE.md` gained an execution-timing
  Gap Risk rule (§3F) and session-specific Pre-Market/After-Hours order-type
  restrictions (§3D); four previously-unscaled Red Team outputs (Technical
  Failure Probability, Portfolio Compatibility, Overall Risk Severity,
  Failure Scenarios' Probability/Impact/Recovery Difficulty) now use the
  system's existing categorical convention instead of an undefined or
  false-precision numeric scale; `PORTFOLIO_ENGINE.md` §9A's dangling
  reference to an undefined "Portfolio Fit Score" was removed in favour of
  its actual mechanism; `OPTIONS_ENGINE.md`'s Liquidity Score was given its
  already-implied 0–100 scale; and `PARAMETER_REGISTRY.md`'s remaining
  verbatim-copied weight/mapping tables were replaced with
  cross-references. Added `docs/AI_AGENT_IMPLEMENTATION_GUIDE.md`, a
  non-authoritative implementer's checklist (stateless engines, never skip
  the State Machine, never override the Constitution, never invent a
  playbook, never generate missing data, how to use the Engine Interface
  Contract, how to return failure) for whatever agent framework eventually
  runs this document set.
- Eighth-review hardening — the machine schema layer: `schemas/` now
  contains 11 real, validated JSON Schema (Draft 2020-12) files, one per
  engine, mechanically derived from `ENGINE_INTERFACE_CONTRACT.md` §2–§12,
  so any AI model or tool can validate engine payloads against a common,
  unambiguous shape rather than re-interpreting prose independently.
  `playbooks/PLAYBOOK_LIBRARY.md` was fully re-templated to an identical
  12-field structure across all 15 playbooks, each with a
  `FAILURE_TAXONOMY.md`-mapped Failure Code. `STATE_MACHINE.md` gained
  explicit exception-path diagrams (§2A). `docs/AI_AGENT_IMPLEMENTATION_GUIDE.md`
  gained a Bad-vs-Good Agent Behaviour table and New Agent Checklist. Both
  worked examples gained an at-a-glance version-targeting block.
  Deliberately deferred: additional `DATA_FAILURE`/`RISK_REJECTION`/
  `RED_TEAM_REJECTION` worked examples, flagged by the user as lower
  priority.
- Ninth-review hardening — the portfolio optimization layer: a new pipeline
  state, State 17 Portfolio Optimization (`engines/PORTFOLIO_OPTIMIZATION_ENGINE.md`),
  converts an already-published Master Decision outcome into target
  weight, share count, and capital released/required, without ever
  re-deciding Hold/Reduce/Exit/Execute (`STATE_MACHINE.md` renumbered
  17→18/18→19/19→20 accordingly). `EXECUTION_ENGINE.md` gained a
  Multi-Position Trade Plan with Execution Priority (P1/P2/P3) and a
  Maximum Slippage hard cap; `OUTPUT_CONTRACT.md` gained a mandatory
  Portfolio Action Plan section. Two trigger documents —
  `engines/POSITION_MANAGEMENT_ENGINE.md` (gain/loss/earnings/IV triggers)
  and `engines/PORTFOLIO_REBALANCING_ENGINE.md` (periodic sector/theme
  drift) — deliberately hold no State Machine position of their own; each
  starts a fresh analysis run rather than extending the pipeline
  (`STATE_MACHINE.md` new §2A/§2B). `playbooks/PLAYBOOK_LIBRARY.md` gained
  a 13th shared field, Position Management, with strategy-specific
  take-profit tiers and scaling rules across all 15 playbooks. A new
  glossary, `docs/PORTFOLIO_MANAGEMENT_GUIDE.md`, reconciles Hold/Add/
  Reduce/Exit/Rotate/Optimize/Rebalance terminology in one place.
- Tenth-review hardening — Investment Policy consolidation and a Capital
  Allocation step: `system/INVESTMENT_POLICY.md` is now the sole owner of
  every position/sector/cash concentration limit and the capital-priority
  order, replacing near-duplicate copies previously held independently by
  `PORTFOLIO_ENGINE.md` and `PORTFOLIO_OPTIMIZATION_ENGINE.md`. A new
  pipeline state, State 18 Capital Allocation
  (`engines/CAPITAL_ALLOCATION_ENGINE.md`), makes the Deploy-vs-Wait
  decision for released capital its own gated step
  (`STATE_MACHINE.md` renumbered 18→19/19→20/20→21 accordingly).
  `EXECUTION_ENGINE.md` gained an explicit Execution Package requirement
  (Valid For, If Not Filled) so no action is left as a bare label.
  `PORTFOLIO_REBALANCING_ENGINE.md` gained an Optimization Score reusing
  the existing Portfolio Health Score formula. `DECISION_SNAPSHOT_POLICY.md`
  gained a Decision History policy (storage path deliberately deferred as
  a v2.1 implementation-layer concern), and a new
  `templates/PORTFOLIO_PROFILE_TEMPLATE.md` gives the human an optional
  style/risk/holding-period worksheet.

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
