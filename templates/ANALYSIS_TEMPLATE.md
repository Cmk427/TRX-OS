# TRX Trading OS
## ANALYSIS_TEMPLATE.md

```text
Document ID      : TRX-TPL-001
Document Name    : Analysis Template
Version          : 1.0.0
Status           : Active
Classification   : Template
Dependencies     : STATE_MACHINE.md
                   VERIFICATION_POLICY.md
                   DATA_SOURCE_POLICY.md
                   MARKET_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   RISK_ENGINE.md
                   SCANNER_ENGINE.md
                   PLAYBOOK_ENGINE.md
                   OPTIONS_ENGINE.md
Applies To       : States 02–11 (Input through Options Review)
```

---

## 1. Purpose

This is the working worksheet for the research half of the pipeline — from
raw input through Options Review (`STATE_MACHINE.md` States 02–11). Fill one
copy per analysis run before candidates move to `DECISION_TEMPLATE.md` for
ranking and committee review. Every field must be either a stated value, a
labelled assumption, or `UNKNOWN`. Never leave a material field blank.

Copy this file per run; do not edit the template in place.

---

## 2. Run Header

```text
Run ID           :
Requested by     :
Timestamp        :               (state exact timezone)
Market Session   :               (Pre-Market / Regular / After-Hours / Closed)
Objective        :               (e.g. new-candidate scan, existing-position review, options comparison)
Constraints      :               (capital limit, sector exclusions, horizon, risk preference)
```

---

## 3. State 03 — Input Validation

| Field | Value | Status |
|---|---|---|
| Tickers / watchlist | | Valid / Needs clarification |
| Current positions (qty, cost basis) | | Valid / Needs clarification |
| Cash balance | | Valid / Needs clarification |
| Buying power / margin status | | Valid / Needs clarification |
| Open orders | | Valid / Needs clarification |

If any row is "Needs clarification," stop here and request the missing input
per `STATE_MACHINE.md` State 03 — do not proceed to Verification.

---

## 4. State 04 — Verification Ledger

For every material fact used later in this analysis:

| Fact | Value | Source (tier S1–S5) | Timestamp | V-Label (V1–V5) | Note |
|---|---|---|---|---|---|
| | | | | | |

If any critical item (current price, market session, option chain, portfolio
data — see `DATA_SOURCE_POLICY.md` §3) cannot reach at least V2, stop this
analysis and report `INSUFFICIENT VERIFIED INFORMATION` for the affected
scope only. Unaffected scope may continue.

---

## 5. State 05 — Market & Macro (Market Engine)

```text
Trend / Stage            :
Breadth                  :
Volatility (e.g. VIX)    :
Macro events (next 5–10d):
Sector rotation          :
Market Score (0–100)     :               → Regime: Exceptional / Strong / Healthy / Weak / Defensive
Recommended exposure     :               (per Market Score risk multiplier)
Confidence               :
```

---

## 6. State 06 — Portfolio Review (Portfolio Engine)

```text
Portfolio Health Score (0–100) :          → Excellent / Healthy / Acceptable / ...
Cash %                         :
Buying power                   :
Largest position (ticker, %)   :
Sector / theme concentration   :
Correlation risk                :
Constraints on new exposure    :
```

---

## 7. State 07 — Preliminary Risk Gate (Risk Engine)

```text
Existing portfolio heat        :
Risk budget remaining          :
Drawdown status                :
Event-risk window              :
Gate result                    :          Pass / Restrict new risk / NO TRADE
Reason                         :
```

A `NO TRADE` or restriction here is binding for the rest of this run per
`CONSTITUTION.md` §5. Record it and continue only to State 08 (existing
positions) if any positions exist.

---

## 8. State 08 — Existing Position Review

Repeat per open position:

```text
Ticker           :
Status           :          Healthy / At Risk / Invalidated
Health / trend    :
Risk             :
Action           :          HOLD / REDUCE / EXIT / WATCH
Reason           :
```

---

## 9. State 09 — Opportunity Scan (Scanner Engine)

| Ticker | Setup Quality | Portfolio Fit | Candidate Quality Score | Catalyst state | Exclusion reason (if any) |
|---|---|---|---|---|---|
| | | | | | |

Candidate Quality Score bands: 90+ Elite; 80–89 Qualified; 70–79 Watchlist;
below 70 Exclude. If zero candidates survive, record `NO ELIGIBLE CANDIDATE`
and proceed toward a final `NO TRADE`.

---

## 10. State 10 — Playbook Assignment (Playbook Engine)

Repeat per surviving candidate (max 3 carried forward):

```text
Ticker                :
Playbook (ID / name)  :
Playbook Match Score  :          90+ Excellent / 80+ Qualified / 70+ Weak / <70 Reject
Rationale             :
No-trade conditions checked :
```

No match at 85+ (execution eligibility threshold) removes the candidate from
actionable ranking; it may still be logged as `WATCH`.

---

## 11. State 11 — Options Review (Options Engine, conditional)

`Not Applicable` unless a Long Call structure is being compared to the
underlying.

```text
Underlying candidate score gate (≥80 required) :
Contract (strike / expiration)                 :
Verified pricing / IV / Greeks                 :
Liquidity (OI, spread)                          :
Option Quality Score (0–100)                    :
Bull thesis / Bear thesis                       :
Maximum risk                                    :
```

---

## 12. Handoff to Decision Template

Carry forward to `DECISION_TEMPLATE.md`: the Market Score and regime,
Portfolio Health Score and constraints, the Preliminary Risk Gate result, each
surviving candidate's Candidate Quality Score / Playbook / Option Quality
Score, and the full verification ledger from §4. Do not re-derive facts
already labelled here — reference them by row.

---

End of Document

TRX Trading OS v1.0
