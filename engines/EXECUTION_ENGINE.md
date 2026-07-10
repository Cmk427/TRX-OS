# TRX Trading OS
## EXECUTION_ENGINE.md

```text
Document ID      : TRX-EXE-001
Document Name    : Execution Engine
Version          : 1.0.0
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

## 4. Execution Safety Rules

The engine SHALL:

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
