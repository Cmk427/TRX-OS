# TRX Trading OS
## OUTPUT_CONTRACT.md

```text
Document ID      : TRX-OUT-001
Document Name    : Output Contract
Version          : 1.0.0
Status           : Active
Classification   : Core
Dependencies     : STATE_MACHINE.md
                   VERIFICATION_POLICY.md
                   DATA_SOURCE_POLICY.md
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
recommendation or simulate missing values.

### B. Completed Decision Report

Every completed analysis — including `NO TRADE`, `WATCH`, `HOLD`, `REDUCE`,
and `EXIT` — SHALL contain all sections below. A non-applicable section MUST be
shown as `Not Applicable` with a reason, never silently omitted.

---

## 3. Mandatory Completed-Report Structure

1. **Executive Summary** — outcome, confidence, market regime, highest-priority
   action, largest portfolio risk, and whether new risk is permitted.
2. **Evidence Status and As-of Time** — market session, source / freshness
   summary, material V1–V5 labels, conflicts, and critical unknowns.
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
    confidence, and all binding gate results.
13. **Execution Plan** — `Not Applicable` or action, order type, entry, stop,
    target, size, capital, exit conditions, human pre-flight checks, and review
    schedule.
14. **Self Audit** — result of every required check below.

---

## 4. Self-Audit Checklist

The final report SHALL verify:

- prices, market status, news, events, and options data are labelled with
  source and freshness status;
- no fabricated values, unsupported claims, or hidden assumptions exist;
- portfolio review occurred before opportunity scanning;
- position size, risk, stop, invalidation, and exits are defined when action is
  proposed;
- Committee, Red Team, and Risk outcomes are shown; and
- Master Decision did not override a verification, Risk, or Red Team veto.

---

## 5. Confidence and Language

Overall confidence SHALL be Very High, High, Medium, Low, or Very Low and
explain why. Confidence is a statement about evidence quality and clarity, not
a forecast probability. The report must never promise returns, claim autonomous
execution, or obscure that `NO TRADE` is valid.

---

End of Document

TRX Trading OS v1.0
