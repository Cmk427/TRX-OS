# TRX Trading OS
## POSITION_MANAGEMENT_ENGINE.md

```text
Document ID      : TRX-PME-001
Document Name    : Position Management Engine
Owner            : TRX project owner
Last Updated     : 2026-07-14
Version          : 1.1.0
Status           : Active
Classification   : Critical
Dependencies     : STATE_MACHINE.md
                   PORTFOLIO_ENGINE.md
                   RISK_ENGINE.md
                   OPTIONS_ENGINE.md
                   COMMITTEE_ENGINE.md
                   PORTFOLIO_OPTIMIZATION_ENGINE.md
Applies To       : Ongoing, trigger-based position review (no State Machine position)
```

---

## 1. Purpose and Boundary

Every other engine in this repository runs *within* one analysis pass of
`STATE_MACHINE.md`'s pipeline. This engine does not — it exists because a
portfolio does not stand still between analysis runs: a position can gain
20% or lose 10%, a company can report earnings, or an option's implied
volatility can spike, all without the human having asked TRX to look again.

This engine is **not** a new pipeline state. Per `STATE_MACHINE.md` §2B, a
fired trigger from this document starts a **new analysis run at State
01/02**, with the trigger condition and affected ticker(s) supplied as
input to that new run — it does not resume, patch, or bypass any state of a
run already in progress, and it is not a `STATE_MACHINE.md` §6 Recovery
Rules transition (§6 corrects a defect *within* an in-progress run; this
engine's triggers begin a run that has not started yet).

Consequently, this engine holds **neither Constraint Authority nor
Publication Authority** (`CONSTITUTION.md` §4A):

- It never itself publishes `HOLD`/`REDUCE`/`EXIT`/`WATCH` for a position —
  that remains Master Decision Engine's Publication Authority, exercised by
  the new run this engine's trigger starts.
- It never skips Verification (State 04), Portfolio Review (State 06), or
  any other required state of that new run.
- Its only output is a **trigger record**: which rule fired, for which
  ticker, and why — never a decision.

---

## 2. Required Inputs

- Portfolio Engine (06/08) — current position weight, unrealized gain/loss,
  entry price, entry date.
- Risk Engine (07/15) — the position's recorded Invalidation Condition
  (`PORTFOLIO_ENGINE.md` §8A) and stop.
- Options Engine (11) — for option positions only, current IV Rank and its
  own existing time/profit/loss triggers (§8A/§8B), which this engine does
  not duplicate (§3A).
- Committee Engine (13) — the original thesis and role votes recorded for
  a held position, for the new run's Committee stage to re-evaluate against
  a Guidance Cut (§3A).
- A market calendar for scheduled events (earnings dates, guidance
  updates).

---

## 3. Trigger Matrix

Quick-reference form — every trigger, in one place, mapped to its action.
§3A below gives the exact condition and disambiguation for each:

| Trigger | Action |
|---|---|
| +20% Gain | Review Trim |
| -10% Loss | Review Thesis |
| Earnings | Mandatory Review |
| Guidance Cut | Re-run Committee |
| IV Spike | Options Review |

This is what makes Position Management genuinely event-driven rather than
a single vague "keep an eye on it" rule — every row above is a specific,
checkable condition with a specific, named next action.

## 3A. Trigger Table (Conditions and Disambiguation)

These are new, equity-level, wall-clock/price triggers — checked against
every existing numeric threshold in `PARAMETER_REGISTRY.md` to confirm none
already claims this ground before being added here. Every "Action" below
still resolves to the same mechanism (§1): a **new analysis run at State
01/02**, never a mid-pipeline resume — "Re-run Committee" means the new
run's Committee stage (13) is where the revised guidance must be
specifically re-evaluated, not that this engine jumps into Committee
directly.

| Trigger | Condition | Action |
|---|---|---|
| Gain Review | Unrealized gain > 20% of cost basis | Mandatory Review-Trim — start a new run to reassess target weight via `PORTFOLIO_OPTIMIZATION_ENGINE.md` |
| Loss Review | Unrealized loss > 10% of cost basis | Mandatory Review-Thesis — start a new run; check the recorded Invalidation Condition first (`PORTFOLIO_ENGINE.md` §8A) before any other analysis |
| Earnings Review | A scheduled earnings date for a held position falls within the next trading session | Mandatory Review — start a new run before the event, not after |
| Guidance Cut Review | The company lowers forward guidance for a held position | Mandatory Re-run Committee — start a new run; Committee (13) SHALL explicitly re-evaluate the original thesis against the revised guidance, not merely note the cut occurred |
| IV Spike Review | Implied volatility rises sharply intraday for a held option position | Option Review — start a new run scoped to that option position |

**Guidance Cut is distinct from Earnings Review.** Earnings Review fires on
the scheduled event itself, regardless of content; Guidance Cut fires on
the specific content of a lowered forward guidance, whether announced at
an earnings event or separately (e.g. a mid-quarter update) — the two can
fire independently or together for the same position.

**IV Spike is distinct from Options Engine's existing IV-collapse trigger.**
`OPTIONS_ENGINE.md` §8A already defines an **IV-collapse** exit trigger (IV
Rank drops ≥15 points intra-session without a compensating price move) for
managing an *open* Long Call. IV **Spike** here is the opposite direction —
a *rise* in IV — and is a distinct, complementary condition; this engine
does not redefine or restate the existing collapse trigger.

**Gain/Loss triggers are new and equity-level**, distinct from
`OPTIONS_ENGINE.md` §8B's premium-based profit-taking (≥100% of paid
premium) and loss-cutting (≥50% of paid premium) triggers, which apply only
to option premium, not to an equity position's cost basis. They are also
distinct from `PORTFOLIO_ENGINE.md` §6's qualitative Position Health Score
bands — this engine's triggers are boolean rule fires ("review required:
yes/no, and why"), not a competing 0–100 score.

---

## 4. Trigger Record (Output)

Every fired trigger SHALL record:

```text
Ticker                :
Trigger               : Gain Review / Loss Review / Earnings Review /
                         Guidance Cut Review / IV Spike Review
Condition observed     :   (e.g., "unrealized gain 23.4% > 20% threshold")
Timestamp              :
Action                 :   Start new analysis run at State 01/02
```

This record is the entire output of this engine. It is not itself a
Decision Snapshot (`DECISION_SNAPSHOT_POLICY.md` §2) — the new run it starts
produces that.

---

## 5. Failure Conditions

If position data (entry price, cost basis, current price) required to
evaluate a trigger is incomplete, this engine SHALL flag the affected
ticker as **not evaluated** rather than silently skipping it or assuming
the trigger did not fire. A silently-skipped review is indistinguishable
from a position that genuinely has nothing to review, which defeats this
engine's purpose.

---

## 6. Success Criteria

A successful pass SHALL:

- Evaluate every open position against every trigger in §3/§3A, with no
  position silently skipped;
- Never publish a position decision itself; and
- Produce a trigger record legible enough that a human can see exactly why
  a new run was started.

---

End of Document

TRX Trading OS v1.0
