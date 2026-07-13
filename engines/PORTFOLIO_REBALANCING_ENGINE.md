# TRX Trading OS
## PORTFOLIO_REBALANCING_ENGINE.md

```text
Document ID      : TRX-PRB-001
Document Name    : Portfolio Rebalancing Engine
Owner            : TRX project owner
Last Updated     : 2026-07-13
Version          : 1.0.0
Status           : Active
Classification   : Critical
Dependencies     : STATE_MACHINE.md
                   PORTFOLIO_ENGINE.md
                   PORTFOLIO_OPTIMIZATION_ENGINE.md
Applies To       : Periodic sector/theme allocation review (no State Machine position)
```

---

## 1. Purpose and Boundary

`PORTFOLIO_OPTIMIZATION_ENGINE.md` runs every analysis cycle, ticker-level,
downstream of one specific Master Decision run. This engine is different in
both frequency and altitude: it runs **periodically** (e.g., monthly, not
daily) and looks at the portfolio from the **top down** — sector and theme
allocation drift — rather than reacting to a single run's candidate
decisions.

Like `POSITION_MANAGEMENT_ENGINE.md`, this engine is **not** a new pipeline
state (`STATE_MACHINE.md` §2B). A scheduled review starts a **new analysis
run at State 01/02**, and this engine's gap analysis (§3) feeds that run's
Portfolio Review (06) and Portfolio Optimization (17) as input. It does not
bypass Scanner, Committee, or Risk for any new capital deployment, and it
does not create a third authority over "what a position should be" —
alongside Master Decision (ticker-level outcome) and Portfolio Optimization
(ticker-level sizing), this engine only ever speaks at the sector/theme
level, never overriding either.

This engine holds **neither Constraint Authority nor Publication
Authority**. It never itself publishes a ticker-level decision — a
sector/theme gap becomes an actual trade only after that ticker goes
through the full pipeline (new capital) or through Portfolio Optimization
Engine (an existing position) in the run this engine's trigger starts.

---

## 2. Required Inputs

- Portfolio Engine (06) — current sector and theme concentration
  (`PORTFOLIO_ENGINE.md` §9A/§11).
- A target allocation, either user-supplied or carried forward from the
  most recent prior rebalancing review (never invented without a stated
  source).

---

## 3. Gap Analysis

```text
Current Allocation
  ↓
Target Allocation
  ↓
Gap Analysis
  ↓
Rebalance Plan
```

Example:

```text
Sector/Theme   Current   Target
AI             45%       35%
Cash            0%        8%
Defense         5%       10%
```

Every gap above the account's Risk-Profile-adjusted tolerance
(`PORTFOLIO_ENGINE.md` §9A) SHALL be listed, whether the gap is an
overweight or an underweight — this engine reports drift in both
directions, not only positions to trim.

---

## 4. Rebalance Plan

The Rebalance Plan states, per sector/theme with a material gap:

- Which specific tickers contribute to the overweight (sourced from
  Portfolio Engine's current holdings).
- Whether closing the gap is better achieved by reducing existing
  overweight positions (routed through `PORTFOLIO_OPTIMIZATION_ENGINE.md`
  in the new run) or by raising cash and waiting for a qualified candidate
  in the underweight sector/theme (routed through the full Scanner/
  Committee/Risk pipeline — this engine never assigns a specific new
  ticker itself).

This engine SHALL NOT propose a specific new position to fill an
underweight sector/theme — that remains Scanner Engine's and Playbook
Engine's responsibility, evaluated on its own merits rather than assigned
top-down to satisfy an allocation target.

---

## 5. Failure Conditions

If current allocation data is incomplete, or no target allocation exists
(user-supplied or carried forward), this engine SHALL NOT compute a gap. It
SHALL report that the periodic review could not run and state exactly what
input is missing, rather than substituting an assumed target.

---

## 6. Success Criteria

A successful periodic review SHALL:

- State every material sector/theme gap, over- and under-weight alike;
- Never assign a specific new ticker to fill an underweight; and
- Route every proposed action through the existing pipeline (Portfolio
  Optimization Engine for existing positions, the full candidate pipeline
  for new capital) rather than acting as a parallel decision path.

---

End of Document

TRX Trading OS v1.0
