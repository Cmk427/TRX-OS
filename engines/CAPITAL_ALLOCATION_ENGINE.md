# TRX Trading OS
## CAPITAL_ALLOCATION_ENGINE.md

```text
Document ID      : TRX-CAP-001
Document Name    : Capital Allocation Engine
Owner            : TRX project owner
Last Updated     : 2026-07-14
Version          : 1.0.0
Status           : Active
Classification   : Critical
Dependencies     : STATE_MACHINE.md
                   PORTFOLIO_OPTIMIZATION_ENGINE.md
                   INVESTMENT_POLICY.md
                   PORTFOLIO_ENGINE.md
Applies To       : State 18 — Deploy-or-Wait Decision for Released Capital
```

---

## 1. Purpose and Boundary

`PORTFOLIO_OPTIMIZATION_ENGINE.md` (State 17) states **how much** capital a
`REDUCE`/`EXIT` releases. Until now, nothing in the pipeline made an
explicit, auditable decision about **where that capital goes** — it was
folded into Portfolio Optimization Engine's own §8 as a secondary
afterthought. This engine makes that decision its own dedicated, gated
step, with its own failure modes and its own record.

This engine's primary question is **"deploy this released capital now, or
wait?"** — never "should this position have been reduced" (already decided
at State 16) and never "how much was released" (already computed at State
17). It holds **neither Constraint Authority nor Publication Authority**
(`CONSTITUTION.md` §4A):

- It never invents a new investment opportunity. It may only `Deploy` into
  an `EXECUTE` candidate Master Decision has **already published in this
  same run** — never a candidate this engine selects on its own.
- It never overrides Master Decision, Portfolio Optimization, or any
  binding gate.

---

## 2. Workflow Position

```text
Master Decision (16) → Portfolio Optimization (17)
  → Capital Allocation (18)   ← this document
  → Execution Plan (19) → Self Audit (20) → Final Report (21) → END
```

---

## 3. Required Inputs

- `PORTFOLIO_OPTIMIZATION_ENGINE.md` §6 — every `capital_released` value
  from this run.
- Master Decision Engine (16) — every `EXECUTE` candidate already published
  in this same run, with its required capital
  (`ENGINE_INTERFACE_CONTRACT.md` §11).
- `PORTFOLIO_ENGINE.md` — current cash balance and buying power.
- `INVESTMENT_POLICY.md` §4 — minimum and preferred cash reserve.

---

## 4. Decision

For each capital-releasing action from State 17, this engine SHALL decide
exactly one of:

- **Deploy** — into a specific, already-published, same-run `EXECUTE`
  candidate. The candidate's own capital requirement (from Master Decision/
  Portfolio Optimization) SHALL NOT be exceeded by the deployed amount.
- **Wait** — categorized using `PORTFOLIO_ENGINE.md` §16's existing
  categories, reused rather than reinvented:
  - Hold Cash
  - Reserve for Earnings
  - Reserve for Volatility
  - Wait for Better Setup

If deploying would leave cash below `INVESTMENT_POLICY.md` §4's minimum,
this engine SHALL default to `Wait` and state the shortfall explicitly —
it SHALL NOT deploy below the minimum to force a fit.

If no already-approved `EXECUTE` candidate exists in this run, the only
valid decision is `Wait` — this engine never searches for or proposes one;
that remains Scanner/Playbook/Committee's job in a future run.

---

## 5. Required Output

| Field | Content |
|---|---|
| Source Ticker | the `REDUCE`/`EXIT` ticker that released this capital |
| Amount | currency amount being allocated |
| Decision | `Deploy \| Wait` |
| Destination Ticker | the same-run `EXECUTE` candidate, if `Deploy`; `null` if `Wait` |
| Wait Category | one of §4's four categories, if `Wait`; `null` if `Deploy` |
| Reason | traces to §4's rule applied |
| Status | `READY \| CAPITAL ALLOCATION INCOMPLETE — DATA REQUIRED` |

---

## 6. Failure Conditions

If the amount released, the destination candidate's capital requirement, or
current cash/buying power is incomplete or contradictory, this engine
SHALL NOT guess an allocation. It SHALL report
**`CAPITAL ALLOCATION INCOMPLETE — DATA REQUIRED`**, attached only to this
step's own output — modeled on the same non-blocking pattern as
`PORTFOLIO_OPTIMIZATION_ENGINE.md` §15: it never changes the primary
outcome already published at State 16.

---

## 7. Success Criteria

A successful pass SHALL:

- Give every capital-releasing action from State 17 exactly one Deploy/Wait
  decision, never left unresolved;
- Never deploy into a candidate Master Decision did not already publish in
  this run; and
- Never deploy below `INVESTMENT_POLICY.md` §4's cash minimum.

---

End of Document

TRX Trading OS v1.0
