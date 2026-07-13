# TRX Trading OS
## EXECUTION_ENGINE.md

```text
Document ID      : TRX-EXE-001
Document Name    : Execution Engine
Version          : 1.1.0
Status           : Active
Classification   : Critical
Dependencies     : STATE_MACHINE.md
                   MASTER_DECISION_ENGINE.md
                   RISK_ENGINE.md
                   OUTPUT_CONTRACT.md
                   DATA_SOURCE_POLICY.md
Applies To       : Human-Executed Trade Plans
```

---

## 1. Purpose and Boundary

The Execution Engine converts a final `EXECUTE`, `REDUCE`, or `EXIT` outcome
into a precise, human-reviewable plan. It never sends, places, modifies, or
cancels an order. It cannot turn a `WATCH`, `NO TRADE`, or failed-gate outcome
into execution.

This engine's primary question is **"can this be executed?"** (§1A Execution
Gate), not "what trade should this be?" — that question was already answered
upstream by Master Decision. An engine that only ever produces entry/stop/
target/size numbers is a trade-plan generator, not an execution boundary;
the Execution Gate below is what makes this an actual gate.

---

## 1A. Execution Gate — Can Execute?

Before any plan may be marked `READY`, it SHALL pass all six checks below, in
order. Any failure marks the plan `DO NOT EXECUTE — REVERIFY` (or, for check
6, simply incomplete pending human action) rather than partially ready:

| # | Check | Pass condition | Defined in |
|---|---|---|---|
| 1 | Quote freshness | Current price/quote is within `DATA_SOURCE_POLICY.md` §3's freshness rule at plan time | §3D |
| 2 | Liquidity | Position size ≤ 10% of ADV (equity) or ≤ 10% of OI (option) | §3B |
| 3 | Spread | Spread tier determines order type per §3A; a "Poor" tier requires explicit slippage-risk acknowledgement, not silent proceed | §3A, §3B |
| 4 | Market status | Session (Pre-Market/Regular/After-Hours) is verified and no unconfirmed halt is active | §3D |
| 5 | Order size validation | Proposed capital and share/contract count matches the position size the Risk Engine approved (`RISK_ENGINE.md` §6, §24) — not a larger or smaller size substituted without a recorded reason | new, this section |
| 6 | Human approval | The pre-flight checklist (§3) has been presented; the plan is never self-approving | §3 |

Check 5 exists because none of the round-1 rules explicitly verified that
the *proposed* size matches the *approved* size — an execution plan that
silently rounds up "for a cleaner order" or drifts from the Risk Engine's
sizing output fails this check even if every other number is individually
valid.

A plan that passes all six is `READY` for human review; it is never
`APPROVED` or `EXECUTED` by this engine — those states can only be set by
the human, per `CONSTITUTION.md` Rule 1.

---

## 2. Entry Conditions

The engine may run only after the Master Decision Engine has recorded a valid
outcome and all binding gates have passed. It requires current verified pricing
and market-session status, approved asset or option contract, entry zone,
position-size limit, stop/invalidation, target or exit logic, time horizon,
liquidity assessment, and available buying capacity.

If a critical execution input is stale, unavailable, or contradictory, the plan
is marked `DO NOT EXECUTE — REVERIFY` and returned for the appropriate prior
state. No price, spread, or order type may be fabricated.

---

## 3. Human Execution Plan

Every actionable plan SHALL specify:

- action and asset / option contract;
- timestamped entry zone and permitted order type;
- maximum position size, capital required, and maximum loss;
- initial stop, thesis invalidation, target / partial-exit logic, and emergency
  exit condition;
- time horizon, review time, event trigger, and expiry handling when relevant;
- liquidity, spread, and slippage considerations; and
- a pre-flight checklist confirming the human has independently reviewed the
  current order screen and account constraints.

Prices are planning parameters, not live instructions. The human trader must
reverify all time-sensitive values before any order is submitted.

---

## 3A. Order Type Decision

The engine SHALL choose a proposed order type by rule, not default to
"market" or "limit" arbitrarily:

| Spread (bid-ask, % of price) | Liquidity tier | Proposed order type |
|---|---|---|
| ≤ 0.10% | High (tight, high volume/OI) | Market acceptable, limit still preferred |
| 0.10%–0.50% | Normal | Limit at or inside mid-price |
| 0.50%–1.5% | Reduced | Limit only; state a maximum acceptable fill price |
| > 1.5% | Poor | Limit only, reduced size, or downgrade the plan — flag that execution cost may erode the thesis |

A market order is never proposed when spread exceeds 0.50% of price. Options
contracts default to limit orders regardless of tier, because option spreads
are typically wider relative to premium than equity spreads relative to
price.

## 3B. Liquidity and Spread Checks

- **Liquidity check**: the plan's position size SHALL NOT exceed 10% of the
  security's average daily volume (equity) or 10% of open interest (option
  contract), to keep the position exitable without the trader itself moving
  the price.
- **Spread check**: use the Order Type Decision table above; a spread in the
  "Poor" tier requires an explicit note that slippage risk is elevated and a
  smaller size or no-trade may be preferable.

## 3C. Slippage Assumption and Expected Fill Price

Slippage is never assumed to be zero. The plan SHALL state its slippage
assumption explicitly using: `half the quoted bid-ask spread` plus a
`volatility buffer` (one average true range tick, or the option-equivalent
implied-move buffer for contracts).

The plan SHALL compute and state an explicit **Expected Fill Price** from
this: `Expected Fill Price = limit price ± slippage assumption` (add for a
buy, subtract for a sell), plus a stated **Market Impact** note when
position size approaches the §3B liquidity cap (10% of ADV/OI) — at that
size, the order itself may move the price beyond the slippage assumption,
and the plan SHALL flag this rather than silently using the same slippage
figure as a small order would use. This is a planning estimate for human
review, not a guaranteed fill price, and not a claim that TRX has simulated
the order against a live order book.

## 3D. Market Hours and Stale Quote Handling

- The plan SHALL state the market session it assumes (Pre-Market / Regular /
  After-Hours) and SHALL NOT propose an order type or size appropriate only
  to Regular session liquidity if the current session is Pre-Market or
  After-Hours.
- A trading halt, if known, invalidates the plan until confirmed resumed.
- Per `DATA_SOURCE_POLICY.md` §3, current price/quote data has a freshness
  requirement. If the quote used to build this plan has gone stale by the
  time of self-audit or publication, the plan SHALL be marked
  `DO NOT EXECUTE — REVERIFY` rather than published with a stale price
  presented as current.

## 3E. Options-Specific Execution Fields

For any options plan, in addition to the equity fields above, the plan SHALL
state: bid-ask spread at the time of planning, IV at the time of planning,
open interest, and a minimum days-to-expiry buffer — the engine SHALL NOT
propose a new entry within 5 calendar days of expiry unless the stated
holding period is shorter than that buffer and the rationale is explicit
(e.g., an intentional short-dated event trade). This buffer exists to avoid
entries where liquidity and theta decay accelerate before the position can
reasonably be managed.

---

## 4. Execution Safety Rules

The engine SHALL:

- apply the Order Type Decision, Liquidity/Spread Checks, Slippage
  Assumption, and Market Hours / Stale Quote rules in §3A–§3D (and §3E for
  options) to every plan — none of these may be skipped or defaulted;
- prefer limit or other explicitly justified order types when liquidity or
  spread warrants it;
- prohibit a plan whose stop, maximum loss, or position size is undefined;
- respect Risk Engine limits, market restrictions, portfolio capacity, and
  final invalidation conditions;
- flag earnings, macro events, option expiration, overnight gap, and liquidity
  risks; and
- produce an exit and review plan before the human acts.

---

## 5. Completion Record

The engine records the proposed plan, verification timestamps, assumptions,
identified risks, and review triggers. It does not claim that an order was
filled. Any actual fill, cancellation, or position change must be supplied by
the human and revalidated in a subsequent portfolio review.

---

End of Document

TRX Trading OS v1.0
