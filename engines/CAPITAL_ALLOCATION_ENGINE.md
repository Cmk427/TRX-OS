# TRX Trading OS
## CAPITAL_ALLOCATION_ENGINE.md

```text
Document ID      : TRX-CAP-001
Document Name    : Capital Allocation Engine
Owner            : TRX project owner
Last Updated     : 2026-07-14
Version          : 1.1.0
Status           : Active
Classification   : Critical
Dependencies     : STATE_MACHINE.md
                   PORTFOLIO_OPTIMIZATION_ENGINE.md
                   INVESTMENT_POLICY.md
                   PORTFOLIO_ENGINE.md
                   RISK_ENGINE.md
                   MASTER_DECISION_ENGINE.md
Applies To       : State 18 — Deploy/Rotate/Reserve/Wait Decision and New-Position Sizing
```

---

## 1. Purpose and Boundary

`PORTFOLIO_OPTIMIZATION_ENGINE.md` (State 17) states **how much** capital an
existing position's `REDUCE`/`EXIT` releases — and nothing else. This
engine owns everything downstream of that number: deciding where capital
goes, and — because Portfolio Optimization Engine never processes an
`EXECUTE` decision at all (`PORTFOLIO_OPTIMIZATION_ENGINE.md` §1) — sizing
every new position itself. Drawing the boundary this way means exactly one
engine ever computes a "buy N shares of this ticker" number for any given
ticker in a run, never two.

This engine holds **neither Constraint Authority nor Publication
Authority** (`CONSTITUTION.md` §4A):

- It never invents a new investment opportunity. It may only size or
  deploy into an `EXECUTE` candidate Master Decision has **already
  published in this same run** — never a candidate this engine selects on
  its own.
- It never exceeds the Risk Engine's already-approved position size for
  that candidate (`RISK_ENGINE.md` §6/§24) — this engine sizes *within*
  that ceiling, it does not compute risk itself.
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
  from this run's existing-position `REDUCE`/`EXIT` actions.
- Master Decision Engine (16) — every `EXECUTE` candidate already published
  in this same run (`ENGINE_INTERFACE_CONTRACT.md` §11).
- Risk Engine (07/15) — the already-approved position size for each
  `EXECUTE` candidate (`RISK_ENGINE.md` §6/§24) — the ceiling this engine's
  own sizing (§5) may never exceed.
- `PORTFOLIO_ENGINE.md` — current cash balance and buying power, and §16's
  capital-reallocation categories (reused in §4).
- `INVESTMENT_POLICY.md` §4 — minimum and preferred cash reserve.

---

## 4. Decision

For each `EXECUTE` candidate and each unit of released/available capital,
this engine SHALL decide exactly one of:

- **Deploy** — capital (from general buying power / cash, not tied to a
  specific same-run release) goes into a specific, already-published
  same-run `EXECUTE` candidate.
- **Rotate** — the same as Deploy, except the capital source is
  specifically a same-run `REDUCE`/`EXIT` release from
  `PORTFOLIO_OPTIMIZATION_ENGINE.md` §6 — labelled separately from Deploy
  so the paired Exit-then-Execute relationship
  (`docs/PORTFOLIO_MANAGEMENT_GUIDE.md` §2, "Rotate") is auditable as one
  record rather than two unrelated-looking entries.
- **Reserve Cash** — hold, specifically to build or maintain
  `INVESTMENT_POLICY.md` §4's minimum/preferred cash reserve (renames
  `PORTFOLIO_ENGINE.md` §16's "Hold Cash" category for this engine's own
  decision vocabulary — same category, this engine's name for it).
- **Wait** — hold for a specific situational reason, categorized using
  `PORTFOLIO_ENGINE.md` §16's remaining categories: Reserve for Earnings /
  Reserve for Volatility / Wait for Better Setup.

If cash would fall below `INVESTMENT_POLICY.md` §4's minimum after a
Deploy/Rotate, this engine SHALL choose `Reserve Cash` instead and state
the shortfall explicitly — it SHALL NOT deploy below the minimum to force
a fit.

If no already-approved `EXECUTE` candidate exists in this run, the only
valid decisions are `Reserve Cash` or `Wait` — this engine never searches
for or proposes a candidate itself; that remains Scanner/Playbook/
Committee's job in a future run.

If more capital is available than every same-run `EXECUTE` candidate
requires, or more candidates need capital than are available, allocate by
the ranking Master Decision/Decision Engine already established
(`MASTER_DECISION_ENGINE.md` §12 Multiple Opportunities) — this engine
reuses that ranking, it does not compute its own.

---

## 5. Sizing

For every `Deploy` or `Rotate` decision, this engine SHALL compute:

```text
Shares = min(
  Risk Engine's approved size for this candidate (§6/§24, a hard ceiling),
  available capital ÷ current verified price
)
```

This may be **less** than the Risk Engine's approved size if available
capital is insufficient — a partial fill at the capital ceiling is valid.
It SHALL NEVER be **more** than the Risk Engine's approved size; this
engine has no authority to size beyond what Risk Engine already approved.
If available capital cannot fund even one share/contract at a reasonable
round lot, this engine SHALL choose `Reserve Cash` or `Wait` instead of
forcing an undersized position, and SHALL state that reason explicitly.

---

## 6. Required Output

| Field | Content |
|---|---|
| Decision | `Deploy \| Rotate \| Reserve Cash \| Wait` |
| Destination Ticker | the same-run `EXECUTE` candidate, if `Deploy`/`Rotate`; `null` otherwise |
| Source Ticker | the releasing `REDUCE`/`EXIT` ticker, if `Rotate`; `null` otherwise |
| Shares | computed per §5, if `Deploy`/`Rotate`; `null` otherwise |
| Amount | currency amount being allocated or reserved |
| Wait/Reserve Category | one of §4's Reserve Cash / Wait sub-categories, if applicable; `null` if `Deploy`/`Rotate` |
| Reason | traces to §4's rule and, for Deploy/Rotate, §5's sizing computation |
| Status | `READY \| CAPITAL ALLOCATION INCOMPLETE — DATA REQUIRED` |

---

## 7. Failure Conditions

If the Risk Engine's approved size for a candidate, the amount released, or
current cash/buying power is incomplete or contradictory, this engine
SHALL NOT guess an allocation or a share count. It SHALL report
**`CAPITAL ALLOCATION INCOMPLETE — DATA REQUIRED`**, attached only to this
step's own output — modeled on the same non-blocking pattern as
`PORTFOLIO_OPTIMIZATION_ENGINE.md` §15: it never changes the primary
outcome already published at State 16.

---

## 8. Success Criteria

A successful pass SHALL:

- Give every same-run `EXECUTE` candidate and every released-capital amount
  exactly one Deploy/Rotate/Reserve Cash/Wait decision, never left
  unresolved;
- Never deploy into a candidate Master Decision did not already publish in
  this run, and never compute a share count exceeding the Risk Engine's
  approved size;
- Never deploy or rotate below `INVESTMENT_POLICY.md` §4's cash minimum;
  and
- Label a Rotate distinctly from a Deploy whenever the capital source is a
  same-run release, so the pairing is auditable.

---

End of Document

TRX Trading OS v1.0
