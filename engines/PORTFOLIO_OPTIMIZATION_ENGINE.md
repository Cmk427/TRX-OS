# TRX Trading OS
## PORTFOLIO_OPTIMIZATION_ENGINE.md

```text
Document ID      : TRX-PFO-001
Document Name    : Portfolio Optimization Engine
Owner            : TRX project owner
Last Updated     : 2026-07-14
Version          : 1.2.0
Status           : Active
Classification   : Critical
Dependencies     : STATE_MACHINE.md
                   PORTFOLIO_ENGINE.md
                   RISK_ENGINE.md
                   COMMITTEE_ENGINE.md
                   MASTER_DECISION_ENGINE.md
                   INVESTMENT_POLICY.md
                   CAPITAL_ALLOCATION_ENGINE.md
Applies To       : State 17 — Post-Master-Decision Portfolio Allocation
```

---

## 1. Purpose and Boundary

Master Decision Engine (State 16) can publish that a position should be
`REDUCE`d — but "Reduce MUU" alone does not say how much, to what target
weight, or freeing how much cash. This engine closes that gap for
**existing positions**: it converts an already-published Master Decision
outcome of `HOLD`, `REDUCE`, `EXIT`, or `WATCH` into concrete portfolio
allocation math — target weight, share count, and capital released.

**This engine never processes an `EXECUTE` decision.** A new position's
sizing (how many shares, how much capital) is decided entirely by
`CAPITAL_ALLOCATION_ENGINE.md` (State 18), bounded by the Risk Engine's
already-approved size for that candidate. This split exists because both
engines could otherwise independently compute "buy N shares of ORCL" for
the same candidate — a genuine overlap the user identified. Drawing the
line at "existing position" (this engine) versus "new position / released
capital" (Capital Allocation Engine) means exactly one engine ever answers
"how many shares of this ticker," never two.

This engine's primary question is **"given what Master Decision already
decided, what does the portfolio look like afterward?"**, not "what should
happen to this position?" — that question was already answered upstream, with
full Committee and Red Team review, at State 16. An engine that re-opens
Hold/Reduce/Exit/Execute after Master Decision has published would compete
with Master Decision's Publication Authority (`CONSTITUTION.md` §4A); this
engine SHALL NOT do that.

Consequently:

- This engine holds **neither Constraint Authority nor Publication
  Authority** (`CONSTITUTION.md` §4A) — the same "Neither" classification as
  Market, Portfolio, Scanner, Playbook, Options, Decision, and Committee
  Engines (`docs/RESPONSIBILITY_MATRIX.md` §3).
- It does not generate investment ideas, does not re-evaluate a thesis, and
  does not perform Committee or Red Team review of its own.
- Its `Decision` field per ticker (§6) is a **verbatim echo** of Master
  Decision Engine's State-16 `final_outcome` for that ticker — never a
  separately computed value. If this engine and Master Decision's record
  ever disagree on a `Decision` value, that is a defect in this engine's
  output, not a second valid answer.
- It cannot override a Risk Engine veto, a Red Team critical-risk veto, or
  any binding gate result. It operates entirely downstream of State 16,
  after every constraint has already been applied.
- This engine's `Priority` field (§6: `P1`/`P2`/`P3`) ranks **execution
  ordering of already-approved actions**, computed post-decision. It is a
  different concept from `PORTFOLIO_ENGINE.md` §15's Exit Priority (a 1–5
  urgency ranking of existing positions computed pre-decision, at State
  06/08) — the two share the word "priority" but never the same input or
  timing, and SHALL NOT be conflated.

---

## 2. Workflow Position

```text
Final Risk Gate (15) → Master Decision (16)
  → Portfolio Optimization (17)   ← this document
  → Execution Plan (18)
  → Self Audit (19) → Final Report (20) → END
```

Per `STATE_MACHINE.md` §2B, ongoing position monitoring between runs is
out-of-band and is not a further pipeline state after Execution — see
`POSITION_MANAGEMENT_ENGINE.md`.

---

## 3. Required Inputs

- **Master Decision Engine (16)** — the published `final_outcome` per
  existing position for this run (`ENGINE_INTERFACE_CONTRACT.md` §11):
  `HOLD`, `REDUCE`, `EXIT`, or `WATCH`. A ticker whose outcome is `EXECUTE`
  or `NO TRADE` is out of this engine's scope entirely — `EXECUTE` routes
  directly to `CAPITAL_ALLOCATION_ENGINE.md` (18).
- **Portfolio Engine (06/08)** — current holdings, per-position current
  weight, cash balance, buying power, and the §9A concentration state for
  every position (already computed before Master Decision integrated it).
- **Risk Engine (07/15)** — current Portfolio Heat, for context in the
  Reason field only; this engine does not perform Risk Engine's sizing
  math (that applies to `EXECUTE` candidates, out of scope here).
- **Committee Engine (13)** — context only (concerns, dissent) for the
  Reason field in §6; this engine does not re-weigh Committee votes.

A required input that is `UNKNOWN` or missing is handled per §13 Failure
Conditions — it never causes this engine to guess a weight or share count.

---

## 4. Responsibilities

Given the inputs above, this engine SHALL answer, per ticker/candidate:

1. What is the current portfolio weight?
2. What is the target portfolio weight consistent with Master Decision's
   published outcome and `PORTFOLIO_ENGINE.md`'s construction limits (§9A)?
3. What share count moves the position from current to target weight?
4. How much capital does this release (if reducing/exiting)?
5. What is the resulting portfolio shape after every action in this run is
   applied (§10)?

Questions 4's capital, once released, is handed off entirely — see §8. This
engine never decides where released capital goes, and never sizes a new
position.

This engine SHALL NOT ask or answer "should this position be reduced
instead of held" — that question belongs to State 16 and earlier.

---

## 5. Required Position Analysis

For every position or candidate carried into this state, the engine SHALL
state:

- Current Weight (percent of portfolio value, from Portfolio Engine)
- Target Weight (this engine's output, §6/§7)
- Decision (echoed from Master Decision, §1)
- Carried-forward thesis/catalyst summary (restated, not recomputed)
- Liquidity (from Portfolio Engine §12, for share-count feasibility)

---

## 6. Required Output

Every existing position carried into this state SHALL produce one row —
`EXECUTE` tickers never appear in this table at all (see
`CAPITAL_ALLOCATION_ENGINE.md` instead):

| Field | Content |
|---|---|
| Ticker | string |
| Decision | `HOLD \| REDUCE \| EXIT \| WATCH` — echoed from Master Decision (16); never computed here |
| Current Weight | percent of portfolio value |
| Target Weight | percent of portfolio value |
| Suggested Shares | signed share count, always ≤ 0 (a sell or no change) — this engine never produces a positive (buy) share count; `0` for `HOLD`/`WATCH` unless §7's advisory applies |
| Capital Released | currency amount freed by a reduction/exit; `null` for `HOLD`/`WATCH` with no advisory |
| Reason | one line, tracing to the Master Decision rationale and any construction-limit rule applied |
| Priority | `P1 \| P2 \| P3` per `EXECUTION_ENGINE.md` §3G |
| Execution Required | boolean — `false` for `HOLD`/`WATCH` with no advisory triggered |

Example:

```text
Ticker              : MUU
Decision            : REDUCE            (echoed from Master Decision, State 16)
Current Weight      : 20.8%
Target Weight       : 12.0%
Suggested Shares     : -5
Capital Released    : USD 4,820
Reason              : Master Decision REDUCE; target weight set to this
                       engine's preferred single-stock band (§7) given
                       available liquidity.
Priority             : P2
Execution Required  : true
```

---

## 7. Optimization Rules

### 7A. Concentration — Hard Cap vs. Preferred Target

`INVESTMENT_POLICY.md` §2's default maximum concentration percentages
(Single Stock, Sector, Theme, Small-Cap, Options — each modified by Account
Risk Profile per that document's §8) are the **binding hard ceiling**. This
engine never redefines, raises, or relaxes them — that would recreate
exactly the competing-authority problem `PARAMETER_REGISTRY.md` §1 and
`docs/DEPENDENCY_MAP.md` §3A exist to prevent. Because Portfolio Review
(State 06/08) runs before Master Decision in the same analysis run, any
hard-cap breach is already reflected in that run's Master-Decision-published
`Decision` value before this engine ever sees it — this engine does not
encounter a breach whose `Decision` does not already account for it.

The **Preferred Target Band** this engine uses to compute a target weight
once a `Decision` of `REDUCE` or `EXIT` already requires one is defined in
`INVESTMENT_POLICY.md` §3 (preferred single-stock target and advisory
review band) — not restated here.

For a ticker whose Master-Decision `Decision` is `HOLD` or `WATCH`, this
engine SHALL NOT force a target weight change. If that ticker's current
weight sits inside `INVESTMENT_POLICY.md` §3's advisory review band, this
engine MAY add a single labelled **Optional Risk-Reduction Note** (not a
`Suggested Shares` value, not an `Execution Required: true`) — clearly
marked non-binding, since converting it into a forced action would be a
re-decision this engine is not authorized to make.

### 7B. Cash Reserve

The minimum and preferred cash-reserve percentages are defined in
`INVESTMENT_POLICY.md` §4 — not restated here. This engine's own §6 actions
(Reduce/Exit on existing positions) only ever release capital, never
deploy it, so they cannot themselves breach the cash minimum. Whether
released capital is then held toward that minimum (`Reserve Cash`) or
deployed is `CAPITAL_ALLOCATION_ENGINE.md`'s decision, per §11's
capital-priority order.

---

## 8. Capital Allocation Handoff

This engine states **how much** capital a `REDUCE`/`EXIT` releases
(`Capital Released`, §6). Everything downstream of that number belongs
entirely to `CAPITAL_ALLOCATION_ENGINE.md` (State 18):

- **Where it goes** — Deploy into an already-approved same-run `EXECUTE`
  candidate, Rotate (a Deploy specifically paired with this engine's own
  releasing ticker), Reserve Cash (toward `INVESTMENT_POLICY.md` §4's
  minimum/preferred reserve), or Wait (categorized per
  `PORTFOLIO_ENGINE.md` §16's remaining categories).
- **How much of a new position to buy** — Capital Allocation Engine sizes
  every `EXECUTE` candidate itself, bounded by the Risk Engine's approved
  size; this engine never computes a buy-side share count for any ticker.

This split keeps "how much did an existing position release" (this
engine's job) and "where does capital go, including sizing a new position"
(Capital Allocation Engine's job) as two single-owner questions rather than
one engine's overloaded responsibility.

A "rotation" (release from one ticker, deploy into another) is always
expressed as the paired `EXIT`/`REDUCE` (source, this engine) and `EXECUTE`
(destination, sized by Capital Allocation Engine) `Decision` values already
published by Master Decision, per `MASTER_DECISION_ENGINE.md` §9 — never as
a single atomic action either engine invents.

---

## 9. No-Rebalancing-Action Rule

If, after applying every rule above, no position's target weight differs
from its current weight and no capital reallocation is required, this
engine SHALL report **`NO REBALANCING ACTION`** for that ticker. This is
deliberately not `NO TRADE` — that is a `SYSTEM.md` §6 primary outcome this
engine has no authority to publish. `NO REBALANCING ACTION` is a statement
about this engine's own output only, alongside whatever primary outcome
Master Decision already published.

---

## 10. Portfolio Optimization Report

Every run SHALL produce this fixed structure:

```text
Current Allocation
  ↓
Target Allocation
  ↓
Required Actions (§6 table)
  ↓
Execution Priority (P1/P2/P3, per EXECUTION_ENGINE.md §3G)
  ↓
Expected Portfolio After Optimization
```

Example:

```text
Ticker   Current   Target
MUU      21%       15%
TSLA     18%       18%
NOW      13%       15%
Cash     4%         8%

Required Actions:
  Sell 4 MUU  → raise USD 3,900 → increase Cash
```

`Expected Portfolio After Optimization` is a computed projection for human
review, not a claim that the trades have occurred — actual fills are
recorded only after `EXECUTION_ENGINE.md` §5's Completion Record.

---

## 11. Constraints and Priority

This engine's priority order is `INVESTMENT_POLICY.md` §6's canonical
capital-priority order — not restated here, and not a competing list.
`PORTFOLIO_ENGINE.md` §19 follows the identical cross-reference.

This engine SHALL never optimize solely for expected return. Capital
preservation, then risk, then diversification, outrank return whenever they
conflict.

---

## 12. Optimization Objectives

The objective is **not** to maximize return. The objective is to maximize
long-term risk-adjusted portfolio quality, in this priority order:

1. Preserve Capital
2. Reduce Unnecessary Risk
3. Improve Portfolio Quality
4. Improve Capital Efficiency
5. Maintain Strategic Exposure
6. Increase Expected Return

---

## 13. Decision Validation

Before finalizing §6's output for any `REDUCE` or `EXIT` row, this engine
SHALL answer four questions about the **sizing/optimization choice** — not
about the Hold/Reduce/Exit decision itself, which was already made and
reviewed at State 16 and is not reopened here:

1. Why is this target weight/share count better than making no allocation
   change at all?
2. Why is this specific size better than a larger or smaller adjustment?
3. Is the released capital amount (§6) correctly stated for Capital
   Allocation Engine to act on — not whether it should be deployed or held,
   which is that engine's decision (§8), not this one?
4. What could make this sizing wrong (e.g., a liquidity constraint, a
   subsequent price move, a stale weight input)?

A row that cannot answer all four SHALL still be published, but with an
explicit note identifying which question could not be answered and why —
never silently omitted.

---

## 14. Engine Interface

- **Input**: Portfolio Engine, Risk Engine, Committee Engine (context only),
  Master Decision Engine.
- **Output**: Execution Engine (§6 table feeds `EXECUTION_ENGINE.md` §2 and
  §3G), and the Output Contract's Portfolio Action Plan
  (`OUTPUT_CONTRACT.md` §13).
- Structured shape: `ENGINE_INTERFACE_CONTRACT.md` §12; machine schema:
  `schemas/portfolio_optimization.schema.yaml`.

---

## 15. Failure Conditions

If portfolio weight, cash, or buying-power data is incomplete or
contradictory, this engine SHALL NOT estimate a target weight or share
count. It SHALL instead report status **`PORTFOLIO OPTIMIZATION
INCOMPLETE — DATA REQUIRED`**, attached only to `OUTPUT_CONTRACT.md` §13's
Portfolio Action Plan section — modeled on the existing
`DO NOT EXECUTE — REVERIFY` pattern (`SYSTEM.md` §6, `EXECUTION_ENGINE.md`
§1A/§3D): it never changes the primary outcome Master Decision already
published at State 16. If market data has gone stale since State 16, this
engine SHALL request re-verification at State 04 rather than computing
against a stale price.

---

## 16. Success Criteria

A successful Portfolio Optimization pass SHALL:

- Translate every Master-Decision outcome into a concrete, execution-ready
  number without altering that outcome;
- Respect `INVESTMENT_POLICY.md` §2's hard concentration limits and §4's
  cash-reserve minimum;
- Never leave a `REDUCE`/`EXIT` `Decision` without a Suggested Shares and
  Capital Released value, and never produce a row for an `EXECUTE` ticker
  at all; and
- Produce a Portfolio Optimization Report (§10) legible as a single
  before/after view of the whole portfolio.

---

End of Document

TRX Trading OS v1.0
