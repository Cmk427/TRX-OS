# TRX Trading OS
## PARAMETER_REGISTRY.md

```text
Document ID      : TRX-PRM-001
Document Name    : Parameter Registry
Version          : 1.0.0
Status           : Active
Classification   : Reference
Dependencies     : RISK_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   MARKET_ENGINE.md
                   SCANNER_ENGINE.md
                   PLAYBOOK_ENGINE.md
                   OPTIONS_ENGINE.md
                   COMMITTEE_ENGINE.md
                   MASTER_DECISION_ENGINE.md
                   EXECUTION_ENGINE.md
                   STATE_MACHINE.md
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

---

## 2. Risk Parameters — owned by `RISK_ENGINE.md`

| Parameter | Value | Section |
|---|---|---|
| Portfolio Heat bands | 0–20% Very Low, 20–40% Normal, 40–60% Elevated, 60–80% High, 80%+ Critical | §7, formula §7A |
| Risk Score bands | 100 Minimal, 90 Low, 80 Controlled, 70 Elevated, <70 Reject | §24, formula §24A |
| Risk Score deduction points | R/R shortfall −25, Elevated Heat −10, Critical Heat −25, High correlation −10, High event risk −10, Extreme event risk −20, Poor liquidity −10, Active drawdown mode −15, Non-structural stop −20 | §24A |
| Drawdown bands | 0–5% Normal, 5–10% Caution, 10–15% Defensive, 15–20% Recovery Mode, >20% Capital Protection Mode | §9 |
| Minimum Reward/Risk | 2:1 minimum, 3:1 preferred, 4:1+ exceptional; below minimum → reject | §19 |
| Position size, daily/weekly/monthly loss limits | Set per Account Risk Profile (Conservative/Balanced/Growth/Aggressive/Maximum Aggressive) — no single fixed number | §4, §10–12 |

## 3. Portfolio Construction Parameters — owned by `PORTFOLIO_ENGINE.md`

| Parameter | Value | Section |
|---|---|---|
| Max single stock concentration | 10% of portfolio value (default) | §9A |
| Max sector concentration | 30% of portfolio value (default) | §9A |
| Max theme concentration | 40% of portfolio value (default) | §9A |
| Portfolio Health Score bands | 90–100 Excellent, 80–89 Healthy, 70–79 Acceptable, ... | §4 |

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
| Opportunity Score bands | 95–100 Exceptional, 90–94 Very Strong, 80–89 Strong, ... | `DECISION_ENGINE.md` §Opportunity Score |
| Option Quality Score bands | 95+ Institutional Grade, 90+ Excellent, 85+ Strong, 80+ Qualified, <80 Reject | `OPTIONS_ENGINE.md` §6 |
| Underlying candidate score gate for options | ≥ 80 required before Options Engine review | `OPTIONS_ENGINE.md` §4 |

## 6. Long Call Time Risk — owned by `OPTIONS_ENGINE.md` §8A

| Parameter | Value |
|---|---|
| Minimum DTE at entry | ≥ 30 days |
| Maximum intended holding period | ≤ 60% of entry DTE |
| Theta-loss mandatory review trigger | 20% of paid premium, thesis not yet invalid |
| Exit-or-rejustify threshold | DTE ≤ 5 (also governs new-entry buffer, `EXECUTION_ENGINE.md` §3E) |

## 7. Execution Parameters — owned by `EXECUTION_ENGINE.md`

| Parameter | Value | Section |
|---|---|---|
| Spread tiers → order type | ≤0.10% High liquidity (market ok); 0.10–0.50% Normal (limit); 0.50–1.5% Reduced (limit only); >1.5% Poor (limit/reduce/downgrade) | §3A |
| Liquidity cap | ≤ 10% of ADV (equity) or ≤ 10% of OI (option) | §3B |
| Minimum DTE for new options entry | > 5 calendar days unless explicitly justified | §3E |

## 8. Decision Parameters — owned by `MASTER_DECISION_ENGINE.md`

| Parameter | Value | Section |
|---|---|---|
| Confidence Model weights | Verification 20%, Risk 20%, Market 15%, Portfolio 15%, Opportunity 15%, Committee 10%, Red Team 5% | §7 |
| WCS minimum threshold | < 70 caps Confidence at Low regardless of gate results | §7 |
| Confidence bands | Very High, High, Medium, Low, Very Low | §14 |
| Uncertainty tiers | Low, Medium, High, Critical (separate axis from Confidence) | §14A |

## 9. Committee Parameters — owned by `COMMITTEE_ENGINE.md`

| Parameter | Value | Section |
|---|---|---|
| Role weights | Market Strategist 25%, Risk Manager 25%, Portfolio Manager 20%, Technical Analyst 15%, Execution Specialist 10%, Options Specialist 5% | §6 |
| Vote-to-points mapping | STRONG BUY 100, BUY 80, WATCH 60, PASS 40, REDUCE 20, REJECT 0 | §5A |
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
