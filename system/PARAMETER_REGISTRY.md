# TRX Trading OS
## PARAMETER_REGISTRY.md

```text
Document ID      : TRX-PRM-001
Document Name    : Parameter Registry
Owner            : TRX project owner
Last Updated     : 2026-07-13
Version          : 1.5.0
Status           : Active
Classification   : Reference
Dependencies     : RISK_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   MARKET_ENGINE.md
                   SCANNER_ENGINE.md
                   PLAYBOOK_ENGINE.md
                   OPTIONS_ENGINE.md
                   COMMITTEE_ENGINE.md
                   RED_TEAM_ENGINE.md
                   MASTER_DECISION_ENGINE.md
                   EXECUTION_ENGINE.md
                   STATE_MACHINE.md
                   VERIFICATION_POLICY.md
Applies To       : Entire System (index only)
```

---

## 1. Purpose

Numeric thresholds currently live scattered across every engine — a
position-sizing limit here, a score band there. This registry is a single
place to look them up. It is **not** an independent source of authority: if
a number here ever disagrees with its owning document, **the owning
document wins**, the same relationship `docs/RESPONSIBILITY_MATRIX.md` and
`docs/DEPENDENCY_MAP.md` already have to the documents they index. Updating
a parameter still requires editing the owning engine document (and passing
`CONSTITUTION.md` §7 change control) — never editing this registry alone.

To remove any doubt for an agent that reads this file first (a real risk —
this file is short and easy to read before the engine it summarizes):

- This registry SHALL NOT introduce a rule, threshold, or default that does
  not already exist, verbatim, in its cited owning document.
- This registry SHALL NOT override, relax, or tighten any engine's
  parameter. It has no authority to do so even if edited to say otherwise.
- If this file is ever out of sync with its owning document, that is a bug
  in this file, not a second valid configuration to choose between.
- This registry lists **thresholds and bands** (which number carries which
  label) because those are lookup values, not computation. It does not
  restate **formulas, weight tables, or point-value mappings** — anywhere a
  parameter would require showing the arithmetic (a weighted sum, a
  per-flag deduction table, a vote-to-points mapping) this file names the
  owning section and stops there, so the arithmetic itself only ever
  exists once.

---

## 2. Risk Parameters — owned by `RISK_ENGINE.md`

| Parameter | Value | Section |
|---|---|---|
| Portfolio Heat bands | [0%, 20%) Very Low, [20%, 40%) Normal, [40%, 60%) Elevated, [60%, 80%) High, [80%, 100%] Critical — lower bound inclusive, upper bound exclusive except the top band | §7, formula §7A |
| Risk Score bands | 100 (exact) Minimal, 90–99 Low, 80–89 Controlled, 70–79 Elevated, <70 Reject | §24, formula §24A |
| Risk Score Layer 1 — Hard Reject Flags | Force score = 0, Reject, bypassing Layer 2 arithmetic — see `RISK_ENGINE.md` §24A for the exact flag list (not restated here to avoid a second copy going stale) | §24A |
| Risk Score Layer 2 — deduction model | Starts at 100, floored at 70 — see `RISK_ENGINE.md` §24A for the exact per-flag point values (not restated here to avoid a second copy going stale) | §24A |
| Drawdown bands | [0%, 5%) Normal, [5%, 10%) Caution, [10%, 15%) Defensive, [15%, 20%) Recovery Mode, [20%, 100%] Capital Protection Mode | §9 |
| Minimum Reward/Risk | 2:1 minimum (hard reject below this, see Layer 1 above), 3:1 preferred, 4:1+ exceptional | §19 |
| Position size, daily/weekly/monthly loss limits | Set per Account Risk Profile (Conservative/Balanced/Growth/Aggressive/Maximum Aggressive) — no single fixed number | §4, §10–12 |

## 3. Portfolio Construction Parameters — owned by `PORTFOLIO_ENGINE.md`

| Parameter | Value | Section |
|---|---|---|
| Max single stock concentration | 10% of portfolio value (default) | §9A |
| Max sector concentration | 30% of portfolio value (default) | §9A |
| Max theme concentration | 40% of portfolio value (default) | §9A |
| Portfolio Health Score bands | 90–100 Excellent, 80–89 Healthy, 70–79 Acceptable, 60–69 Weak, <60 Critical | §4 |

All three concentration defaults scale with Account Risk Profile the same
way `RISK_ENGINE.md` §4 limits do — tighter for Conservative, never looser.

## 4. Market Parameters — owned by `MARKET_ENGINE.md`

| Parameter | Value | Section |
|---|---|---|
| Market Score bands | 90+ Exceptional, 80+ Strong, 70+ Healthy, 60+ Weak, <60 Defensive | §15 |
| Risk Multiplier by Market Score | 90+ → 100%, 80+ → 75%, 70+ → 50%, 60+ → 25%, <60 → No New Position | §16 |
| Regime Transition Period | Triggered on any band crossing; exposure capped one tier more conservative until the band holds for 2 consecutive sessions | §15A |

## 5. Candidate / Setup Parameters

| Parameter | Value | Owning document |
|---|---|---|
| Candidate Quality Score bands | 90+ Elite, 80–89 Qualified, 70–79 Watchlist, <70 Exclude | `SCANNER_ENGINE.md` §5 |
| Playbook Match Score bands | 90+ Excellent, 80+ Qualified, 70+ Weak, <70 Reject; 85+ required for execution eligibility | `PLAYBOOK_LIBRARY.md` §2, `PLAYBOOK_ENGINE.md` §7–8 |
| Opportunity Score bands | 95–100 Exceptional, 90–94 Very Strong, 80–89 Strong, 70–79 Watchlist, <70 Reject | `DECISION_ENGINE.md` §Opportunity Score |
| Option Quality Score bands | 95+ Institutional Grade, 90+ Excellent, 85+ Strong, 80+ Qualified, <80 Reject | `OPTIONS_ENGINE.md` §6 |
| Underlying Candidate Quality Score gate for options | ≥ 80 required before Options Engine review (Scanner's score, not Decision Engine's Opportunity Score) | `OPTIONS_ENGINE.md` §4 |

## 6. Long Call Time Risk — owned by `OPTIONS_ENGINE.md` §8A

| Parameter | Value |
|---|---|
| Minimum DTE at entry | ≥ 30 days |
| Maximum intended holding period | ≤ 60% of entry DTE |
| Theta-loss mandatory review trigger | 20% of paid premium, thesis not yet invalid |
| IV-collapse exit trigger | IV Rank drops ≥15 points intra-session without a compensating favourable price move |
| Exit-or-rejustify threshold | DTE ≤ 5 (also governs new-entry buffer, `EXECUTION_ENGINE.md` §3E) |
| Profit-taking review trigger | Unrealized gain ≥ 100% of paid premium (§8B) |
| Loss-cut review trigger | Unrealized loss ≥ 50% of paid premium (§8B) |

## 7. Execution Parameters — owned by `EXECUTION_ENGINE.md`

| Parameter | Value | Section |
|---|---|---|
| Spread tiers → order type | ≤0.10% High liquidity (market ok); 0.10–0.50% Normal (limit); 0.50–1.5% Reduced (limit only); >1.5% Poor (limit/reduce/downgrade) | §3A |
| Liquidity cap | ≤ 10% of ADV (equity) or ≤ 10% of OI (option) | §3B |
| Minimum DTE for new options entry | > 5 calendar days unless explicitly justified | §3E |

## 8. Decision Parameters — owned by `MASTER_DECISION_ENGINE.md`

| Parameter | Value | Section |
|---|---|---|
| Confidence Model weights | Seven inputs, weighted — see `MASTER_DECISION_ENGINE.md` §7's weight table for the exact per-input percentages (not restated here to avoid a second copy going stale) | §7 |
| WCS-to-Confidence bands (the only rule — there is no separate "minimum threshold"; an earlier unscoped "<70 caps at Low" rule was removed because it contradicted these bands, e.g. WCS=55 read as both "Low" and "Very Low") | 90–100 Very High, 80–89 High, 70–79 Medium, 60–69 Low, <60 Very Low | §7 |
| Missing-value caps (can only lower a reading below what this table alone gives, never raise it) | Gate-linked input UNKNOWN → capped at Low; non-gate-linked input UNKNOWN → drop one level from table reading | §7 |
| Uncertainty tiers | Low, Medium, High, Critical (separate axis from Confidence) | §14A |
| Verification Confidence bands | All V1 → 100, worst V2 → 90, worst V3 → 75, worst V4 → 60, worst V5 (non-critical) → 40 | `VERIFICATION_POLICY.md` §4A |
| Resilience / Red Team Score bands | 100 (exact) Exceptional, 90–99 Strong, 80–89 Acceptable, 70–79 Weak, <70 Reject | `RED_TEAM_ENGINE.md` §19 |
| Resilience Score deduction model | Starts at 100, floored at 0 — see `RED_TEAM_ENGINE.md` §19 for the exact per-category deduction values (not restated here to avoid a second copy going stale) | `RED_TEAM_ENGINE.md` §19 |

## 9. Committee Parameters — owned by `COMMITTEE_ENGINE.md`

| Parameter | Value | Section |
|---|---|---|
| Role weights | Six roles, weighted — see `COMMITTEE_ENGINE.md` §6 for the exact per-role percentages (not restated here to avoid a second copy going stale) | §6 |
| Vote-to-points mapping | Six vote options, each a fixed point value — see `COMMITTEE_ENGINE.md` §5A for the exact mapping (not restated here to avoid a second copy going stale) | §5A |
| Consensus Score bands | 95+ Unanimous, 90+ Strong, 80+ Moderate, 70+ Weak, <70 No Consensus | §10 |
| Risk Manager forced-escalation trigger | Risk Manager votes REDUCE or REJECT | §12 |

## 10. Workflow Parameters — owned by `STATE_MACHINE.md`

| Parameter | Value | Section |
|---|---|---|
| Revision cycle limit | Max 2 full-cycle revisions per run; 3rd trigger → `SYSTEM REVIEW REQUIRED` | §6A |

---

## 11. Consistency Rule

Any change to a number listed above SHALL be made in its owning document
first; a follow-up edit to this registry is a mechanical sync, not a
separate decision. `docs/DEPENDENCY_MAP.md` §3A tracks this document as
purely downstream/observational — it must never become a dependency of an
engine.

---

End of Document

TRX Trading OS v1.0
