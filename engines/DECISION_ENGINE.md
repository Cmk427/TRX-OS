# TRX Trading OS
## DECISION_ENGINE.md

```text
Document ID      : TRX-DEC-001
Document Name    : Decision Engine
Version          : 1.2.0
Status           : Stable
Classification   : Critical
Dependencies      : CORE_PRINCIPLES.md
                    STATE_MACHINE.md
                    VERIFICATION_POLICY.md
                    CONSTITUTION.md
                    DATA_SOURCE_POLICY.md
Applies To       : Entire System
```

---

# Purpose

The Decision Engine defines how TRX Trading OS converts verified information
into a transparent, ranked decision package for subsequent review.

The objective is NOT to predict future prices.

The objective is to maximize decision quality.

This engine owns candidate comparison, Opportunity Score, conviction rationale,
and preliminary disposition. It does not publish the final recommendation,
override a veto, or construct an execution plan. Workflow order is owned solely
by `STATE_MACHINE.md`; final integration is owned solely by the Master Decision
Engine.

---

# Prohibited Outputs

To prevent two competing decision-makers in any future implementation, the
Decision Engine SHALL NOT produce:

- **NO FINAL RECOMMENDATION** — only `MASTER_DECISION_ENGINE.md` may publish
  `EXECUTE`, `NO TRADE`, `WATCH`, `HOLD`, `REDUCE`, or `EXIT` as a final,
  reportable outcome (`MASTER_DECISION_ENGINE.md` §1).
- **NO EXECUTION DECISION** — a BUY/SELL determination, or any statement that
  reads as authorization to act, belongs to Master Decision (§8 Decision
  Matrix) and, downstream of that, `EXECUTION_ENGINE.md`.
- **NO CAPITAL ALLOCATION** — position sizing and capital deployment belong
  to `RISK_ENGINE.md` (sizing) and `MASTER_DECISION_ENGINE.md` §11 (allocation
  priority). The Decision Engine's Opportunity Score ranks candidates; it
  does not decide how much capital, if any, is committed to them.

The Decision Engine's sole output is the ranked Opportunity Score package
(§ Opportunity Score) handed to Committee Review — a *candidate* disposition,
never a *final* one.

---

# Decision Philosophy

Every recommendation SHALL satisfy three requirements.

1.

Supported by evidence.

2.

Acceptable risk.

3.

Portfolio compatible.

Failure of any requirement SHALL reject the recommendation.

---

# Candidate Dispositions

Every candidate SHALL receive exactly one provisional disposition. Existing
position actions remain subject to the Portfolio, Risk, and Master Decision
engines; `EXECUTE` remains a final Master Decision outcome, not a Decision
Engine instruction.

EXECUTE CANDIDATE

WATCH

HOLD

REDUCE

EXIT

NO ACTION

---

# Decision Pipeline

```
Consume outputs from completed States 04–11

↓

Rank candidates and calculate Opportunity Score

↓

Record conviction, evidence, risk/reward, and portfolio fit

↓

Pass the ranked package to Committee Review

↓

Master Decision Engine receives this package together with independent
Committee, Red Team, and final Risk outputs
```

---

# Stage 1

## Market Gate

Question

Does the overall market support taking new risk?

Evaluation

SPY Trend

QQQ Trend

VIX

Breadth

Sector Rotation

Liquidity

Possible Results

PASS

CAUTION

FAIL

If FAIL

No aggressive candidate disposition (§ Candidate Dispositions — the
`EXECUTE CANDIDATE` recommendation, not a standalone "BUY" outcome; "BUY" is
Committee's own vote option, `COMMITTEE_ENGINE.md` §5, not a Decision Engine
output) shall be produced.

---

# Stage 2

## Portfolio Gate

Question

Can the portfolio safely accept additional exposure?

Review

Cash

Buying Power

Sector Exposure

Single Position Risk

Correlation

Margin Usage

Concentration

Result

PASS

LIMITED

FAIL

If FAIL

Recommend portfolio adjustment before opening new positions.

---

# Stage 3

## Candidate Gate

Every candidate receives evaluation.

Trend

Momentum

Volume

Liquidity

Catalyst

Relative Strength

Institutional Interest

Technical Structure

Risk

Reward

---

# Stage 4

## Opportunity Score

Every candidate receives a score.

Suggested Weight

Market Alignment

20%

Technical Structure

20%

Momentum

15%

Volume

15%

Catalyst

15%

Portfolio Fit

10%

Liquidity

5%

Maximum Score

100

---

# Opportunity Rating

95-100

Exceptional

90-94

Very Strong

80-89

Strong

70-79

Watchlist

Below 70

Reject

---

# Stage 5

## Conviction Score

Conviction is independent of expected return.

Factors

Evidence Quality

Verification Quality

Market Clarity

Catalyst Quality

Risk Clarity

Portfolio Compatibility

Conviction Rating — deliberately NOT called "Confidence" here, to avoid
collision with `MASTER_DECISION_ENGINE.md` §7's formal Confidence Model.
That Confidence is a Weighted Composite Score computed at State 16, two
states after this Conviction Score; Decision Engine (State 12) has no such
number to report yet — this star scale is a qualitative rating of
conviction/evidence quality feeding the later analysis, not an early
instance of MDE's Confidence.

★★★★★

Exceptional

★★★★☆

High

★★★☆☆

Moderate

★★☆☆☆

Low

★☆☆☆☆

Very Low

---

# Stage 6

## Risk / Reward Test

Every trade SHALL calculate

Maximum Risk

Potential Reward

Reward / Risk Ratio

Minimum acceptable ratio

2:1

Preferred

3:1+

If below minimum

Reject.

---

# Stage 7

## Portfolio Compatibility

Questions

Does this increase concentration?

Does this duplicate exposure?

Does this increase sector risk?

Does this increase correlation?

Does this reduce flexibility?

Any severe issue

Reject.

---

# Stage 8

## Committee Handoff

The Committee Engine owns member composition, weighting, consensus, dissent,
and escalation. The Decision Engine supplies the verified candidate package and
shall not calculate a competing vote.

---

# Stage 9

## Red Team

The Red Team SHALL challenge every `EXECUTE CANDIDATE` disposition (see
`RED_TEAM_ENGINE.md` §4A for the full 11-category attack framework this
maps onto — this section is Decision Engine's own preview of that review,
not a substitute for it).

Questions

What breaks this thesis?

Who is selling?

What catalyst could fail?

What macro risk exists?

Why should capital remain in cash instead?

Every `EXECUTE CANDIDATE` disposition requires a written Bear Case.

---

# Stage 10

## Decision Matrix

Conditions

High Score

High Conviction

PASS Committee

PASS Red Team

↓

ELIGIBLE FOR MASTER DECISION

Moderate Score

↓

WATCH

Weak Evidence

↓

PASS

Poor Risk

↓

REJECT

---

# Provisional Recommendation Types

EXECUTE CANDIDATE

Eligible for final Master Decision review; no order action is implied.

WATCH

Monitor.

No execution.

HOLD

Maintain existing position.

REDUCE

Decrease exposure.

EXIT

Close position.

NO ACTION

Remain in cash.

---

# Position Sizing Awareness (Not a Decision Engine Output)

This section is context the Decision Engine SHALL be aware of when ranking
candidates — it is not something the Decision Engine computes or outputs,
per the Prohibited Outputs section above ("NO CAPITAL ALLOCATION"). The
actual position size is computed and owned exclusively by `RISK_ENGINE.md`
§6 Position Sizing, using:

Portfolio Value

Risk Budget

Stop Distance

Correlation

Existing Exposure

Never on conviction alone.

A candidate's Portfolio Fit component of the Opportunity Score (§ Opportunity
Score) may reflect how well it *would* fit alongside these factors, but the
Decision Engine ranks and compares candidates on that basis — it does not
itself decide or publish a position size.

---

# Decision Quality

Every recommendation SHALL answer

Why this?

Why now?

Why not later?

Why not another asset?

What could invalidate this?

What changes tomorrow?

---

# Mandatory Decision Package

Each recommendation SHALL include

Decision

Conviction

Evidence

Bear Case

Reward

Risk

Invalidation

Position Size — SHOWN FOR CONTEXT ONLY, not computed by Decision Engine
(see "Position Sizing Awareness" above); this field is populated by
`RISK_ENGINE.md` §6 at States 07/15, not at State 12, and SHALL be marked
`PENDING RISK ENGINE` if displayed before those states run

Holding Period

Conviction Rating — this is the Stage 5 star scale (Exceptional/High/
Moderate/...), NOT `MASTER_DECISION_ENGINE.md` §7's formal Confidence,
which does not exist until State 16 (see Stage 5 above)

---

# Prohibited Behaviour

The Decision Engine SHALL NOT

Recommend trades without evidence.

Ignore portfolio exposure.

Ignore macro conditions.

Ignore risk.

Recommend trades because the user requested one.

Invent conviction.

Invent catalysts.

Invent probabilities.

---

# Success Criteria

A successful decision is

Evidence-based

Repeatable

Auditable

Transparent

Portfolio-aware

Risk-controlled

Not necessarily profitable.

---

# End of Document

TRX Trading OS

DECISION_ENGINE.md

Version 1.2.0
```
