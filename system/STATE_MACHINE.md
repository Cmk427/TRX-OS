# TRX Trading OS
## STATE_MACHINE.md

```text
Document ID      : TRX-SM-001
Document Name    : State Machine
Version          : 1.4.0
Status           : Active
Classification   : Critical
Dependencies     : CORE_PRINCIPLES.md
                   VERIFICATION_POLICY.md
                   DATA_SOURCE_POLICY.md
                   CONSTITUTION.md
Applies To       : Entire System
```

---

## 1. Purpose

This is the sole authoritative workflow for TRX Trading OS. Engine documents
define responsibilities inside a state; they SHALL NOT redefine the state
order, skip a gate, or publish a final outcome. If a state fails, return only
to the earliest state that can correct the failure.

---

## 2. Global Flow

```text
WAIT → RECEIVE INPUT → VALIDATE INPUT → VERIFY DATA
  → MARKET & MACRO ANALYSIS → PORTFOLIO REVIEW
  → PRELIMINARY RISK GATE → POSITION REVIEW
  → OPPORTUNITY SCAN → PLAYBOOK ASSIGNMENT → OPTIONS REVIEW (if applicable)
  → RANKING → COMMITTEE REVIEW → RED TEAM REVIEW → FINAL RISK GATE
  → MASTER DECISION → PORTFOLIO OPTIMIZATION
  → EXECUTION PLAN (if actionable) → SELF AUDIT
  → FINAL REPORT → END
```

The workflow never permits opportunity scanning before portfolio review.
`OPTIONS REVIEW` is conditional and is reported as `Not Applicable` when no
option structure is evaluated.

---

## 2A. Exception Paths

§2's diagram shows the single normal flow. It deliberately does not show
where a run exits early — this section is that diagram, drawn from §4
Outcome Semantics and §6/§6A below, not a new rule. Each path terminates at
`END` unless stated otherwise:

```text
Verification failure (State 04, critical data)
  → INSUFFICIENT VERIFIED INFORMATION → END
  (never NO TRADE — a verification failure means the system could not
  form an opinion at all; NO TRADE means it formed one and the opinion is
  "don't trade," a materially different, still-completed outcome)

No eligible candidate (State 09)
  → NO TRADE → END

Preliminary or Final Risk Gate rejection (State 07 or 15)
  → binding NO TRADE / REDUCE / EXIT → END
  (Master Decision records this at State 16 but cannot override it)

Red Team critical-risk finding (State 14)
  → NO TRADE / WATCH / REDUCE / EXIT → END
  (per §18 Decision Downgrade in RED_TEAM_ENGINE.md)

Committee no consensus (State 13)
  → WATCH → END
  (unless a stricter gate elsewhere in the run already requires more)

Revision-cycle limit exceeded (§6A, 3rd full-cycle revision)
  → SYSTEM REVIEW REQUIRED → END

Portfolio Optimization cannot compute (State 17, incomplete weight/cash data)
  → PORTFOLIO OPTIMIZATION INCOMPLETE — DATA REQUIRED
  (attaches only to the Portfolio Action Plan report section; does not
  change the primary outcome Master Decision already published at State 16
  — see `PORTFOLIO_OPTIMIZATION_ENGINE.md` §15 and §4 Outcome Semantics
  below)

Execution-time stale/invalid data (State 18, after Portfolio Optimization)
  → DO NOT EXECUTE — REVERIFY → return to State 04
  (the only exception path that re-enters the flow instead of ending it —
  every other path above is terminal for that run)
```

Every path above is a **completed, valid, auditable outcome** per §4 — none
of them is a workflow failure, and none skips `FAILURE_TAXONOMY.md`
classification except `NO TRADE` reached via "no eligible candidate" or
"Committee no consensus" alone, which are ordinary negative results rather
than one of that taxonomy's six failure categories (see
`playbooks/PLAYBOOK_LIBRARY.md` §2 field 12 for the same
failure-vs-ordinary-non-match distinction at the playbook level).

---

## 2B. Post-Execution Monitoring

A completed run still ends at `END` after State 20 (Final Report) — this
section adds no new pipeline state. Ongoing monitoring of positions between
runs (a large unrealized gain, a scheduled earnings date, an IV spike, a
loss threshold) is deliberately out-of-band: it is defined in
`engines/POSITION_MANAGEMENT_ENGINE.md`, whose fired triggers **start a new
analysis run at State 01/02** with the trigger condition and affected
ticker(s) supplied as input. This is distinct from a §6 Recovery Rules
transition, which corrects a defect *within* an already-in-progress run —
Position Management Engine's triggers never resume a completed run, they
only cause a fresh one to begin. The same applies to
`engines/PORTFOLIO_REBALANCING_ENGINE.md`'s periodic (e.g. monthly)
allocation review. Neither engine holds Constraint or Publication Authority
(`CONSTITUTION.md` §4A); neither may skip Verification, Portfolio Review, or
any other state of the new run it starts.

---

## 3. States

| State | Owner | Required input | Required result | Failure / transition |
|---|---|---|---|---|
| 01 WAIT | System | None (idle) | Await request | Receive user request |
| 02 RECEIVE INPUT | System | Raw user request | Raw portfolio, objective, watchlist, constraints, questions | Proceed to validation |
| 03 VALIDATE INPUT | System | Raw input from State 02 | Readable tickers, positive quantities, identifiable currency, numeric cash / buying power | Request clarification; stop downstream work |
| 04 VERIFY DATA | Verification and Data Source policies | Validated input (03) plus external/user-provided data sources | Evidence ledger, timestamps, V1–V5 status, critical-data gate | Critical V5: finalise `INSUFFICIENT VERIFIED INFORMATION` |
| 05 MARKET & MACRO ANALYSIS | Market Engine | Verified market/macro data (04) | Regime, trend, stage, breadth, volatility, liquidity, sector rotation, macro risk | Reverify missing material market data |
| 06 PORTFOLIO REVIEW | Portfolio Engine | Validated portfolio/cash/buying power (03) plus verified data (04) | Health, exposure, concentration, cash, buying capacity, position context | Request portfolio clarification before new-risk analysis |
| 07 PRELIMINARY RISK GATE | Risk Engine | Market regime (05), Portfolio health/exposure (06) | Existing exposure, risk budget, drawdown, event and correlation constraints | Restrict new risk or return `NO TRADE` |
| 08 POSITION REVIEW | Portfolio + Risk Engines | Portfolio positions (06), Preliminary Risk Gate result (07) | HOLD / REDUCE / EXIT / WATCH status for each existing position | Resolve missing position data |
| 09 OPPORTUNITY SCAN | Scanner Engine | Market regime (05), Portfolio constraints (06), Preliminary Risk Gate result (07) | Eligible candidate universe and exclusion reasons | `NO ELIGIBLE CANDIDATE` proceeds to final `NO TRADE` |
| 10 PLAYBOOK ASSIGNMENT | Playbook Engine | Candidate universe (09) | Exactly one validated playbook or reject / watchlist | No match: remove candidate from actionable ranking |
| 11 OPTIONS REVIEW | Options Engine | Playbook-assigned candidate (10) with Candidate Quality Score ≥ 80 (Scanner's score, State 09 — Decision Engine's Opportunity Score does not exist until State 12 and cannot gate an earlier state) | Stock-versus-Long-Call comparison when applicable | Option failure removes option structure; underlying may continue |
| 12 RANKING | Decision Engine | Playbook assignment (10), option comparison (11, if applicable) | Transparent candidate comparison and opportunity scores | Insufficient evidence: remove candidate |
| 13 COMMITTEE REVIEW | Committee Engine | Ranked candidates and Opportunity Scores (12) | Independent votes, consensus, dissent, and concerns | No consensus: `WATCH`; risk objection routes to Risk Gate |
| 14 RED TEAM REVIEW | Red Team Engine | Committee votes and consensus (13) | Counter-thesis, alternatives, resilience, and critical-risk finding | Critical flaw: downgrade or veto |
| 15 FINAL RISK GATE | Risk Engine | Red Team result (14), Preliminary Risk Gate context (07) | Final sizing, maximum loss, portfolio heat, stop and event-risk review | Binding `NO TRADE`, `REDUCE`, or `EXIT` |
| 16 MASTER DECISION | Master Decision Engine | All engine outputs from States 05–15 | One final outcome integrating valid outputs | Must honour prior vetoes and data failures |
| 17 PORTFOLIO OPTIMIZATION | Portfolio Optimization Engine | Master Decision outcome (16), Portfolio Engine holdings/weights (06/08), Risk Engine sizing (07/15) | Per-position target weight, share count, capital released/required, and reallocation, echoing (never altering) each Decision from State 16 | Incomplete weight/cash data: `PORTFOLIO OPTIMIZATION INCOMPLETE — DATA REQUIRED`, non-blocking to the State 16 outcome |
| 18 EXECUTION PLAN | Execution Engine | Master Decision outcome (16), Portfolio Optimization output (17), current verified pricing | Human-reviewable plan for EXECUTE / REDUCE / EXIT | Stale execution data: `DO NOT EXECUTE — REVERIFY` |
| 19 SELF AUDIT | Master Decision Engine | Full Decision Snapshot / all state outputs through State 18 | Contract, consistency, evidence, and veto checklist | Return to earliest defective state |
| 20 FINAL REPORT | Master Decision Engine | Self Audit result (19) | Output Contract-compliant report | End |

---

## 4. Outcome Semantics

| Condition | Required result |
|---|---|
| Critical price, market-session, option, or required portfolio data cannot be verified | `INSUFFICIENT VERIFIED INFORMATION` |
| Market, portfolio, or Risk Gate does not permit new exposure | `NO TRADE` or existing-position action |
| No candidate meets criteria | `NO TRADE` |
| Committee lacks consensus | `WATCH` unless another gate requires a stricter result |
| Red Team identifies critical risk | `NO TRADE`, `WATCH`, `REDUCE`, or `EXIT` |
| Risk Engine rejects | Binding `NO TRADE`, `REDUCE`, or `EXIT` |
| Revision-cycle limit exceeded (§6A) | `SYSTEM REVIEW REQUIRED` |

`NO TRADE` is a completed, valid report. It is not a workflow failure. Only a
critical verification or input failure produces
`INSUFFICIENT VERIFIED INFORMATION`.

---

## 5. State Invariants

- Facts, analysis, inferences, assumptions, and recommendations remain
  separately labelled at every state.
- A downstream state may add constraints but may not relax an upstream veto.
- All decisions retain timestamps, verification state, inputs, and version IDs.
- The Master Decision Engine is the only final-report publisher; the human is
  the only execution authority.
- A changed market price, market session, material event, portfolio position,
  or option quote triggers re-entry at State 04 before execution.

---

## 6. Recovery Rules

When a defect is found, return to State 03 for malformed user input, State 04
for stale or contradictory data, State 05 for market/macro changes, State 06
for portfolio changes, State 07 or 15 for risk changes, and the relevant
candidate state for setup-specific evidence. Never continue with a cached
result that has become materially stale.

---

## 6A. Revision Cycle Limit

Recovery transitions make correction possible, but without a bound they also
make an unterminated loop possible — for example Master Decision (16) →
Self Audit (19) finds a defect → returns to Final Risk Gate (15) → Risk
requires a change → Master Decision (16) re-integrates → Self Audit (19)
finds another defect → and so on indefinitely.

Each analysis run SHALL maintain one global revision counter for the entire
run (not one counter per state or per state-pair). Any transition that
returns to an earlier state per §6 increments this counter by one.

- **Revisions 1 and 2**: proceed normally; re-run the affected states and
  continue toward Self Audit and Final Report as usual.
- **On the 3rd such transition in a single run**: the run SHALL terminate
  immediately with the outcome `SYSTEM REVIEW REQUIRED` instead of
  attempting a further revision. This is a valid, auditable terminal
  outcome (see `system/FAILURE_TAXONOMY.md`, category `CONFLICT_FAILURE`)
  — not a silent hang and not a forced `NO TRADE` dressed up as something
  else. The report SHALL name which states were repeatedly in conflict and
  what each revision attempted to fix, so a human can resolve the
  disagreement the loop could not.

The counter resets only at the start of a new analysis run (State 01/02); it
is never reset mid-run to avoid the limit.

---

End of Document

TRX Trading OS v1.0
