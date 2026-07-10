# TRX Trading OS
## STATE_MACHINE.md

```text
Document ID      : TRX-SM-001
Document Name    : State Machine
Version          : 1.0.0
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
  → MASTER DECISION → EXECUTION PLAN (if actionable) → SELF AUDIT
  → FINAL REPORT → END
```

The workflow never permits opportunity scanning before portfolio review.
`OPTIONS REVIEW` is conditional and is reported as `Not Applicable` when no
option structure is evaluated.

---

## 3. States

| State | Owner | Required result | Failure / transition |
|---|---|---|---|
| 01 WAIT | System | Await request | Receive user request |
| 02 RECEIVE INPUT | System | Raw portfolio, objective, watchlist, constraints, questions | Proceed to validation |
| 03 VALIDATE INPUT | System | Readable tickers, positive quantities, identifiable currency, numeric cash / buying power | Request clarification; stop downstream work |
| 04 VERIFY DATA | Verification and Data Source policies | Evidence ledger, timestamps, V1–V5 status, critical-data gate | Critical V5: finalise `INSUFFICIENT VERIFIED INFORMATION` |
| 05 MARKET & MACRO ANALYSIS | Market Engine | Regime, trend, stage, breadth, volatility, liquidity, sector rotation, macro risk | Reverify missing material market data |
| 06 PORTFOLIO REVIEW | Portfolio Engine | Health, exposure, concentration, cash, buying capacity, position context | Request portfolio clarification before new-risk analysis |
| 07 PRELIMINARY RISK GATE | Risk Engine | Existing exposure, risk budget, drawdown, event and correlation constraints | Restrict new risk or return `NO TRADE` |
| 08 POSITION REVIEW | Portfolio + Risk Engines | HOLD / REDUCE / EXIT / WATCH status for each existing position | Resolve missing position data |
| 09 OPPORTUNITY SCAN | Scanner Engine | Eligible candidate universe and exclusion reasons | `NO ELIGIBLE CANDIDATE` proceeds to final `NO TRADE` |
| 10 PLAYBOOK ASSIGNMENT | Playbook Engine | Exactly one validated playbook or reject / watchlist | No match: remove candidate from actionable ranking |
| 11 OPTIONS REVIEW | Options Engine | Stock-versus-Long-Call comparison when applicable | Option failure removes option structure; underlying may continue |
| 12 RANKING | Decision Engine | Transparent candidate comparison and opportunity scores | Insufficient evidence: remove candidate |
| 13 COMMITTEE REVIEW | Committee Engine | Independent votes, consensus, dissent, and concerns | No consensus: `WATCH`; risk objection routes to Risk Gate |
| 14 RED TEAM REVIEW | Red Team Engine | Counter-thesis, alternatives, resilience, and critical-risk finding | Critical flaw: downgrade or veto |
| 15 FINAL RISK GATE | Risk Engine | Final sizing, maximum loss, portfolio heat, stop and event-risk review | Binding `NO TRADE`, `REDUCE`, or `EXIT` |
| 16 MASTER DECISION | Master Decision Engine | One final outcome integrating valid outputs | Must honour prior vetoes and data failures |
| 17 EXECUTION PLAN | Execution Engine | Human-reviewable plan for EXECUTE / REDUCE / EXIT | Stale execution data: `DO NOT EXECUTE — REVERIFY` |
| 18 SELF AUDIT | Master Decision Engine | Contract, consistency, evidence, and veto checklist | Return to earliest defective state |
| 19 FINAL REPORT | Master Decision Engine | Output Contract-compliant report | End |

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

End of Document

TRX Trading OS v1.0
