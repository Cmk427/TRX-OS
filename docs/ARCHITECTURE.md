# TRX Trading OS v1.0 Architecture

## Purpose

TRX Trading OS is a document-driven AI Trading Research Operating System. It
does not place orders or provide automated trading. This architecture makes
research outputs verifiable, auditable, risk-constrained, and extensible while
keeping human authority over all capital decisions.

## Repository Structure

```text
README.md                         Product overview and entry point
system/                           Governance, principles, policies, workflow, output
  SYSTEM.md                       System scope and contract
  CONSTITUTION.md                 Responsibility and veto hierarchy
  CORE_PRINCIPLES.md              Immutable trading-safety principles
  OUTPUT_CONTRACT.md              Final report requirements
  STATE_MACHINE.md                Sole workflow source of truth
  VERIFICATION_POLICY.md          V1–V5 evidence classification
  DATA_SOURCE_POLICY.md           Source quality, freshness, critical-data rules
  DECISION_SNAPSHOT_POLICY.md     What is locked/recorded per published decision, plus Decision History policy
  ENGINE_INTERFACE_CONTRACT.md    Structured engine-to-engine schema (naming/shape only)
  FAILURE_TAXONOMY.md             Classification of every non-EXECUTE outcome
  PARAMETER_REGISTRY.md           Numeric-threshold index (derived view; owning engine wins on conflict)
  DOCUMENTATION_GOVERNANCE.md     Header standard (Owner/Last Updated) for new documents only
  INVESTMENT_POLICY.md            Sole owner of position/sector/cash concentration limits and capital-priority principles
engines/                          Specialist research and decision components
  PORTFOLIO_OPTIMIZATION_ENGINE.md Post-Master-Decision target weight/shares/capital (State 17)
  CAPITAL_ALLOCATION_ENGINE.md     Deploy-or-Wait decision for released capital (State 18)
  POSITION_MANAGEMENT_ENGINE.md    Trigger-based ongoing position review (no State Machine position — starts a new run)
  PORTFOLIO_REBALANCING_ENGINE.md  Periodic sector/theme drift correction (no State Machine position — starts a new run)
playbooks/                        Repeatable, rule-based setup definitions
docs/                             Architecture, roadmap, and cross-reference maps
  ARCHITECTURE.md                 This document
  ROADMAP.md                      Version plan and promotion criteria
  RESPONSIBILITY_MATRIX.md        Quick-reference authority/veto index (derived from CONSTITUTION.md)
  DEPENDENCY_MAP.md                Document dependency graph and forward-dependency checks
  AI_AGENT_IMPLEMENTATION_GUIDE.md Non-authoritative implementer's checklist (derived index)
  PORTFOLIO_MANAGEMENT_GUIDE.md    Non-authoritative glossary (Hold/Add/Reduce/Exit/Rotate/Optimize/Rebalance)
schemas/                          Machine-readable JSON Schema rendering of the Engine Interface Contract (derived, non-authoritative — see schemas/README.md)
  (includes capital_allocation.schema.yaml, portfolio_optimization.schema.yaml)
templates/                        Fill-in worksheets aligned to State Machine stages
  ANALYSIS_TEMPLATE.md            States 02–11 (input through options review)
  DECISION_TEMPLATE.md            States 12–20 (ranking through self audit)
  REPORT_TEMPLATE.md              State 21 — direct fill-in version of OUTPUT_CONTRACT.md
  PORTFOLIO_PROFILE_TEMPLATE.md   Blank worksheet — do not edit directly, copy to memory/
memory/                           Human-maintained instance data (not runtime-accumulating state)
  PORTFOLIO_PROFILE.md             Populated copy of the template above, at a fixed path engines reference
workflows/                        Reserved for future tool-level orchestration definitions
examples/                         Fictional worked examples of a completed report
```

## Workflow and Data Flow

`STATE_MACHINE.md` is the only document that defines the processing order.

```text
Validate Input → Verify Data → Market & Macro → Portfolio
  → Preliminary Risk → Positions → Scanner → Playbook → Options (conditional)
  → Ranking → Committee → Red Team → Final Risk → Master Decision
  → Portfolio Optimization → Capital Allocation
  → Execution Plan (conditional) → Self Audit
  → Final Report
```

Ongoing position monitoring and periodic sector/theme rebalancing are
deliberately not further steps in this diagram — per `STATE_MACHINE.md`
§2B, `engines/POSITION_MANAGEMENT_ENGINE.md` and
`engines/PORTFOLIO_REBALANCING_ENGINE.md` are trigger documents that each
start a **new** run through the diagram above, rather than extending it.

The workflow deliberately evaluates existing holdings and risk before searching
for new opportunities. `NO TRADE` is a completed outcome. A critical data
failure results in `INSUFFICIENT VERIFIED INFORMATION`; it never receives an
invented substitute.

## Authority Model

Per `CONSTITUTION.md` §4A, "authority" splits into two non-competing kinds:
**Constraint Authority** (the binding power to forbid an outcome) and
**Publication Authority** (the sole right to integrate and emit the one
final report). A component never holds both meaningfully — Risk Engine's
Constraint Authority does not make it a publisher, and Master Decision
Engine's Publication Authority does not make it a constraint.

| Layer | Kind | Authority |
|---|---|---|
| Human trader | — (outside the system) | Sole capital and order-execution authority |
| Core Principles / data controls | Constraint | Non-negotiable safety and evidence constraints |
| Risk Engine | Constraint | Binding preliminary and final risk veto |
| Red Team Engine | Constraint | Binding veto for a documented critical risk |
| State Machine | Constraint (procedural) | Valid order of work, recovery transitions, and the §6A revision-cycle limit |
| Master Decision Engine | Publication | Only publisher of the final integrated outcome; holds no veto |
| Committee Engine | Neither | Independent multi-role assessment; preserves dissent |
| Investment Policy | Neither (governance data, not a runtime veto) | Defines position/sector/cash limits and capital-priority principles; `PORTFOLIO_ENGINE.md` §17 and Risk Engine sizing enforce them, this document computes and vetoes nothing itself |
| Specialist engines | Neither | Evidence, analysis, classification, and proposed plans |

The Master Decision Engine integrates outputs but cannot override an upstream
critical-data failure, Risk Engine hard veto, or Red Team critical-risk veto.
`CONSTITUTION.md` §4B Conflict Resolution Protocol states this as one fixed
priority order (Risk veto > Red Team veto > Committee majority > Master
Decision integration) for the case where these disagree.

## Engine Boundaries

| Engine | Owns | Does not own |
|---|---|---|
| Market | Regime, breadth, volatility, macro and sector context | Trade selection |
| Portfolio | Current holdings, capacity, concentration, correlation | Risk override |
| Scanner | Candidate universe, eligibility, preliminary ranking | Final opportunity decision |
| Playbook | One repeatable setup classification | New trade authorisation |
| Options | Long Call versus underlying analysis | Independent underlying thesis |
| Risk | Position / portfolio risk and vetoes | Data verification or order placement |
| Decision | Candidate Opportunity Score and decision package | Final output |
| Committee | Independent expert-style votes and dissent | Final output / risk override |
| Red Team | Counter-evidence and critical-risk challenge | Suppressing evidence or dissent |
| Master Decision | Final integration and report publication | Veto override |
| Portfolio Optimization | Target weight, share count, capital released for an already-published existing-position outcome (Hold/Reduce/Exit/Watch) | Re-decide Hold/Reduce/Exit/Watch, override Master Decision, or process an EXECUTE decision |
| Capital Allocation | Deploy/Rotate/Reserve/Wait decision for capital, and sizing every new-position EXECUTE candidate (bounded by Risk Engine's approval) | Invent a new investment opportunity, or size beyond Risk Engine's approval |
| Execution | Human review plan and re-verification checklist (Execution Package: order, price, Valid For, If Not Filled) | Broker access or order placement |
| Position Management *(trigger document, no State Machine position)* | Determine when a gain/loss/earnings/IV trigger starts a new run | Publish a position decision itself |
| Portfolio Rebalancing *(trigger document, no State Machine position)* | Determine when periodic sector/theme drift starts a new run | Assign a specific new ticker, or bypass Scanner/Committee/Risk |

## Evidence and Auditability

Every material factual claim is classified V1–V5 and governed by source tier,
timestamp, freshness, and conflict rules. Each completed analysis retains an
evidence ledger, engine outcomes, gate results, assumptions, dissent, outcome,
invalidation, review triggers, and document versions — collected into one
Decision Snapshot per `DECISION_SNAPSHOT_POLICY.md`. Historical analyses are
append-only; future learning may alter future rules but not past records. Any
non-`EXECUTE` outcome is additionally classified under
`FAILURE_TAXONOMY.md` for audit purposes.

## Release Version Matrix

"TRX Trading OS v1.0" (the product-level version stamped at the foot of
every document) is a composite of many independently-versioned documents,
not one number — a Decision Snapshot's "engine document versions used"
(`DECISION_SNAPSHOT_POLICY.md` §2) only makes sense if there is one place
that says what the current, coherent set of versions actually is. This
table is that place:

```text
system/     CORE_PRINCIPLES 1.0.0 · CONSTITUTION 1.2.0 · SYSTEM 1.3.0
            OUTPUT_CONTRACT 1.3.0 · STATE_MACHINE 1.6.0
            VERIFICATION_POLICY 1.2.0 · DATA_SOURCE_POLICY 1.1.0
            DECISION_SNAPSHOT_POLICY 1.3.0 · ENGINE_INTERFACE_CONTRACT 1.7.0
            FAILURE_TAXONOMY 1.1.0 · PARAMETER_REGISTRY 1.7.0
            DOCUMENTATION_GOVERNANCE 1.0.0 · INVESTMENT_POLICY 1.0.0
engines/    MARKET 1.3.0 · PORTFOLIO 1.4.0 · SCANNER 1.0.0 · PLAYBOOK 1.1.0
            OPTIONS 1.4.0 · RISK 1.3.0 · DECISION 1.2.0
            MASTER_DECISION 1.4.0 · COMMITTEE 1.4.0 · RED_TEAM 1.5.0
            PORTFOLIO_OPTIMIZATION 1.2.0 · EXECUTION 1.5.0
            CAPITAL_ALLOCATION 1.1.0
            POSITION_MANAGEMENT 1.1.0 · PORTFOLIO_REBALANCING 1.1.0
playbooks/  PLAYBOOK_LIBRARY 1.4.0
schemas/    13 files, each 1.0.0 except execution.schema.yaml 1.2.0,
            portfolio_optimization.schema.yaml 1.1.0, and
            capital_allocation.schema.yaml 1.1.0 — see schemas/README.md
templates/  ANALYSIS 1.3.0 · DECISION 1.5.0 · REPORT 1.6.0
            PORTFOLIO_PROFILE 1.1.0
examples/   NO_TRADE 1.8.0 · EXECUTE_LONG_CALL 1.8.0
docs/       RESPONSIBILITY_MATRIX 1.4.0 · DEPENDENCY_MAP 1.8.0
            AI_AGENT_IMPLEMENTATION_GUIDE 1.1.0
            PORTFOLIO_MANAGEMENT_GUIDE 1.0.0
memory/     PORTFOLIO_PROFILE.md — deliberately unversioned; it is
            human-edited instance data, not a governed spec document
workflows/  WORKFLOWS 1.0.0 (unchanged — still a placeholder)
```

A document at 1.0.0 above genuinely has not changed since the original
architecture pass (`SCANNER_ENGINE.md`, `CORE_PRINCIPLES.md`,
`DOCUMENTATION_GOVERNANCE.md`, `WORKFLOWS.md`) — that is a fact about their
history, not a sign they were skipped. Each whole revision level above 1.0
reflects one round of substantive fixes. `examples/` are at 1.5.0 (five
revision rounds) and `MASTER_DECISION_ENGINE.md`/`COMMITTEE_ENGINE.md`/
`MARKET_ENGINE.md` are at 1.3.0/1.4.0 partly because a fifth audit round
found that an earlier fix (removing an unscoped "WCS &lt; 70 caps Confidence
at Low" rule from `MASTER_DECISION_ENGINE.md` §7) had not fully
propagated — the same disavowed rule was still quoted in that document's
own §17, `PARAMETER_REGISTRY.md` §8, and `templates/DECISION_TEMPLATE.md`
§6. This is recorded here deliberately: propagation gaps are exactly the
failure mode this matrix and the Decision Snapshot policy exist to catch,
and pretending a fix round found nothing would defeat that purpose.

A sixth audit round bumped `ENGINE_INTERFACE_CONTRACT.md` (1.2.0 → 1.3.0),
`RISK_ENGINE.md` and `OPTIONS_ENGINE.md` (1.2.0 → 1.3.0),
`MASTER_DECISION_ENGINE.md` and `COMMITTEE_ENGINE.md` (1.3.0 → 1.4.0), and
`PARAMETER_REGISTRY.md` (1.3.0 → 1.4.0): every one of the 11 Engine
Interface Contract example payloads was missing its own mandated
`schema_version` field, several categorical fields (Market Engine's
`trend`/`breadth`/`volatility`/`regime`, Options Engine's
`assignment_risk`) were typed as bare strings instead of the enums their
owning documents already define, `PARAMETER_REGISTRY.md` had verbatim-copied
(not cross-referenced) the Risk Score Layer 1/Layer 2 tables, the Portfolio
Heat formula's ratio-vs-percent unit conversion was never stated, Options
Engine had no numeric profit-taking or loss-cutting trigger (only named
fields with no threshold), and the Master Decision Engine never stated
whether a Risk Engine Hard Reject's Risk Score of exactly 0 counts as a
normal computed value or an `UNKNOWN` for WCS-blending purposes.

A seventh audit round bumped `EXECUTION_ENGINE.md` (1.1.0 → 1.2.0),
`RED_TEAM_ENGINE.md` (1.2.0 → 1.3.0), `PORTFOLIO_ENGINE.md` (1.2.0 →
1.3.0), `OPTIONS_ENGINE.md` (1.3.0 → 1.4.0 — its second bump this cycle),
`ENGINE_INTERFACE_CONTRACT.md` (1.3.0 → 1.4.0 — likewise), and
`PARAMETER_REGISTRY.md` (1.4.0 → 1.5.0 — likewise): Execution Engine gained
an explicit gap-risk order-timing rule (§3F) and session-specific
Pre-Market/After-Hours order-type restrictions (§3D); four Red Team outputs
(Technical Failure Probability, Portfolio Compatibility, Overall Risk
Severity, and the Failure Scenarios' Probability/Impact/Recovery
Difficulty) that previously had no stated scale now use the same
categorical convention already established elsewhere in the system, rather
than an invented numeric score that would misrepresent how precise these
judgment calls actually are; Portfolio Engine's dangling reference to an
undefined "Portfolio Fit Score" was removed in favour of its actual
mechanism (the §9A concentration limits and §17 qualitative check); Options
Engine's Liquidity Score was given its already-implied 0–100 scale; and
Parameter Registry's remaining verbatim-copied weight/mapping tables
(Confidence Model weights, Resilience Score deduction, Committee role
weights, vote-to-points mapping) were replaced with cross-references, the
same treatment already applied to the Risk Score tables the round before.
This round also added `docs/AI_AGENT_IMPLEMENTATION_GUIDE.md`, a
non-authoritative implementer's checklist for whatever agent framework
eventually runs this document set — same "owning document always wins"
relationship as `PARAMETER_REGISTRY.md`.

An eighth audit round added the **machine schema layer** the user had
flagged as the single most important remaining gap across three
consecutive review rounds: `schemas/` now contains 11 real JSON Schema
(Draft 2020-12, written in YAML) files, one per engine, mechanically
transcribed from `ENGINE_INTERFACE_CONTRACT.md` §2–§12 — every schema was
validated both for well-formedness and for actually accepting a correct
payload while rejecting an invalid enum/phantom field. This round also:
fully re-templated `playbooks/PLAYBOOK_LIBRARY.md` (1.2.0 → 1.3.0) — all 15
playbooks now use an identical 12-field structure (Objective, Applicable
Market, Scanner/Market/Portfolio Criteria, Required Data, Entry, Stop,
Exit, Risk, Invalidation, Failure Code), each Failure Code cross-referenced
to `FAILURE_TAXONOMY.md`; extended `docs/AI_AGENT_IMPLEMENTATION_GUIDE.md`
(1.0.0 → 1.1.0) with a Bad-vs-Good Agent Behaviour table and a New Agent
Checklist; added explicit exception-path diagrams to `STATE_MACHINE.md`
(1.2.0 → 1.3.0, new §2A) — correcting, not copying, the user's suggested
diagram, since a verification failure produces
`INSUFFICIENT VERIFIED INFORMATION`, never `NO TRADE`, and those two
outcomes are deliberately not interchangeable; added a
"Specification Versions Targeted" at-a-glance block to both worked
examples; and trimmed `README.md`'s Report Standard section into links
rather than restating `OUTPUT_CONTRACT.md` inline. Not done this round,
deliberately deferred as lower priority: three additional failure-path
worked examples (`DATA_FAILURE`, `RISK_REJECTION`, `RED_TEAM_REJECTION`)
the user suggested as a "P2" item.

A ninth audit round closed the gap the user identified as the system's
largest remaining hole: Master Decision could publish "Reduce MUU" with no
document ever answering how much, to what target weight, or where the
released capital goes. This round added a new pipeline state,
**State 17 — Portfolio Optimization** (`engines/PORTFOLIO_OPTIMIZATION_ENGINE.md`,
new, 1.0.0), inserted between Master Decision (16) and Execution Plan
(renumbered 17→18; Self Audit 18→19; Final Report 19→20 —
`STATE_MACHINE.md` 1.3.0→1.4.0). This engine deliberately holds neither
Constraint nor Publication Authority: its `Decision` field is a verbatim
echo of Master Decision's already-published outcome, never a re-decision —
a distinction load-bearing enough that it is stated explicitly in the new
engine's §1, in `ENGINE_INTERFACE_CONTRACT.md`'s new §12, and in
`schemas/portfolio_optimization.schema.yaml`. Its own new, non-competing
parameters (a 6% preferred single-stock target and an 8–10% advisory band,
both operating strictly inside `PORTFOLIO_ENGINE.md` §9A's existing 10%
hard cap; a new 5%/10% minimum/preferred cash reserve, previously
undefined anywhere) are indexed in `PARAMETER_REGISTRY.md`'s new §3A.
`EXECUTION_ENGINE.md` (1.2.0→1.3.0) gained a Multi-Position Trade Plan
(§3G: batch table, Execution Priority P1/P2/P3, a Maximum Slippage hard cap
distinct from the existing Slippage Assumption estimate) and now sources
existing-position share counts from Portfolio Optimization Engine rather
than computing its own. `OUTPUT_CONTRACT.md` (1.1.0→1.2.0) gained a
mandatory Portfolio Action Plan section (Immediate Action / This Week /
Monitor / No Action). `SYSTEM.md` (1.1.0→1.2.0) documents the new
non-blocking `PORTFOLIO OPTIMIZATION INCOMPLETE — DATA REQUIRED` status,
modeled on the existing `DO NOT EXECUTE — REVERIFY` pattern. This round
also added two trigger documents that deliberately do **not** occupy a
State Machine position (`STATE_MACHINE.md` new §2B):
`engines/POSITION_MANAGEMENT_ENGINE.md` (new, 1.0.0, gain/loss/earnings/IV
triggers that start a new run) and `engines/PORTFOLIO_REBALANCING_ENGINE.md`
(new, 1.0.0, periodic sector/theme drift review) — each starts a fresh
analysis run rather than extending the pipeline, and neither may publish a
position decision itself. `playbooks/PLAYBOOK_LIBRARY.md` (1.3.0→1.4.0)
gained a 13th shared field, Position Management, applied to all 15
playbooks with strategy-specific take-profit tiers and scaling rules rather
than a copy-pasted default. A new non-authoritative glossary,
`docs/PORTFOLIO_MANAGEMENT_GUIDE.md` (new, 1.0.0), defines Hold / Add /
Reduce / Exit / Rotate / Optimize / Rebalance once, each pointing at its
actual owning document, so this vocabulary does not drift the way ADD/
ROTATE/BUY previously did (v1.0's original terminology-reconciliation
round, noted above). One schema-layer subtlety worth recording here since
it is exactly the kind of drift this table exists to catch:
`schemas/execution.schema.yaml`'s `schema_version` was bumped to `"1.1.0"`
(the only schema file at other than `"1.0.0"`) because its shape gained two
required fields (`max_slippage`, `priority`) — per
`ENGINE_INTERFACE_CONTRACT.md` §1A's own rule that a field addition bumps
`schema_version` independent of `engine_version`; the new
`portfolio_optimization.schema.yaml` starts fresh at `"1.0.0"` since it has
no prior shape to compare against. Two cross-reference-only fixes
propagated from the renumbering: `RED_TEAM_ENGINE.md` (1.3.0→1.4.0) had its
own `§4A` reference to "State 17" (the old Execution Plan) updated to
State 18, and `DECISION_SNAPSHOT_POLICY.md` (1.1.0→1.2.0) had its Self
Audit reference updated from State 18 to State 19 — both are exactly the
kind of propagation gap this table's own history (see the fifth audit round
note above) exists to catch rather than let drift.

A tenth audit round addressed a second round of user feedback on the
portfolio optimization layer: **Execution was still too passive** (a bare
"Reduce MUU" with no order mechanics), **policy numbers were scattered**
across engines with some genuinely hardcoded, and **capital raised by a
Reduce/Exit had no explicit Deploy-vs-Wait decision**. This round added
`system/INVESTMENT_POLICY.md` (new, 1.0.0) as the single canonical owner of
every position/sector/cash concentration limit, preferred-target band, and
the capital-priority order — numbers previously owned independently (and,
in the capital-priority case, restated near-identically) by
`PORTFOLIO_ENGINE.md` §9A/§19 and `PORTFOLIO_OPTIMIZATION_ENGINE.md`
§7A/§7B/§11, both of which now cross-reference this one document instead.
Two genuinely new parameters (Maximum Small-Cap Exposure 25%, Maximum
Options Exposure 15%) are recorded there as having no prior owner anywhere
in the repository. A new pipeline state, **State 18 — Capital Allocation**
(`engines/CAPITAL_ALLOCATION_ENGINE.md`, new, 1.0.0), was inserted between
Portfolio Optimization (17) and Execution Plan (renumbered 18→19; Self
Audit 19→20; Final Report 20→21 — `STATE_MACHINE.md` 1.4.0→1.5.0); it
decides Deploy (into an already-approved same-run `EXECUTE` candidate,
never one it invents) or Wait for capital Portfolio Optimization Engine
released, a responsibility Portfolio Optimization Engine's own §8
previously held as an under-specified afterthought.
`EXECUTION_ENGINE.md` (1.3.0→1.4.0) gained an explicit **Execution
Package** requirement — Valid For (order time-in-force) and an If Not
Filled fallback (Re-evaluate/Cancel/Resubmit) alongside every action, so no
row is ever a bare label. `PORTFOLIO_REBALANCING_ENGINE.md` (1.0.0→1.1.0)
gained an Optimization Score reusing `PORTFOLIO_ENGINE.md`'s existing
Portfolio Health Score formula (current vs. projected allocation) rather
than inventing a new one. `DECISION_SNAPSHOT_POLICY.md` (1.2.0→1.3.0)
gained a Decision History policy section — deliberately deferring concrete
storage paths as `RESERVED — NOT APPLICABLE to v1.0`, the same treatment
already given its Prompt-version/Model-identifier fields, since TRX v1.0
has no implementation layer yet. A new `templates/PORTFOLIO_PROFILE_TEMPLATE.md`
(1.0.0) gives the human an optional, non-authoritative style/risk/
holding-period worksheet engines may read as context, never as a limit.
Finally, this round added explicit "this status is preliminary, not final"
language to `PORTFOLIO_ENGINE.md` §7/§18 (1.3.0→1.4.0) and a one-line
disambiguation between that document's own Exit Priority (1–5, pre-decision)
and `EXECUTION_ENGINE.md`/`PORTFOLIO_OPTIMIZATION_ENGINE.md`'s Execution
Priority (P1–P3, post-decision) — the two share a word but never a
timeline, and the user's suspected "engine overlap" turned out to be this
naming collision rather than a real competing authority. One more
propagation-gap fix, caught by this round's own verification pass:
`RED_TEAM_ENGINE.md` (1.4.0→1.5.0) still referenced "State 18" for the
Execution Plan's not-yet-reached check (§4A) — correct after round nine's
renumbering, stale after this round's second renumbering — now updated to
State 19.

An eleventh audit round closed a real overlap the user identified between
Portfolio Optimization Engine and Capital Allocation Engine: both could
independently compute "buy N shares of this ticker" for the same `EXECUTE`
candidate. Resolved by narrowing scope rather than adding coordination
logic — `PORTFOLIO_OPTIMIZATION_ENGINE.md` (1.1.0→1.2.0) now processes
existing positions only (`HOLD`/`REDUCE`/`EXIT`/`WATCH`) and never an
`EXECUTE` decision at all; `CAPITAL_ALLOCATION_ENGINE.md` (1.0.0→1.1.0)
took over sizing every `EXECUTE` candidate itself, bounded by the Risk
Engine's already-approved size, and gained a four-way decision
(`Deploy`/`Rotate`/`Reserve Cash`/`Wait`, replacing the prior
`Deploy`/`Wait`) so a same-run Exit-funds-Execute rotation is labelled
distinctly from a generic deploy. `OUTPUT_CONTRACT.md` (1.2.0→1.3.0) gained
its own missing Capital Allocation report section (item 14) — a gap this
round's audit found: the templates already had one, the normative contract
never did. `ENGINE_INTERFACE_CONTRACT.md` (1.6.0→1.7.0) and both affected
schemas were updated to match, each schema's `schema_version` bumped per
§1A's field-retyping rule. This round also closed a second, independently
requested compliance gap: a full-repo audit for hardcoded Investment
Policy numbers (P0 from the tenth round) found that `ANALYSIS_TEMPLATE.md`,
`DECISION_TEMPLATE.md`, `REPORT_TEMPLATE.md`, and both worked examples had
inherited "≤10%/≤30%/≤40%" as literal text rather than a cross-reference —
all four now defer to `INVESTMENT_POLICY.md` §2, and the two examples'
stale `DECISION_SNAPSHOT_POLICY.md`/`PLAYBOOK_LIBRARY.md` version citations
were corrected in the same pass. `POSITION_MANAGEMENT_ENGINE.md`
(1.0.0→1.1.0) gained a quick-reference Trigger Matrix and a new Guidance
Cut trigger (distinct from Earnings Review — content-triggered, not
event-triggered) that routes to a mandatory Committee re-evaluation in the
new run it starts. `memory/PORTFOLIO_PROFILE.md` (new) is the
actual populated instance of `templates/PORTFOLIO_PROFILE_TEMPLATE.md`
(1.0.0→1.1.0) at a fixed path engines can reference — the template alone
had no predictable location a real engine reference could point to.
This round's own verification pass caught two more propagation gaps from
the Portfolio Optimization / Capital Allocation boundary change:
`STATE_MACHINE.md` (1.5.0→1.6.0) §3's State 18 row still described the old
Deploy/Wait-only decision, and `EXECUTION_ENGINE.md` (1.4.0→1.5.0) §3G
still said a bare "`Deploy`" fed its Action/Shares fields without
mentioning `Rotate` or the Risk Engine sizing ceiling — both updated to
match the four-way decision.

**Keeping this table honest**: any change to a document's `Version` header
SHALL update its entry here in the same edit — this table is exactly as
prone to drifting out of sync as `PARAMETER_REGISTRY.md` is, and is subject
to the same rule: if this matrix and a document's own header ever disagree,
**the document's own header wins**, and the mismatch is a bug in this table.

## Consistency Checklist

Any material document change must confirm:

- all referenced files exist and reference the correct responsibility;
- State Machine ordering is not redefined elsewhere;
- the authority hierarchy and vetoes remain intact;
- output fields have an evidence and freshness policy;
- no duplicate engine becomes a competing final-decision authority;
- no new dependency creates a cycle or points to a later State Machine state
  — see `docs/DEPENDENCY_MAP.md` §1 and its checklist;
- any use of the word "authority" names Constraint or Publication Authority
  per `CONSTITUTION.md` §4A — never an unqualified "final authority";
- a change to `STATE_MACHINE.md` §6A's revision-cycle limit, or to any
  terminal status, stays consistent across `OUTPUT_CONTRACT.md`,
  `FAILURE_TAXONOMY.md`, and `docs/RESPONSIBILITY_MATRIX.md`;
- the changed document's `Version` header is bumped and the Release
  Version Matrix above is updated in the same edit; and
- README architecture matches the repository structure.

## Extension Rules

New instruments, option strategies, data integrations, scoring models, or
automation proposals require a documented scope, source policy, risk controls,
failure modes, output requirements, and constitutional review. They must not
imply autonomous execution without a separately approved, explicitly scoped
system change.

---

TRX Trading OS v1.0
