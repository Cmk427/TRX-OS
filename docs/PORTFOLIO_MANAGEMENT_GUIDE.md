# TRX Trading OS
## PORTFOLIO_MANAGEMENT_GUIDE.md

```text
Document ID      : TRX-PMG-001
Document Name    : Portfolio Management Guide
Owner            : TRX project owner
Last Updated     : 2026-07-13
Version          : 1.0.0
Status           : Active
Classification   : Reference
Dependencies     : MASTER_DECISION_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   PORTFOLIO_OPTIMIZATION_ENGINE.md
                   PORTFOLIO_REBALANCING_ENGINE.md
Applies To       : Entire System (glossary only)
```

---

## 1. Purpose

Like `system/PARAMETER_REGISTRY.md` and `docs/AI_AGENT_IMPLEMENTATION_GUIDE.md`,
this is a **derived, non-authoritative glossary** — not a new rule source.
Every term below is defined once here in plain language, pointing at the
document that actually owns it. If this guide ever disagrees with an owning
document, **the owning document wins**, and the mismatch is a bug in this
guide.

It exists because this repository's own history shows engines drifting
toward inventing their own vocabulary for the same concept
(`docs/ROADMAP.md`'s v1.0 hardening notes on reconciling stray outcome
vocabulary — `ADD`, `ROTATE`, `BUY` — treated as if they were canonical
outcomes). This guide is the single place a future engine or reader can
check a term's meaning instead of re-deriving it independently.

---

## 2. Terms

**Hold** — an existing position's Master Decision outcome (State 16)
meaning no change; owned by `MASTER_DECISION_ENGINE.md` §9 and
`PORTFOLIO_ENGINE.md` §18. Not a synonym for "no opinion" — it is an
affirmative decision that the position remains as-is.

**Add** — deliberately not a distinct outcome value. Increasing an existing
position is expressed as Master Decision's own `EXECUTE` against that
ticker (`MASTER_DECISION_ENGINE.md` §9), which goes through the full
new-candidate pipeline like any other `EXECUTE` — it is not a
lower-scrutiny shortcut for an existing holding.

**Reduce** — an existing position's Master Decision outcome (State 16)
lowering exposure without closing it; owned by `MASTER_DECISION_ENGINE.md`
§9 and `PORTFOLIO_ENGINE.md` §18. `PORTFOLIO_OPTIMIZATION_ENGINE.md` §6
supplies the share count and capital released once this outcome is already
published — it never decides the Reduce itself.

**Exit** — an existing position's Master Decision outcome (State 16)
closing it entirely; owned by `MASTER_DECISION_ENGINE.md` §9 and
`PORTFOLIO_ENGINE.md` §18.

**Rotate** — deliberately not a single atomic action. A rotation (releasing
capital from one holding to fund another) is expressed as the paired `Exit`
(source ticker) and `Execute` (destination ticker), each having gone
through its own full pipeline review (`MASTER_DECISION_ENGINE.md` §9,
`PORTFOLIO_ENGINE.md` §18, `PORTFOLIO_OPTIMIZATION_ENGINE.md` §8). No
engine may publish "Rotate" as if it were one decision.

**Optimize** — the act of converting an already-published Master Decision
outcome into concrete target weight, share count, capital released/
required, and reallocation. Owned entirely by
`PORTFOLIO_OPTIMIZATION_ENGINE.md`, which runs every analysis cycle,
ticker-level, and never re-decides Hold/Reduce/Exit/Execute.

**Rebalance** — the periodic (e.g. monthly), top-down correction of
sector/theme allocation drift against a target allocation. Owned entirely
by `PORTFOLIO_REBALANCING_ENGINE.md`, which is distinct from Optimize in
both frequency (periodic vs. every run) and altitude (sector/theme vs.
ticker-level), and which never assigns a specific new ticker to fill an
underweight — that remains Scanner/Playbook/Committee's responsibility.

**Watch** — a candidate or position's Master Decision outcome (State 16)
meaning the setup may be valid later but no action is currently proposed;
owned by `MASTER_DECISION_ENGINE.md` §9.

**No Trade** — a completed, valid `SYSTEM.md` §6 primary outcome meaning no
new risk should be taken. Distinct from `NO REBALANCING ACTION`
(`PORTFOLIO_OPTIMIZATION_ENGINE.md` §9), which is a statement about that
engine's own output only, not a primary outcome.

---

## 3. Non-Goals

This guide does not grant any engine new authority, does not create a new
outcome value beyond what `MASTER_DECISION_ENGINE.md` §9 and `SYSTEM.md` §6
already define, and is not itself subject to `CONSTITUTION.md` §7A change
control the way a Critical-classification document is — a definition here
changes only when its owning document changes first.

---

End of Document

TRX Trading OS v1.0
