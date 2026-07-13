# TRX Trading OS
## OPTIONS_ENGINE.md

```text
Document ID      : TRX-OPT-001
Document Name    : Options Engine
Version          : 1.4.0
Status           : Active
Classification   : Critical
Dependencies     : MARKET_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   SCANNER_ENGINE.md
                   PLAYBOOK_ENGINE.md
                   RISK_ENGINE.md
                   VERIFICATION_POLICY.md
                   DATA_SOURCE_POLICY.md
Applies To       : Long Call Research
```

---

## 1. Purpose

The Options Engine determines whether a liquid Long Call provides a superior
risk-adjusted expression of an already qualified underlying opportunity. Its
objective is asymmetric return with controlled risk, not maximum leverage.

It preserves the original TRX scope: Long Calls are supported; covered calls,
cash-secured puts, verticals, calendars, diagonals, and LEAPS are future
extensions and require their own approved rules before use.

---

## 2. Entry Gate

An option analysis may begin only after the underlying candidate has passed the
Market, Portfolio, preliminary Risk, Scanner, and Playbook gates. The engine
shall compare stock exposure with a Long Call; it cannot create an independent
trade thesis.

Required verified inputs are:

- underlying price and market session;
- option chain, bid, ask, strike, expiry, open interest, and volume;
- implied volatility and available Greeks;
- underlying catalyst, intended holding period, and invalidation level; and
- portfolio risk capacity and market regime.

If current option data is unavailable or stale under `DATA_SOURCE_POLICY.md`,
the result is `NOT ELIGIBLE — OPTION DATA UNVERIFIED`, not an estimate.

---

## 3. Contract Eligibility

The contract SHALL have a verified current quote, tradable liquidity,
reasonable bid-ask spread, sufficient open interest, sufficient daily volume,
and an expiry compatible with the expected holding period and catalyst.

Reject a contract that is illiquid, cannot be exited reasonably, has unknown
pricing, or creates unacceptable premium-loss, gap, volatility, or time risk.

---

## 4. Underlying and Greeks Review

The underlying must have a Candidate Quality Score (`SCANNER_ENGINE.md` §5,
available since State 09) of at least 80 before option analysis — not the
Decision Engine's Opportunity Score, which is not computed until State 12,
one state after Options Review. The engine evaluates:

| Factor | Requirement |
|---|---|
| Delta | Preferred range 0.55–0.75; explain deviations |
| Gamma | Assess acceleration potential and sensitivity |
| Theta | Ensure time decay fits the intended holding period |
| Vega | Assess IV-expansion and IV-contraction exposure |
| IV | Review current IV, IV Rank, IV percentile, historical and relative IV |
| Strike | Compare ATM, ITM, and OTM for probability, efficiency, and risk |
| Expiry | Balance catalyst timing, liquidity, and theta decay |

Higher delta may reduce leverage and theta sensitivity but increases capital
required. High IV may make premium expensive; low IV is not automatically an
entry signal. Every inference remains distinct from verified inputs.

---

## 5. Liquidity, Risk, and Reward

The Liquidity Score is 0–100 (same shared scale as every other score in
this system, per `system/ENGINE_INTERFACE_CONTRACT.md` §1A) and considers
bid-ask spread, open interest, daily option volume, and execution
difficulty; it is the same number §6's Option Quality Score weights at 15%.
The Option Risk Model measures maximum
premium risk, probability of total loss, volatility risk, gap risk, liquidity
risk, and time risk.

The reward model states the verified premium, break-even, target-price basis,
expected-return assumptions, and risk/reward ratio. It does not invent a
probability or claim that a target will be reached.

### 5A. Named Option Risk Factors

Options carry risk shapes an equity stop-loss model cannot express. This
engine, not `RISK_ENGINE.md`, owns the following, each of which SHALL be
explicitly assessed and reported (not merely implied by the Greeks table in
§4):

| Factor | What it is | Requirement |
|---|---|---|
| Theta decay | Time value lost per day as expiry approaches | Already assessed in §4; confirm decay rate remains acceptable across the full intended holding period, not just at entry |
| IV crush risk | A sharp implied-volatility drop immediately after a known catalyst (e.g. earnings) that reduces premium independent of price direction | Explicitly flag when the position is held through a scheduled IV-moving event; state expected pre/post-event IV if known, or mark unknown |
| Delta exposure | Directional sensitivity to the underlying | Already scored in §4; restate here as the position's effective directional risk for portfolio-level aggregation |
| Gamma risk | Rate of change of delta — how quickly directional exposure can accelerate near the strike as expiry nears | Already assessed in §4; flag when gamma risk is elevated (near-the-money, near expiry) as a distinct line item, not folded silently into "Greeks look fine" |
| Expiration risk | Risk that time runs out before the thesis plays out, and mechanics at/near expiry | State days-to-expiry versus intended holding period explicitly; a Long Call bought under this engine's rules is never held through exercise by assignment risk to the buyer — assignment risk applies to option *sellers*, not buyers, and does not apply here. State this explicitly per contract so it is never silently assumed to be a live risk that was overlooked, nor silently applied where it does not belong |
| Liquidity / spread risk | Cost and feasibility of exiting before expiry | Already assessed via the Liquidity Score above; restate as a named risk line, not just a score |

Maximum premium risk and Portfolio Heat roll-up for this position use
**Premium at Risk**, not equity stop-distance — see `RISK_ENGINE.md` §28A
Options Risk Bridge for the exact aggregation rule. This document defines
the risk factors above; `RISK_ENGINE.md` only aggregates the resulting
number into portfolio-level Heat and the Risk Score deduction model. Neither
document may assume the other has covered a factor it has not itself
assessed.

---

## 6. Option Quality Score

The Option Quality Score is 0–100 and uses the original weighting model:

| Component | Weight |
|---|---:|
| Underlying quality | 30% |
| Liquidity | 15% |
| IV environment | 15% |
| Greeks | 15% |
| Risk | 15% |
| Catalyst | 10% |

Classification: 95+ Institutional Grade; 90+ Excellent; 85+ Strong; 80+
Qualified; below 80 Reject. A passing score cannot override a Market,
Portfolio, Risk, or Red Team veto.

---

## 7. Required Output

For every eligible contract, report the underlying, current price and timestamp,
contract, strike, expiry, premium, delta, gamma, theta, vega, IV Rank,
Liquidity Score, Option Quality Score, bull thesis, bear thesis, maximum risk,
expected holding period, and evidence status, plus the named risk factors in
§5A: IV crush exposure (or "none — no scheduled event in holding window"),
gamma risk flag, expiration risk (days-to-expiry vs. holding period), and an
explicit assignment-risk statement (not applicable to a bought Long Call).

The proposed plan SHALL include profit exit, time exit, stop or thesis-break
criteria, catalyst exit, volatility exit, and the condition that invalidates the
underlying thesis. It must follow `EXECUTION_ENGINE.md` and remains subject to
final Risk and Red Team review.

---

## 8. Prohibited Behaviour

The engine SHALL NOT recommend illiquid contracts, ignore theta, IV, or spread,
or present unverified option values as facts. It cannot issue a final
recommendation or bypass the Master Decision Engine.

The Options Engine additionally SHALL NOT judge what maximum risk is
*acceptable* for the account — it only measures and reports maximum risk
(§5, §5A, §7). Whether a given Premium at Risk is acceptable is decided
solely by `RISK_ENGINE.md` (via the Options Risk Bridge, §28A of that
document) against the account's actual risk budget. An Options Engine
output that says "high win-rate Long Call" without the full §7 handoff
(premium, delta, gamma, theta, vega, IV Rank, liquidity, named risk factors)
is incomplete and cannot reach the Risk Gate — Risk Engine has nothing to
evaluate acceptability against otherwise.

---

## 8A. Time Risk Rules

Direction can be correct and a Long Call can still lose to time. These
rules apply for the life of the position, distinct from the entry-only
minimum-DTE-buffer rule in `EXECUTION_ENGINE.md` §3E:

- **Minimum DTE at entry**: no new Long Call entry with fewer than 30 days
  to expiration, regardless of Playbook or catalyst timing.
- **Maximum intended holding period**: the stated holding period SHALL NOT
  exceed 60% of the contract's DTE at entry (e.g., a 30 DTE contract's
  intended hold is capped at 18 calendar days), leaving a decay buffer
  before expiry mechanics dominate.
- **Theta loss review trigger**: if cumulative theta-driven premium loss
  reaches 20% of the originally paid premium while the underlying thesis is
  not yet confirmed invalid, this SHALL force a mandatory Position Review
  (per `RISK_ENGINE.md` §23) specifically evaluating whether time decay has
  made continued holding unfavorable even though the directional thesis is
  intact — this is a distinct exit trigger from thesis invalidation.
- **Exit before expiry**: `EXECUTION_ENGINE.md` §3E's 5-calendar-day
  minimum-DTE-for-new-entry rule also governs continued holding — a Long
  Call SHALL be exited or explicitly re-justified once DTE falls to 5 or
  below, independent of thesis status.
- **IV-collapse exit trigger**: if IV Rank drops by 15 or more points from
  its value at entry within one trading session, without a compensating
  favourable price move toward the stated target, this SHALL force the same
  mandatory Position Review as the theta-loss trigger above — an IV crush
  can erode most of a Long Call's value even when the underlying moved in
  the intended direction, and this is the trigger that catches it. This is
  distinct from a scheduled-event IV crush already disclosed under §5A
  ("no scheduled event in holding window") — this trigger applies whenever
  IV actually drops sharply, scheduled or not.

Exit mechanics overall — profit-taking, stop-loss, thesis-break, and event
exit — are already required in §7 Required Output ("profit exit, time exit,
stop or thesis-break criteria, catalyst exit, volatility exit"); the five
triggers above (min DTE, holding cap, theta-loss, exit-before-expiry,
IV-collapse) are the *time-and-volatility-specific* exit conditions that
exist even when the directional thesis itself remains correct — "direction
was right" is never sufficient on its own to call a Long Call a success.

These numbers are v1.0 defaults, not a claim of optimality; they exist so
"direction was right" is never silently treated as "the trade worked."

---

## 8B. PROFIT AND LOSS EXIT RULES

§7 Required Output already names "profit exit" and "stop or thesis-break
criteria" as fields every plan must include, but names them without a
number — this section is that number, so a plan cannot satisfy §7 with a
purely qualitative statement (e.g. "take profit when appropriate"):

- **Profit-taking review trigger**: if unrealized gain reaches 100% of the
  originally paid premium (the position's value has doubled), this SHALL
  force a mandatory Position Review (per `RISK_ENGINE.md` §23) to evaluate
  partial or full profit-taking. This is a review trigger, not an automatic
  exit — the human trader retains final order authority per
  `CONSTITUTION.md` Rule 1, consistent with the theta-loss and IV-collapse
  triggers in §8A.
- **Loss-cut review trigger**: if unrealized loss reaches 50% of the
  originally paid premium, this SHALL force a mandatory Position Review to
  evaluate exiting before further theta decay or an adverse price move
  compounds the loss. Reaching this trigger does not by itself mean the
  thesis is invalid (see `RED_TEAM_ENGINE.md` §17 Thesis Break Conditions
  for that separate determination) — it means the position's remaining
  premium at risk warrants an explicit re-justification.

Both triggers are independent of, and can fire alongside, the §8A time-risk
triggers and any Thesis Break Condition — a position can simultaneously hit
a loss-cut review trigger and remain thesis-intact, or clear both time-risk
triggers while still requiring a profit-taking review. These are v1.0
defaults, not a claim of optimality, matching the framing in §8A.

---

End of Document

TRX Trading OS v1.0
