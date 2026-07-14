# TRX Trading OS
## OUTPUT_CONTRACT.md

```text
Document ID      : TRX-OUT-001
Document Name    : Output Contract
Version          : 1.3.0
Status           : Active
Classification   : Core
Dependencies     : STATE_MACHINE.md
                   VERIFICATION_POLICY.md
                   DATA_SOURCE_POLICY.md
                   DECISION_SNAPSHOT_POLICY.md
                   FAILURE_TAXONOMY.md
                   PORTFOLIO_OPTIMIZATION_ENGINE.md
                   CAPITAL_ALLOCATION_ENGINE.md
Applies To       : Every final report
```

---

## 1. Purpose

This contract defines the mandatory final-report structure of TRX Trading OS.
Reports SHALL use Traditional Chinese and Markdown. They SHALL distinguish
Verified Facts, Analysis, Inferences, Assumptions, Unknowns, and
Recommendations. Time-sensitive claims must include evidence status and an
as-of timestamp.

---

## 2. Report Profiles

### A. Critical Verification Stop

When State 03 or 04 cannot complete, the report SHALL state:

`STATUS: INSUFFICIENT VERIFIED INFORMATION`

It SHALL list the failed critical input, source / freshness status, decision
impact, and exact information required to resume. It SHALL NOT produce a trade
recommendation or simulate missing values. Classify this outcome as
`DATA_FAILURE` per `FAILURE_TAXONOMY.md`.

### A2. System Review Required

When `STATE_MACHINE.md` §6A's revision-cycle limit is exceeded, the report
SHALL state:

`STATUS: SYSTEM REVIEW REQUIRED`

It SHALL name every state involved in the unresolved revision loop, what each
revision attempted, and why the disagreement could not be resolved
automatically. It SHALL NOT publish `NO TRADE` or any other outcome in place
of this status — the honest report is that the system could not converge,
not that it converged on caution. Classify as `CONFLICT_FAILURE` per
`FAILURE_TAXONOMY.md`.

### B. Completed Decision Report

Every completed analysis — including `NO TRADE`, `WATCH`, `HOLD`, `REDUCE`,
and `EXIT` — SHALL contain all sections below. A non-applicable section MUST be
shown as `Not Applicable` with a reason, never silently omitted.

The report structure itself SHALL NOT create pressure to populate every
section with a positive finding. Section 6 (New Opportunities) with zero
candidates and `Not Applicable` is exactly as complete a report as one
listing three — the mandatory-structure requirement is about *completeness
of consideration*, not about *finding something to recommend*. A Scanner
returning `NO ELIGIBLE CANDIDATE` (`STATE_MACHINE.md` §3, State 09) is a
successful, complete run of that state, not a shortfall to compensate for
elsewhere in the report.

---

## 3. Mandatory Completed-Report Structure

1. **Executive Summary** — outcome, confidence, **uncertainty tier** (Low /
   Medium / High / Critical, with named reasons — see §5; a separate axis
   from confidence, not a restatement of it), market regime, highest-priority
   action, largest portfolio risk, whether new risk is permitted, and (for any
   non-`EXECUTE` outcome) the `FAILURE_TAXONOMY.md` category.
2. **Evidence Status, As-of Time, and Decision Snapshot** — market session,
   source / freshness summary, material V1–V5 labels, conflicts, critical
   unknowns, and the required `DECISION_SNAPSHOT_POLICY.md` §2 fields (engine
   document versions used, Confidence Model version, Playbook Library
   version, and revision count).
3. **Market Regime** — Market Score, trend, breadth, volatility, macro events,
   sector rotation, recommended exposure, and confidence.
4. **Portfolio Health** — health score, cash, buying power, largest position,
   sector exposure, correlation risk, portfolio heat, and constraints.
5. **Existing Position Review** — for every position: ticker, status, health,
   trend, risk, action, and reason.
6. **New Opportunities** — maximum three; ticker, candidate/opportunity score,
   verified catalyst state, entry zone, target, invalidation, holding period,
   and material unknowns.
7. **Playbook Assignment** — playbook name and ID, match score, rationale,
   expected behaviour, and rule references.
8. **Options Analysis** — `Not Applicable` or contract, strike, expiration,
   verified pricing, Greeks, IV Rank, liquidity, quality score, and max risk.
9. **Risk Assessment** — Risk Score definition, maximum portfolio loss,
   position size, reward/risk, drawdown impact, risk budget, heat, and vetoes.
10. **Red Team Review** — counter-thesis, counter-evidence, primary risk,
    failure scenario, alternative, cash alternative, resilience, and adjustment.
11. **Investment Committee Decision** — each role's vote, confidence,
    supporting evidence, concern, consensus, and dissent.
12. **Master Decision** — the sole final outcome, rationale, conditions,
    confidence, uncertainty tier and named reasons, and all binding gate
    results.
13. **Portfolio Action Plan** — `Not Applicable` (with reason) or a fixed
    four-tier structure per `PORTFOLIO_OPTIMIZATION_ENGINE.md` §6/§10:
    **Immediate Action**, **This Week**, **Monitor**, **No Action**, scoped
    to **existing positions only** (`HOLD`/`REDUCE`/`EXIT`/`WATCH`) — no
    `EXECUTE` ticker appears here, since Portfolio Optimization Engine
    never processes one (`PORTFOLIO_OPTIMIZATION_ENGINE.md` §1); new
    positions appear in item 14 instead. Every entry under a tier SHALL
    state Ticker, Shares, Execution (order type/priority per
    `EXECUTION_ENGINE.md` §3G), and Reason. Every `Decision` value shown
    here SHALL trace to a `Decision` already published in section 12
    (Master Decision) for that ticker — this section quantifies an
    outcome, it never introduces one. When portfolio weight or cash data
    is incomplete, this section instead shows
    `PORTFOLIO OPTIMIZATION INCOMPLETE — DATA REQUIRED`
    (`PORTFOLIO_OPTIMIZATION_ENGINE.md` §15) without changing the report's
    primary outcome, the same non-blocking pattern as item 15 below.
14. **Capital Allocation** — `Not Applicable` (with reason) or one entry per
    same-run `EXECUTE` candidate and per capital-releasing action from item
    13: Decision (`Deploy`/`Rotate`/`Reserve Cash`/`Wait`), Destination
    Ticker, Source Ticker (if `Rotate`), Shares (if `Deploy`/`Rotate`,
    computed per `CAPITAL_ALLOCATION_ENGINE.md` §5 and never exceeding the
    Risk Engine's approved size), Amount, and Reason
    (`CAPITAL_ALLOCATION_ENGINE.md` §6). A `Deploy`/`Rotate` destination
    SHALL always trace to an `EXECUTE` candidate already published in
    section 12 — this section never introduces a new opportunity. When
    capital or candidate data is incomplete, this section instead shows
    `CAPITAL ALLOCATION INCOMPLETE — DATA REQUIRED`
    (`CAPITAL_ALLOCATION_ENGINE.md` §7), the same non-blocking pattern as
    item 15 below.
15. **Execution Plan** — `Not Applicable`; or a plan with action, order type,
    entry, stop, target, size, capital, exit conditions, human pre-flight
    checks, and review schedule; or, when execution-time inputs (price,
    session, liquidity) have gone stale since publication, the plan status
    `DO NOT EXECUTE — REVERIFY` (`EXECUTION_ENGINE.md` §1A, §3D) in place of
    a ready plan. This status applies only to this section — it does not
    change the report's primary outcome (`SYSTEM.md` §6), which remains
    `EXECUTE`/`REDUCE`/`EXIT` as already determined by Master Decision. For
    a run with several actionable positions, this section may be presented
    as the `EXECUTION_ENGINE.md` §3G batch table rather than one single-
    ticket plan.
16. **Self Audit** — result of every required check below.

---

## 4. Self-Audit Checklist

The final report SHALL verify:

- prices, market status, news, events, and options data are labelled with
  source and freshness status;
- no fabricated values, unsupported claims, or hidden assumptions exist;
- portfolio review occurred before opportunity scanning;
- position size, risk, stop, invalidation, and exits are defined when action is
  proposed;
- Committee, Red Team, and Risk outcomes are shown;
- Master Decision did not override a verification, Risk, or Red Team veto;
  and
- the Portfolio Action Plan (§3 item 13) is present, scoped to existing
  positions only, and every ticker listed there traces to a `Decision`
  already published in the Master Decision section — no ticker appears in
  the Portfolio Action Plan with a `Decision` value absent from Master
  Decision, and no `EXECUTE` ticker appears there at all; and
- the Capital Allocation section (§3 item 14) is present, and every
  `Deploy`/`Rotate` destination traces to an `EXECUTE` candidate already
  published in the Master Decision section, with no `Shares` value
  exceeding the Risk Engine's approved size for that candidate.

---

## 5. Confidence, Uncertainty, and Language

Overall confidence SHALL be Very High, High, Medium, Low, or Very Low and
explain why, computed via the Weighted Composite Score in
`MASTER_DECISION_ENGINE.md` §7. Confidence is a statement about evidence
quality and clarity, not a forecast probability, and it is entirely separate
from outcome selection: an `EXECUTE` outcome that passed every binding gate
may still carry Low Confidence, and the report SHALL show this combination
plainly rather than inflate Confidence to match a passing outcome.

**Confidence is not yet calibrated against historical outcomes.** TRX v1.0
has no backtesting or trade-journal layer (see `docs/ROADMAP.md` v1.2
Journal and Performance Review), so a stated Confidence of "High" is not a
claim that similarly-labelled past analyses succeeded at a correspondingly
high rate — it has not been measured. The report SHALL NOT imply Confidence
is a validated success probability until that calibration data exists.

**Uncertainty is a separate, mandatory field, not a synonym for low
confidence.** It is one of four qualitative tiers — Low, Medium, High,
Critical — each stated with its specific named reason(s): e.g., insufficient
historical sample size for the assigned Playbook, an unprecedented or
unclassified market regime (`MARKET_ENGINE.md` § Regime Transition Control),
a source-freshness edge case that passed verification but is close to its
freshness limit, or an unresolved same-tier data conflict
(`DATA_SOURCE_POLICY.md` §5). Confidence and Uncertainty MAY disagree: High
Confidence with High Uncertainty is a valid, expected combination meaning
"the available evidence is clear and consistent, but the situation itself
(e.g. the regime) is sufficiently novel that clear evidence may not
generalise."
Never collapse the two into one number.

The report must never promise returns, claim autonomous execution, or
obscure that `NO TRADE` is valid.

---

End of Document

TRX Trading OS v1.0
