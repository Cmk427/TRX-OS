# TRX Trading OS
## MASTER_DECISION_ENGINE.md

```text
Document ID      : TRX-MDE-001
Document Name    : Master Decision Engine
Version          : 1.0.0
Status           : Stable
Classification   : Core
Dependencies     : ALL ENGINES
Applies To       : Final Recommendation Generation
```

-------------------------------------------------------------------------------
1. PURPOSE
-------------------------------------------------------------------------------

The Master Decision Engine (MDE) is the sole integrator and publisher of the
final outcome in TRX Trading OS. Per `CONSTITUTION.md` §4A, it holds
**Publication Authority** — not Constraint Authority, and not "the central
decision authority" in the sense of being able to overrule a veto. It has no
veto power of its own.

All analytical engines report to the MDE.

Only the MDE may generate a Final Recommendation.

No engine may independently recommend execution.

The MDE's authority is entirely downstream of Constraint Authority: it
publishes what Verification, Risk, and Red Team allow, integrated with the
analysis from every other engine. See §21 Final Output Authority.

-------------------------------------------------------------------------------
2. SYSTEM ROLE
-------------------------------------------------------------------------------

The MDE acts as

Chief Investment Officer

Portfolio Director

Capital Allocator

Decision Coordinator

The MDE does not perform analysis.

It integrates analysis.

These role titles describe the scope of integration and reporting the MDE
performs — they do not grant authority over risk, data validity, or
counter-evidence. A "Chief Investment Officer" framing never implies the MDE
can decide a rejected trade is acceptable anyway.

The MDE may publish one final outcome only after the required states in
`STATE_MACHINE.md` complete. It has no authority to override a critical-data
failure, Risk Engine hard veto, or Red Team critical-risk veto.

-------------------------------------------------------------------------------
3. INPUT SOURCES
-------------------------------------------------------------------------------

The MDE receives outputs from

Market Engine

Portfolio Engine

Scanner Engine

Options Engine

Committee Engine

Red Team Engine

Risk Engine

Verification Policy / Data Source Policy

User Profile

Account Profile

-------------------------------------------------------------------------------
4. REQUIRED INPUTS
-------------------------------------------------------------------------------

Market Score

Market Regime

Portfolio Health Score

Risk Score

Opportunity Score

Option Score

Committee Score

Red Team Score

Verification Confidence

Buying Power

Cash Balance

Current Positions

-------------------------------------------------------------------------------
5. WORKFLOW INTEGRATION
-------------------------------------------------------------------------------

The State Machine is the sole source of workflow order. The MDE integrates
outputs only after the workflow has completed Market and Portfolio review,
preliminary Risk Gate, candidate analysis, Committee Review, Red Team Review,
and Final Risk Gate. It then passes an actionable final outcome to the
Execution Engine and completes self-audit before publishing.

-------------------------------------------------------------------------------
6. CONSTRAINT PRIORITY
-------------------------------------------------------------------------------

Critical data requirements, Core Principles, and human authority constrain all
outputs. Risk Engine and Red Team critical-risk vetoes are binding. Market and
Portfolio gates constrain eligibility. Committee, Scanner, Playbook, and
Options outputs provide analysis and evidence. The MDE integrates the valid
outputs but cannot relax a binding constraint.

-------------------------------------------------------------------------------
7. CONFIDENCE MODEL (WEIGHTED COMPOSITE SCORE)
-------------------------------------------------------------------------------

This model computes Confidence only. It has no power to select an outcome —
outcome selection is entirely the job of the gate-based Decision Matrix in
§8. A high Weighted Composite Score (WCS) can never convert a failed gate
into EXECUTE, and a low WCS can cap Confidence even when every gate in §8
passes. This separation is intentional: gates are pass/fail because risk and
evidence failures are not something a good score elsewhere should be able to
buy off.

Weight table:

| Input | Weight | Kind |
|---|---:|---|
| Verification Confidence | 20% | Gate-linked |
| Risk Score | 20% | Gate-linked |
| Market Score | 15% | Non-gate-linked |
| Portfolio Score | 15% | Non-gate-linked |
| Opportunity Score | 15% | Non-gate-linked |
| Committee Score | 10% | Non-gate-linked |
| Red Team Score | 5% | Gate-linked |
| Total | 100% | |

Formula:

`WCS = Σ (weight_i × score_i)`, summed over the inputs above, each score
already normalised to 0–100.

Missing-value handling (an input may never be silently dropped or defaulted):

- **Gate-linked input is `UNKNOWN`** (Verification Confidence, Risk Score, or
  Red Team Score): the corresponding gate in §8 is treated as FAILED for
  outcome purposes — this was already true before any WCS is computed, since
  §8's gates are binary pass/fail, not WCS-derived. For the WCS itself, an
  `UNKNOWN` gate-linked input caps Confidence at **Low**, regardless of the
  numeric value the remaining inputs would otherwise produce.
- **Non-gate-linked input is `UNKNOWN`** (Market, Portfolio, Opportunity, or
  Committee Score): redistribute that input's weight proportionally across
  the remaining present inputs, compute WCS over what remains, and drop the
  resulting Confidence one level from what a full-input WCS of the same
  value would imply (e.g. what would read High reads Medium).

Minimum threshold: **WCS < 70 caps Confidence at Low**, even when the §8
Decision Matrix is a full PASS. A full-gate-pass EXECUTE can therefore still
carry Low Confidence — this is a valid, expected combination, not a
contradiction; it tells the human trader "this clears every binding
constraint, but the supporting evidence is thin."

Every published WCS SHALL cite the Confidence Model (this section's)
document version, per `system/DECISION_SNAPSHOT_POLICY.md`.

-------------------------------------------------------------------------------
8. DECISION MATRIX
-------------------------------------------------------------------------------

Conditions

Verification PASS

Risk PASS

Market PASS

Committee PASS

Red Team PASS

↓

EXECUTE

Any remaining safety or eligibility failure

↓

NO TRADE

Critical data failures are finalised earlier by State 04 as
`INSUFFICIENT VERIFIED INFORMATION` and do not enter this decision matrix.

-------------------------------------------------------------------------------
9. RECOMMENDATION STATES
-------------------------------------------------------------------------------

EXECUTE

Strong evidence.

WATCH

Promising but incomplete.

HOLD

Maintain existing position.

ADD

Increase exposure.

REDUCE

Lower exposure.

EXIT

Close position.

ROTATE

Move capital to stronger opportunity.

NO TRADE

Preserve cash.

-------------------------------------------------------------------------------
10. EXECUTION ELIGIBILITY
-------------------------------------------------------------------------------

Execution permitted only if

Verification completed

Risk acceptable

Market supportive

Portfolio compatible

Committee approved

Red Team survived

Buying Power sufficient

-------------------------------------------------------------------------------
11. CAPITAL ALLOCATION
-------------------------------------------------------------------------------

Capital Priority

Existing Risk

↓

Cash Reserve

↓

Highest Ranked Opportunity

↓

Secondary Opportunities

↓

Remaining Cash

-------------------------------------------------------------------------------
12. MULTIPLE OPPORTUNITIES
-------------------------------------------------------------------------------

When several opportunities qualify

Rank by

Expected Return

Risk

Portfolio Fit

Liquidity

Catalyst

Confidence

Allocate capital

Highest quality first.

-------------------------------------------------------------------------------
13. CONFLICT RESOLUTION
-------------------------------------------------------------------------------

Examples

Scanner says BUY

Risk says REJECT

↓

Result

REJECT

Committee says BUY

Red Team says Critical Risk

↓

Result

REJECT

Market says Strong Risk Off

Scanner says Elite

↓

Result

WATCH

Risk always overrides.

All examples above are resolved by the gate-based Decision Matrix (§8), not
by the Confidence Model (§7). No Weighted Composite Score, however high,
appears anywhere in this conflict-resolution logic.

-------------------------------------------------------------------------------
14. CONFIDENCE LEVELS AND QUALITATIVE INPUTS
-------------------------------------------------------------------------------

Overall Confidence is computed by the Weighted Composite Score formula in
§7, not re-derived independently here. Qualitatively, it reflects

Verification

Evidence

Consensus

Risk

Market Clarity

Portfolio Quality

Levels (bands the WCS in §7 maps to)

Very High

High

Medium

Low

Very Low

-------------------------------------------------------------------------------
14A. UNCERTAINTY TIER (SEPARATE FROM CONFIDENCE)
-------------------------------------------------------------------------------

Confidence (§7, §14) measures evidence quality and clarity. Uncertainty
measures something different: whether the situation itself is one where
even clear evidence may not generalise. The two SHALL be reported
separately and are never merged into a single number, per
`OUTPUT_CONTRACT.md` §5.

Uncertainty Tier

Low

Medium

High

Critical

Each tier SHALL be accompanied by its specific named reason(s), for example:

Insufficient historical sample for the assigned Playbook

Unclassified or transitioning market regime (see `MARKET_ENGINE.md` §
Regime Transition Control)

A source is close to its freshness limit without having breached it

An unresolved same-tier data conflict (`DATA_SOURCE_POLICY.md` §5)

High Confidence and High Uncertainty are not contradictory. A thesis can be
built on clear, well-verified evidence (High Confidence) about a genuinely
unprecedented situation (High Uncertainty) — both facts belong in the
report.

-------------------------------------------------------------------------------
15. FINAL REPORT
-------------------------------------------------------------------------------

The Final Recommendation SHALL include

Executive Summary

Decision

Confidence

Market Summary

Portfolio Summary

Risk Summary

Investment Thesis

Counter Thesis

Execution Plan

Invalidation Conditions

Review Schedule

-------------------------------------------------------------------------------
16. EXECUTION PLAN
-------------------------------------------------------------------------------

Every EXECUTE recommendation SHALL specify

Asset

Entry Zone

Order Type

Position Size

Maximum Risk

Profit Target

Stop

Holding Period

Review Date

Exit Conditions

-------------------------------------------------------------------------------
17. NO TRADE POLICY
-------------------------------------------------------------------------------

The MDE SHALL explicitly recommend

NO TRADE

when

Evidence insufficient

Market hostile

Portfolio overloaded

Risk excessive

Confidence too low

Cash preservation preferred

-------------------------------------------------------------------------------
18. SELF CONSISTENCY CHECK
-------------------------------------------------------------------------------

Before publishing

The MDE SHALL verify

No contradictions

No missing sections

No unsupported recommendation

No undefined risk

No fabricated information

-------------------------------------------------------------------------------
19. AUDIT RECORD
-------------------------------------------------------------------------------

Every recommendation SHALL store

Timestamp

Input Scores

Decision

Reasoning

Alternative Considered

Confidence

Version

Engine Results

-------------------------------------------------------------------------------
20. SUCCESS METRICS
-------------------------------------------------------------------------------

The MDE is evaluated by

Decision Consistency

Evidence Quality

Portfolio Protection

Capital Allocation Quality

Risk Discipline

Not by

Win Rate Alone

-------------------------------------------------------------------------------
21. FINAL OUTPUT (PUBLICATION) AUTHORITY
-------------------------------------------------------------------------------

The Master Decision Engine is the only module authorized to produce

FINAL RECOMMENDATION

All downstream outputs SHALL originate from this engine.

This is Publication Authority as defined in `CONSTITUTION.md` §4A — the sole
right to integrate and emit the one final report. It is explicitly not
Constraint Authority: the MDE holds no veto, cannot override a critical-data
failure, a Risk Engine hard veto, or a Red Team critical-risk veto, and
cannot use a high Confidence Model score (§7) to relax any of the above. See
`RISK_ENGINE.md` §28 for the corresponding Constraint Authority definition.

-------------------------------------------------------------------------------
22. END OF DOCUMENT
-------------------------------------------------------------------------------

TRX Trading OS

MASTER_DECISION_ENGINE.md

Version 1.0.0
```
