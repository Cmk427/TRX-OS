# TRX Trading OS
## ENGINE_INTERFACE_CONTRACT.md

```text
Document ID      : TRX-EIC-001
Document Name    : Engine Interface Contract
Version          : 1.5.0
Status           : Active
Classification   : Reference
Dependencies     : STATE_MACHINE.md
                   MARKET_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   RISK_ENGINE.md
                   SCANNER_ENGINE.md
                   PLAYBOOK_ENGINE.md
                   OPTIONS_ENGINE.md
                   DECISION_ENGINE.md
                   COMMITTEE_ENGINE.md
                   RED_TEAM_ENGINE.md
                   MASTER_DECISION_ENGINE.md
                   PORTFOLIO_OPTIMIZATION_ENGINE.md
                   EXECUTION_ENGINE.md
Applies To       : Engine-to-engine data exchange
```

---

## 1. Purpose

Every engine document defines its responsibility and rules in prose. None of
them defines a structured data shape for what one engine hands to the next.
That is fine for a human reading Markdown end to end, but it will not
compose if TRX is ever wired into an actual multi-agent or tool pipeline
(see `workflows/WORKFLOWS.md`) — each engine would invent its own ad hoc
fields and nothing downstream could rely on them.

This document is the **interchange shape**, not a new rule source. It
changes no engine's authority, veto power, or decision logic — it only fixes
the field names and types each engine already establishes in prose so a
future implementation has one schema to target. If this document and an
engine's prose ever disagree on what a field means, **the engine document
prevails**; this document only prevails on naming/shape when the engine
document is silent on structure.

All timestamps are ISO 8601 UTC. All scores are 0–100 unless stated
otherwise. Any field whose value is unavailable SHALL be the literal string
`"UNKNOWN"`, never omitted and never a guessed default — consistent with
`RISK_ENGINE.md` §26A and `VERIFICATION_POLICY.md`.

---

## 1A. Schema Conventions

These conventions apply to every JSON block in this document (§2–§13) and
to any future engine added to the pipeline. They exist so an agent
validating against this schema has a fixed rule for every ambiguity a bare
example (`"confidence": 0.85`) would otherwise leave open.

- **`schema_version`**: every top-level object SHALL include
  `"schema_version": "1.0.0"` (this document's own `Version` header,
  copied verbatim) alongside its `engine_version` field. `engine_version`
  identifies which version of the *engine's rules* produced the output;
  `schema_version` identifies which version of *this interchange shape* the
  JSON conforms to. The two change independently — a rule change bumps
  `engine_version`; a field added/removed/retyped here bumps
  `schema_version`.
- **Required vs. optional**: every field shown in every example block is
  **required** unless its type annotation ends in `| null` or is explicitly
  marked `(optional)`. A required field missing from an actual payload is a
  schema violation, not an omission to be inferred — it fails validation
  the same way an out-of-enum value does.
- **Type notation**: `"number"` means a JSON number, never a numeric
  string. `"string"` means free text. An explicit enum (`"A | B | C"`) means
  the value SHALL be exactly one of the listed literals — no close
  synonym, no casing variant, no value outside the list. `"UNKNOWN"` is a
  reserved literal, not a member of any enum unless the enum explicitly
  lists it — a field that is a fixed enum (e.g. `final_outcome`) and
  cannot be computed SHALL still surface this as a validation failure
  (below), not as a silent extra enum member.
- **Scale**: unless stated otherwise, every numeric score is 0–100 on a
  shared scale — never a 0–1 fraction, never a percentage string. This is
  what makes `WCS = Σ(weight_i × score_i)` in
  `MASTER_DECISION_ENGINE.md` §7 well-defined: every input it sums already
  arrived pre-normalised to this same 0–100 scale by its owning engine (see
  §7's weight table for exactly which document defines each one).
- **Validation failure → `failure_state`**: if a payload cannot be produced
  because a required upstream value is itself `UNKNOWN`, missing, or
  contradictory, the engine's response SHALL be an object of the shape
  `{"engine": "...", "engine_version": "...", "schema_version": "1.0.0",
  "failure_state": {"category": "DATA_FAILURE | MODEL_FAILURE |
  CONFLICT_FAILURE | RISK_REJECTION | EXECUTION_INVALID | SYSTEM_ERROR",
  "reason": "string"}}` instead of the engine's normal output shape —
  the category values are exactly `FAILURE_TAXONOMY.md` §2's six
  categories. A consumer SHALL check for `failure_state` before reading any
  engine-specific field; its presence means the rest of the normal payload
  does not exist for this call, not that it exists with placeholder values.

---

## 2. Market Engine → downstream

```json
{
  "engine": "MARKET_ENGINE",
  "engine_version": "1.0.0",
  "schema_version": "1.0.0",
  "as_of": "2026-07-10T14:35:00Z",
  "market_score": 84,
  "regime": "STRONG RISK ON | RISK ON | NEUTRAL | RISK OFF | STRONG RISK OFF",
  "trend": "Strong Bull | Bull | Sideways | Bear | Strong Bear",
  "breadth": "Very Strong | Strong | Neutral | Weak | Very Weak",
  "volatility": "LOW | NORMAL | HIGH | EXTREME",
  "macro_events": ["string"],
  "sector_rotation": [
    {"sector": "string", "momentum_score": "number", "relative_strength": "number", "capital_flow_rating": "string"}
  ],
  "recommended_exposure_pct": 75,
  "regime_transition": {
    "detected": "boolean (band crossed vs. prior session, per §15A)",
    "transition_period_active": "boolean",
    "exposure_cap_pct": "number | null (one Risk Multiplier tier more conservative while active)",
    "playbooks_requiring_reverification": ["string"]
  },
  "evidence_refs": ["verification-ledger-row-id"]
}
```

There is no separate `confidence` field here — `market_score` already is the
one number `MARKET_ENGINE.md` §14/§15 calls "Market Confidence"/"Market
Score" (one number, two names, per that document). A second `confidence`
field would either duplicate `market_score` or invent a value this engine's
prose never defines; neither is acceptable per §1's naming/shape-only rule.

## 3. Portfolio Engine → downstream

```json
{
  "engine": "PORTFOLIO_ENGINE",
  "engine_version": "1.0.0",
  "schema_version": "1.0.0",
  "as_of": "ISO8601",
  "portfolio_health_score": 88,
  "cash_pct": 31,
  "buying_power": "number",
  "largest_position": {"ticker": "string", "pct": "number"},
  "sector_concentration": {"<sector-name>": "number (pct of portfolio value)"},
  "theme_concentration": {"<theme-name>": "number (pct of portfolio value)"},
  "construction_limits": {
    "single_stock_max_pct": 10,
    "sector_max_pct": 30,
    "theme_max_pct": 40,
    "breached": ["string (which limit, if any, per §9A)"]
  },
  "correlation_flags": ["string"],
  "constraints": ["string"]
}
```

## 4. Risk Engine → downstream (Preliminary and Final Gate)

```json
{
  "engine": "RISK_ENGINE",
  "engine_version": "1.0.0",
  "schema_version": "1.0.0",
  "gate": "PRELIMINARY | FINAL",
  "portfolio_heat_pct": "number | UNKNOWN",
  "position_heat_score": "number | UNKNOWN",
  "risk_score": "number | UNKNOWN",
  "risk_score_deductions": [{"flag": "string", "points": "number"}],
  "gate_result": "PASS | REJECT | RESTRICT",
  "veto": "boolean",
  "veto_reason": "string | null"
}
```

`risk_score: "UNKNOWN"` SHALL always co-occur with `gate_result: "REJECT"`
per `RISK_ENGINE.md` §26A — a schema validator MAY treat the opposite
combination as a contract violation.

## 5. Scanner Engine → Playbook Engine

```json
{
  "engine": "SCANNER_ENGINE",
  "engine_version": "1.0.0",
  "schema_version": "1.0.0",
  "candidates": [
    {
      "ticker": "string",
      "candidate_quality_score": "number",
      "setup_quality": "number (0-100 component contribution, per §5)",
      "portfolio_fit": "number (0-100 component contribution, per §5)",
      "catalyst_state": "V1 | V2 | V3 | V4 | V5",
      "exclusion_reason": "string | null"
    }
  ]
}
```

`setup_quality` and `portfolio_fit` are numeric component contributions to
`candidate_quality_score` (§5's weighted table), not separately labelled
bands — `SCANNER_ENGINE.md` defines no qualitative scale for either beyond
the composite score itself.

## 6. Playbook Engine → Options Engine / Decision Engine

```json
{
  "engine": "PLAYBOOK_ENGINE",
  "engine_version": "1.0.0",
  "schema_version": "1.0.0",
  "playbook_library_version": "1.0.0",
  "ticker": "string",
  "playbook_id": "PB-001",
  "match_score": "number",
  "no_trade": "boolean",
  "no_trade_reason": "string | null"
}
```

## 7. Options Engine → downstream (conditional)

```json
{
  "engine": "OPTIONS_ENGINE",
  "engine_version": "1.0.0",
  "schema_version": "1.0.0",
  "applicable": "boolean",
  "contract": {"strike": "number", "expiry": "date", "premium": "number"},
  "greeks": {"delta": "number", "gamma": "number", "theta": "number", "vega": "number"},
  "iv_rank": "number",
  "liquidity_score": "number",
  "option_quality_score": "number",
  "named_risk_factors": {
    "theta_decay_ok": "boolean",
    "iv_crush_exposure": "string | null (null when no scheduled IV-moving event falls within the holding window, per §5A)",
    "gamma_risk_flag": "boolean",
    "expiration_risk": {"days_to_expiry": "number", "holding_period_days": "number"},
    "assignment_risk": "not_applicable_long_call_buyer (fixed literal — this engine only analyzes bought Long Calls, per §1; assignment risk applies only to option sellers)"
  },
  "time_risk_rules": {
    "min_dte_at_entry_ok": "boolean (DTE >= 30, per §8A)",
    "holding_period_within_cap": "boolean (holding_period_days <= 0.6 * days_to_expiry)",
    "theta_loss_review_triggered": "boolean (cumulative theta loss >= 20% of paid premium)",
    "iv_collapse_triggered": "boolean (IV Rank dropped >= 15 points intra-session without compensating price move, per §8A)",
    "exit_or_rejustify_required": "boolean (current DTE <= 5)"
  },
  "profit_loss_review_rules": {
    "profit_take_review_triggered": "boolean (unrealized gain >= 100% of paid premium, per §8B)",
    "loss_cut_review_triggered": "boolean (unrealized loss >= 50% of paid premium, per §8B)"
  },
  "max_risk_premium": "number"
}
```

## 8. Decision Engine → Committee Engine

```json
{
  "engine": "DECISION_ENGINE",
  "engine_version": "1.0.0",
  "schema_version": "1.0.0",
  "ticker": "string",
  "opportunity_score": "number",
  "component_breakdown": {"market_alignment": 20, "technical": 20, "momentum": 15, "volume": 15, "catalyst": 15, "portfolio_fit": 10, "liquidity": 5}
}
```

## 9. Committee Engine → Red Team Engine

```json
{
  "engine": "COMMITTEE_ENGINE",
  "engine_version": "1.0.0",
  "schema_version": "1.0.0",
  "ticker": "string",
  "votes": [
    {"role": "Chief Market Strategist | Technical Analyst | Portfolio Manager | Risk Manager | Options Specialist | Execution Specialist",
     "vote": "STRONG BUY | BUY | WATCH | PASS | REDUCE | REJECT",
     "confidence": "High | Medium | Low (member's own qualitative self-rating, per §9 — distinct from MASTER_DECISION_ENGINE.md §7's numeric Confidence Model)",
     "concern": "string"}
  ],
  "consensus_score": "number (0-100, weighted per COMMITTEE_ENGINE.md §6/§10)",
  "consensus_label": "Unanimous | Strong Consensus | Moderate Consensus | Weak Consensus | No Consensus",
  "risk_manager_forced_escalation": "boolean (true if Risk Manager voted REDUCE or REJECT, per COMMITTEE_ENGINE.md §12)"
}
```

## 10. Red Team Engine → Master Decision Engine

```json
{
  "engine": "RED_TEAM_ENGINE",
  "engine_version": "1.0.0",
  "schema_version": "1.0.0",
  "ticker": "string",
  "counter_thesis": "string",
  "attack_categories": [
    {"category": "THESIS | DATA | ASSUMPTION | VALUATION | RISK | TIMING | EXECUTION | MACRO | TECHNICAL | PORTFOLIO | CATALYST",
     "finding": "string",
     "result": "survived | failed | not_applicable (VALUATION/EXECUTION only, per RED_TEAM_ENGINE.md §4A)"}
  ],
  "critical_risk_flag": "boolean",
  "binding_result_if_flagged": "NO TRADE | WATCH | REDUCE | EXIT | null"
}
```

## 11. Master Decision Engine → Execution Engine / Report

```json
{
  "engine": "MASTER_DECISION_ENGINE",
  "engine_version": "1.0.0",
  "schema_version": "1.0.0",
  "confidence_model_version": "1.0.0",
  "ticker": "string",
  "final_outcome": "EXECUTE | WATCH | HOLD | REDUCE | EXIT | NO TRADE | INSUFFICIENT VERIFIED INFORMATION | SYSTEM REVIEW REQUIRED",
  "wcs": "number | null",
  "confidence": "Very High | High | Medium | Low | Very Low",
  "uncertainty_tier": "Low | Medium | High | Critical",
  "uncertainty_reasons": ["string"],
  "gates": {"verification": "PASS | FAIL", "risk": "PASS | FAIL", "red_team": "PASS | FAIL"},
  "decision_snapshot_ref": "run-id"
}
```

## 12. Portfolio Optimization Engine → Execution Engine / Report

```json
{
  "engine": "PORTFOLIO_OPTIMIZATION_ENGINE",
  "engine_version": "1.0.0",
  "schema_version": "1.0.0",
  "ticker": "string",
  "decision": "EXECUTE | HOLD | REDUCE | EXIT | WATCH (echoed verbatim from MASTER_DECISION_ENGINE.md §11 final_outcome for this ticker; never computed here)",
  "current_weight_pct": "number",
  "target_weight_pct": "number",
  "suggested_shares": "number (signed; negative = sell, positive = buy; 0 for HOLD/WATCH unless an advisory note applies, per PORTFOLIO_OPTIMIZATION_ENGINE.md §7A)",
  "capital_released": "number | null (null for EXECUTE — see capital_required — or for HOLD/WATCH with no advisory)",
  "capital_required": "number | null (null unless decision is EXECUTE)",
  "reason": "string",
  "priority": "P1 | P2 | P3 (per EXECUTION_ENGINE.md §3G)",
  "execution_required": "boolean (false for HOLD/WATCH with no advisory triggered, per §7A)",
  "status": "READY | PORTFOLIO OPTIMIZATION INCOMPLETE — DATA REQUIRED | NO REBALANCING ACTION"
}
```

`decision` is a read-only echo, not a second source of truth — a consumer
SHALL treat any mismatch against `MASTER_DECISION_ENGINE.md` §11's
`final_outcome` for the same ticker as a contract violation in this
engine's output, never as a newer or overriding value.

## 13. Execution Engine → Human

`schema_version` is `"1.1.0"` for this shape only (not `"1.0.0"` like every
other engine in this document) — this shape gained two new fields
(`max_slippage`, `priority`) per §1A's rule that a field added/removed/
retyped bumps `schema_version`, independent of `engine_version`.

```json
{
  "engine": "EXECUTION_ENGINE",
  "engine_version": "1.0.0",
  "schema_version": "1.1.0",
  "ticker": "string",
  "order_type": "market | limit | stop-limit | not_applicable",
  "entry_zone": {"low": "number", "high": "number", "as_of": "ISO8601"},
  "stop": "number",
  "target": "number",
  "position_size": "number",
  "max_loss": "number",
  "slippage_assumption": "number (half bid-ask spread + volatility buffer, in the quote's price unit, per §3C)",
  "max_slippage": "number (hard cap distinct from slippage_assumption — the point beyond which the plan is no longer valid as sized, per §3C)",
  "expected_fill_price": "number (limit price adjusted by slippage_assumption, per §3C)",
  "market_impact_note": "string | null (present when size approaches the §3B liquidity cap)",
  "priority": "P1 | P2 | P3 (per §3G; required when this plan is part of a Multi-Position Trade Plan, null for a single-ticket plan)",
  "execution_gate": {
    "quote_freshness_ok": "boolean",
    "liquidity_ok": "boolean (<= 10% ADV or <= 10% OI, per §3B)",
    "spread_ok": "boolean (order type matches §3A tier)",
    "market_status_ok": "boolean (session verified, no unconfirmed halt)",
    "order_size_validated": "boolean (matches Risk Engine-approved size, per §1A check 5)",
    "human_approval_presented": "boolean"
  },
  "gap_risk_note": "string | null (present when a new entry is timed near a gap-risk window or an existing position carries through one, per §3F)",
  "status": "READY | DO NOT EXECUTE — REVERIFY | NOT APPLICABLE"
}
```

---

## 14. Versioning

Each engine's `engine_version` field SHALL match that engine document's
`Version` header at the time the output was produced, feeding directly into
the Decision Snapshot (`DECISION_SNAPSHOT_POLICY.md` §2). A schema change to
this contract is additive-only within a minor version (new optional fields);
removing or renaming a field requires a major version bump and an
`docs/ARCHITECTURE.md` consistency review.

---

End of Document

TRX Trading OS v1.0
