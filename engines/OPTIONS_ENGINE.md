# TRX Trading OS
## OPTIONS_ENGINE.md

```text
Document ID      : TRX-OPT-001
Document Name    : Options Engine
Version          : 1.0.0
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

The underlying must have a qualified candidate score of at least 80 before
option analysis. The engine evaluates:

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

The Liquidity Score considers bid-ask spread, open interest, daily option
volume, and execution difficulty. The Option Risk Model measures maximum
premium risk, probability of total loss, volatility risk, gap risk, liquidity
risk, and time risk.

The reward model states the verified premium, break-even, target-price basis,
expected-return assumptions, and risk/reward ratio. It does not invent a
probability or claim that a target will be reached.

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
expected holding period, and evidence status.

The proposed plan SHALL include profit exit, time exit, stop or thesis-break
criteria, catalyst exit, volatility exit, and the condition that invalidates the
underlying thesis. It must follow `EXECUTION_ENGINE.md` and remains subject to
final Risk and Red Team review.

---

## 8. Prohibited Behaviour

The engine SHALL NOT recommend illiquid contracts, ignore theta, IV, or spread,
or present unverified option values as facts. It cannot issue a final
recommendation or bypass the Master Decision Engine.

---

End of Document

TRX Trading OS v1.0
