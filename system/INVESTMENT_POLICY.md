# TRX Trading OS
## INVESTMENT_POLICY.md

```text
Document ID      : TRX-INV-001
Document Name    : Investment Policy
Owner            : TRX project owner
Last Updated     : 2026-07-14
Version          : 1.0.0
Status           : Active
Classification   : Critical
Dependencies     : CORE_PRINCIPLES.md
                   CONSTITUTION.md
Applies To       : Portfolio construction and capital-allocation limits (entire system)
```

---

## 1. Purpose

Before this document existed, portfolio-construction numbers were defined
independently inside whichever engine happened to need them first —
`PORTFOLIO_ENGINE.md` §9A owned the concentration limits,
`PORTFOLIO_OPTIMIZATION_ENGINE.md` owned its own preferred-target and cash
numbers, and the capital-priority order was restated near-identically in
two separate documents. That pattern is exactly what
`docs/ARCHITECTURE.md`'s Release Version Matrix has repeatedly had to catch
and correct as a propagation gap. This document ends it: it is the single
place every portfolio-construction and capital-allocation policy number
lives. Every engine below references it; none re-defines it.

This document is **foundational governance**, the same tier as
`CORE_PRINCIPLES.md` and `DATA_SOURCE_POLICY.md` — it has no dependency on
any engine, and per `docs/DEPENDENCY_MAP.md`'s forward-dependency rule, no
engine may ever become a dependency of this document. Engines depend on it;
it never depends on them.

This document does not compute anything. It states limits; the engines
that already own the relevant computation (`PORTFOLIO_ENGINE.md`,
`PORTFOLIO_OPTIMIZATION_ENGINE.md`, `RISK_ENGINE.md`) still own how those
limits are checked and enforced. Where this document would otherwise
duplicate a formula or a classification band another document already
owns, it cross-references that document instead of restating the
arithmetic — the same discipline `PARAMETER_REGISTRY.md` already applies.

---

## 2. Position Concentration Limits

These are hard ceilings. A candidate or position breaching one of these is
rejected or flagged before Risk Engine sizing is even attempted
(`PORTFOLIO_ENGINE.md` §17 New Position Compatibility remains the
enforcement mechanism; this document only states the number it enforces):

| Limit | Default |
|---|---|
| Maximum Single Stock concentration | 10% of portfolio value |
| Maximum Sector concentration | 30% of portfolio value |
| Maximum Theme concentration | 40% of portfolio value |
| Maximum Small-Cap exposure | 25% of portfolio value |
| Maximum Options exposure (aggregate Premium at Risk) | 15% of portfolio value |

Small-Cap and Options exposure are genuinely new parameters — no prior
document in this repository defined a numeric ceiling for either before
this one.

---

## 3. Preferred Target and Advisory Band

Distinct from the hard ceilings in §2, these are softer targets used only
once `PORTFOLIO_OPTIMIZATION_ENGINE.md` already has a `REDUCE`/`EXIT`/
`EXECUTE` Decision to size (echoed from Master Decision, never computed
here or there independently):

| Parameter | Default |
|---|---|
| Preferred single-stock target | 6% of portfolio value |
| Advisory review band | 8%–10% of portfolio value (approaching, never breaching, the §2 hard cap) |

---

## 4. Cash Policy

| Parameter | Default |
|---|---|
| Minimum cash reserve | 5% of portfolio value |
| Preferred cash reserve | 10% of portfolio value |

---

## 5. Portfolio Heat Ceiling

This document does not define a second Portfolio Heat number. The enforced
ceiling is `RISK_ENGINE.md` §7/§24A's existing Critical-band boundary
(Heat ≥ 80% is a Layer 1 Hard Reject) — restating it here as a separate
figure would create exactly the two-copies-going-stale risk this document
exists to prevent. This section exists only so a reader looking for "the
portfolio policy limits" in one place finds a pointer to where the real
number and its formula live.

---

## 6. Capital Priority Order

The single canonical statement of this system's capital-priority ordering,
previously restated near-identically in both `PORTFOLIO_ENGINE.md` §19 and
`PORTFOLIO_OPTIMIZATION_ENGINE.md` §11 — both now cross-reference this
section instead:

```text
Protect Capital
  ↓
Reduce Weak Positions
  ↓
Strengthen Strong Positions
  ↓
Increase Cash (if below §4's minimum)
  ↓
Deploy New Capital
```

This is a portfolio-action sequencing rule, distinct from
`RISK_ENGINE.md` §2's Risk Hierarchy (Capital Preservation → Portfolio
Survival → Risk Control → Capital Growth → Profit Maximization), which is a
risk-philosophy ordering owned by, and staying in, that document — the two
are related but not the same list, and are not merged here.

---

## 7. Position Adjustment Principles

Mostly cross-references to rules already established elsewhere — stated
together here so the philosophy reads as one coherent policy rather than
requiring a reader to assemble it from several engines:

- **Adding to a position is not a shortcut.** Increasing an existing
  holding is Master Decision's own `EXECUTE` against that ticker
  (`MASTER_DECISION_ENGINE.md` §9), which goes through the full
  new-candidate pipeline like any other `EXECUTE` — never a lower-scrutiny
  path just because a position already exists.
- **No addition may cross a §2 hard cap.** This is checked at
  `PORTFOLIO_ENGINE.md` §17 before Risk Engine sizing, not after.
- **A published `REDUCE` targets the §3 preferred target by default**, not
  merely "reduce until below the hard cap" — `PORTFOLIO_OPTIMIZATION_ENGINE.md`
  §7 computes this once the Decision already exists.
- **Rotation is always two decisions, never one.** Releasing capital from
  one holding to fund another is the paired `EXIT` (source) and `EXECUTE`
  (destination), each independently reviewed — never a single atomic
  action (`docs/PORTFOLIO_MANAGEMENT_GUIDE.md` §2, "Rotate").

---

## 8. Account Risk Profile Modifier

Every limit in §2–§4 above scales with the account's Risk Profile
(`RISK_ENGINE.md` §4: Conservative / Balanced / Growth / Aggressive /
Maximum Aggressive) the same way Position Size and Portfolio Exposure
limits already do there — a Conservative profile SHALL use tighter limits,
never looser ones, and any profile-specific override SHALL be recorded
explicitly rather than silently assumed. The profile categories themselves
remain owned by `RISK_ENGINE.md` §4; this document only states that its own
numbers are subject to the same scaling rule.

---

## 9. Consuming Documents

The following documents reference this policy and SHALL NOT restate its
numbers independently: `PORTFOLIO_ENGINE.md` (§9A, §19),
`PORTFOLIO_OPTIMIZATION_ENGINE.md` (§7A, §7B, §11),
`CAPITAL_ALLOCATION_ENGINE.md`, `POSITION_MANAGEMENT_ENGINE.md`,
`PORTFOLIO_REBALANCING_ENGINE.md`. `PARAMETER_REGISTRY.md` indexes this
document the same way it indexes every other owning engine — as a
convenience lookup, never a second authority.

---

## 10. Change Control

This is a Critical-classification document. A change to any number above
SHALL follow `CONSTITUTION.md` §7A's change checklist in full — rationale,
affected-file list, `docs/ARCHITECTURE.md`/`docs/DEPENDENCY_MAP.md`
consistency checks, and a version bump — the same as changing a number in
any engine document. No consuming document may locally override a number
here without recording the override explicitly (see §8).

---

End of Document

TRX Trading OS v1.0
