# TRX Trading OS
## DECISION_TEMPLATE.md

```text
Document ID      : TRX-TPL-002
Document Name    : Decision Template
Version          : 1.4.0
Status           : Active
Classification   : Template
Dependencies     : STATE_MACHINE.md
                   DECISION_ENGINE.md
                   COMMITTEE_ENGINE.md
                   RED_TEAM_ENGINE.md
                   RISK_ENGINE.md
                   MASTER_DECISION_ENGINE.md
                   PORTFOLIO_OPTIMIZATION_ENGINE.md
                   CAPITAL_ALLOCATION_ENGINE.md
                   EXECUTION_ENGINE.md
                   ANALYSIS_TEMPLATE.md
                   DECISION_SNAPSHOT_POLICY.md
Applies To       : States 12–20 (Ranking through Self Audit)
```

---

## 1. Purpose

This worksheet continues where `ANALYSIS_TEMPLATE.md` leaves off. It carries
each surviving candidate through Ranking, Committee, Red Team, the Final Risk
Gate, Master Decision, Portfolio Optimization, Capital Allocation, the
Execution Plan, and Self Audit (`STATE_MACHINE.md` States 12–20). Its
output feeds directly into `REPORT_TEMPLATE.md`. One copy per run, covering
all candidates from that run.

---

## 2. State 12 — Ranking (Decision Engine)

Per candidate, weighted per `DECISION_ENGINE.md` §Opportunity Score:

| Ticker | Market Alignment (20%) | Technical (20%) | Momentum (15%) | Volume (15%) | Catalyst (15%) | Portfolio Fit (10%) | Liquidity (5%) | Opportunity Score |
|---|---|---|---|---|---|---|---|---|
| | | | | | | | | |

Rating bands: 95+ Exceptional; 90–94 Very Strong; 80–89 Strong; below —
consult `DECISION_ENGINE.md` for lower tiers. Remove any candidate whose
evidence is insufficient and record why.

---

## 3. State 13 — Committee Review

Repeat this block per candidate. Each of the six roles evaluates
independently — do not let one role's row anchor another's. Vote options
are fixed per `COMMITTEE_ENGINE.md` §5 — do not substitute a simplified
Buy/Hold/Avoid scale.

| Role | Weight (§6) | Vote (STRONG BUY / BUY / WATCH / PASS / REDUCE / REJECT) | Points (§5A) | Confidence | Supporting evidence | Concern |
|---|---|---|---|---|---|---|
| Chief Market Strategist | 25% | | | | | |
| Risk Manager | 25% | | | | | |
| Portfolio Manager | 20% | | | | | |
| Technical Analyst | 15% | | | | | |
| Execution Specialist | 10% | | | | | |
| Options Specialist | 5% | | | | | |

```text
Consensus Score (§10 formula: Σ weight × points) :
Consensus Label      :          Unanimous (95+) / Strong (90+) / Moderate (80+) / Weak (70+) / No Consensus (<70)
Dissent (verbatim)    :
Risk Manager voted REDUCE or REJECT? :   Yes / No
  If Yes → mandatory Risk Engine escalation (§12) — this is an escalation,
  not itself a veto; only Risk Engine's own evaluation can bind.
```

No consensus → this candidate's provisional outcome is `WATCH` unless a
stricter gate below overrides it.

---

## 4. State 14 — Red Team Review

Every mandatory attack category (`RED_TEAM_ENGINE.md` §4A) SHALL produce an
explicit finding — a category with no row recorded is treated as not having
been attacked at all (§4A, §22):

All 11 rows are mandatory — 7 baseline plus 4 specialized. A specialized
category is not optional or combinable with another; each gets its own
finding:

| Category | Finding | Survived / Failed / Not Applicable |
|---|---|---|
| THESIS ATTACK | | |
| DATA ATTACK | | |
| ASSUMPTION ATTACK | | |
| VALUATION ATTACK (§4A: `not applicable` allowed here only, and only if no pricing/entry analysis exists yet) | | |
| RISK ATTACK | | |
| TIMING ATTACK | | |
| EXECUTION ATTACK (§4A: `not applicable` allowed here only, and only if no execution plan exists yet) | | |
| MACRO (specialized, §4A) | | |
| TECHNICAL (specialized, §4A) | | |
| PORTFOLIO (specialized, §4A) | | |
| CATALYST (specialized, §4A) | | |

```text
Ticker                     :
Counter-thesis             :
Counter-evidence           :
Primary risk               :
Failure scenario           :
Alternative (incl. cash)   :
Resilience of original thesis :
Critical-risk finding?     :          Yes / No
If yes — binding result    :          NO TRADE / WATCH / REDUCE / EXIT
```

A "Yes" here is binding per `CONSTITUTION.md` §5 and cannot be reversed by
Master Decision.

---

## 5. State 15 — Final Risk Gate

```text
Ticker                  :
Position size proposed  :
Maximum portfolio loss  :
Reward / risk ratio     :
Drawdown impact         :
Portfolio heat (post-trade) :
Event-risk check        :
Risk Score (0–100)      :          90+ Minimal / 80+ Low / 70+ Controlled / 60+ Elevated / <70 Reject
Gate result             :          Pass / NO TRADE / REDUCE / EXIT
```

Binding — no downstream engine may override a Reject.

---

## 6. State 16 — Master Decision

One block per candidate carried this far, integrating everything above:

```text
Ticker                     :
Final Outcome              :   EXECUTE / WATCH / HOLD / REDUCE / EXIT / NO TRADE
                               / INSUFFICIENT VERIFIED INFORMATION / SYSTEM REVIEW REQUIRED
Rationale                  :
Conditions (if any)        :
Weighted Composite Score   :   (§7 formula in MASTER_DECISION_ENGINE.md; Confidence-only, never outcome-determining)
Confidence                 :   Very High / High / Medium / Low / Very Low
                               (read directly off MDE §7's WCS-to-Confidence band table:
                               90-100/80-89/70-79/60-69/<60; capped at Low if a gate-linked
                               input is UNKNOWN — a cap only ever lowers the band reading,
                               never raises it. There is no separate "WCS < 70" rule.)
Uncertainty Tier (§14A)    :   Low / Medium / High / Critical — named reason(s)
                               (separate axis from Confidence — the two may disagree)
Gate results honoured      :   Verification ✅/❌   Risk ✅/❌   Red Team ✅/❌
```

If any gate above shows ❌, `Final Outcome` MUST be the outcome that gate
requires, per `STATE_MACHINE.md` §4 Outcome Semantics — Master Decision
integrates, it does not override. `SYSTEM REVIEW REQUIRED` applies only when
`STATE_MACHINE.md` §6A's revision-cycle limit (max 2) has been exceeded; do
not use it as a substitute for `NO TRADE`.

A full-gate-pass `EXECUTE` may still carry any Confidence down to Very Low —
this is expected, not an error, whenever the band table reads that low or a
non-gate-linked input was `UNKNOWN`. Record both values; do not silently
round Confidence up to match a passing outcome.

---

## 6A. Decision Snapshot

Required per `DECISION_SNAPSHOT_POLICY.md` §2 — complete this before
publication, not as an afterthought:

```text
Decision ID                      :   (unique per candidate/position — distinct from Run ID)
Run ID / timestamp               :
Market data as-of timestamp      :
Engine document versions used    :   Market __ / Portfolio __ / Risk __ / Scanner __
                                      Playbook __ / Options __ / Decision __ / Committee __
                                      Red Team __ / Master Decision __ / Execution __
Confidence Model version         :   (MASTER_DECISION_ENGINE.md §7)
Playbook Library version         :
Prompt version                   :   NOT APPLICABLE — v1.0 document-driven analysis
Model identifier                 :   NOT APPLICABLE — v1.0 document-driven analysis
Revision count (§6A State Machine) :
Failure Taxonomy category (if not EXECUTE) : DATA_FAILURE / MODEL_FAILURE / CONFLICT_FAILURE / RISK_REJECTION / EXECUTION_INVALID / SYSTEM_ERROR
Human Action (append after publication) :   EXECUTED AS PLANNED / EXECUTED DIFFERENTLY / DECLINED / NO ACTION YET / HUMAN OVERRIDE (see CONSTITUTION.md §6A)
Outcome (append during later Position Review or Journal entry) :
```

## 7. State 17 — Portfolio Optimization

Repeat this row per ticker/candidate carried into this state. `Decision` is
copied verbatim from §6 above — it is never re-decided here
(`PORTFOLIO_OPTIMIZATION_ENGINE.md` §1):

```text
Ticker                  :
Decision (echoed from §6, not recomputed) : EXECUTE / HOLD / REDUCE / EXIT / WATCH
Current Weight          :
Target Weight           :
Suggested Shares        :
Capital Released / Required :
Reason                  :
Priority (§3G in EXECUTION_ENGINE.md) : P1 / P2 / P3
```

Concentration check (§7A): current weight vs. the 10% single-stock hard cap
(`PORTFOLIO_ENGINE.md` §9A, unchanged by this engine) and this engine's own
6% preferred target / 8–10% advisory band.

If portfolio weight or cash data is incomplete: status
`PORTFOLIO OPTIMIZATION INCOMPLETE — DATA REQUIRED`
(`PORTFOLIO_OPTIMIZATION_ENGINE.md` §15) — this does not change the Final
Outcome already recorded in §6.

---

## 8. State 18 — Capital Allocation

Repeat per capital-releasing action from §7 (Portfolio Optimization):

```text
Source Ticker           :
Amount                  :
Decision                :   Deploy / Wait
Destination Ticker (if Deploy) :
Wait Category (if Wait) :   Hold Cash / Reserve for Earnings /
                             Reserve for Volatility / Wait for Better Setup
Reason                  :
```

`Deploy` is only valid into an `EXECUTE` candidate already published in §6
above — this engine never selects its own candidate. If deploying would
leave cash below `INVESTMENT_POLICY.md` §4's minimum, default to `Wait`.

If capital/candidate data is incomplete: status
`CAPITAL ALLOCATION INCOMPLETE — DATA REQUIRED`
(`CAPITAL_ALLOCATION_ENGINE.md` §6) — does not change the Final Outcome in
§6.

---

## 9. State 19 — Execution Plan (conditional)

`Not Applicable` unless Final Outcome is `EXECUTE`, `REDUCE`, or `EXIT`.

Execution Gate (§1A) — all six SHALL pass before the plan is `READY`:

```text
1. Quote freshness ok?            :
2. Liquidity ok (≤10% ADV/OI)?    :
3. Spread ok (order type per §3A tier)? :
4. Market status ok (session verified, no unconfirmed halt)? :
5. Order size validated (matches Risk Engine-approved size, not silently rounded)? :
6. Human approval presented (not self-approving)? :
```

```text
Action / contract           :
Order type (per EXECUTION_ENGINE.md §3A spread/liquidity table) :
Entry zone (timestamped)    :
Stop / invalidation         :
Target / partial-exit logic :
Position size / capital     :   (new EXECUTE: Risk Engine-approved size;
                                 existing-position REDUCE/EXIT: §7's
                                 Suggested Shares / Capital Released)
Maximum loss                :
Time horizon / review date  :
Liquidity check (§3B: ≤10% ADV / ≤10% OI) :
Slippage assumption (§3C: half-spread + volatility buffer) :
Maximum slippage (§3C hard cap, distinct from the assumption above) :
Valid For (order time-in-force, default 5 trading days) :
If Not Filled                :   Re-evaluate / Cancel / Resubmit at adjusted price
Execution Priority (§3G)    :   P1 / P2 / P3
Market session assumed / halt status (§3D) :
Options-only: bid-ask, IV, OI, days-to-expiry buffer (§3E) :
Human pre-flight checklist  :   [ ] reviewed live order screen  [ ] reviewed account constraints
```

If any critical execution input is stale: `DO NOT EXECUTE — REVERIFY`
(`EXECUTION_ENGINE.md` §3D; classified `EXECUTION_INVALID` per
`FAILURE_TAXONOMY.md`). For a run with several actionable positions, present
this section as the §3G Multi-Position Trade Plan batch table instead of
repeating a single-ticket block per row.

---

## 10. State 20 — Self Audit

Confirm each, per `OUTPUT_CONTRACT.md` §4:

- [ ] Every material fact is source- and freshness-labelled.
- [ ] No fabricated values or hidden assumptions.
- [ ] Portfolio review occurred before opportunity scanning.
- [ ] Position size, risk, stop, invalidation, and exits are defined for any
      actionable outcome.
- [ ] Committee, Red Team, and Risk outcomes are shown, including dissent.
- [ ] Master Decision did not override a verification, Risk, or Red Team veto.
- [ ] Every §7 Portfolio Optimization `Decision` value matches its §6 Master
      Decision `Final Outcome` for the same ticker — no re-decision.
- [ ] Every §8 Capital Allocation `Deploy` destination is an `EXECUTE`
      candidate already published in §6 — no invented opportunity.

If any box is unchecked, return to the earliest defective state per
`STATE_MACHINE.md` §6 before publishing.

---

## 11. Handoff to Report Template

Transfer every section above into the matching section of
`REPORT_TEMPLATE.md`, translating into Traditional Chinese for the final
report. Do not recompute scores at this stage — restate them.

---

End of Document

TRX Trading OS v1.0
