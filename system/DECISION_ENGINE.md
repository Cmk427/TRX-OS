# Atlas Trading OS
## DECISION_ENGINE.md

```text
Document ID      : ATO-DEC-001
Document Name    : Decision Engine
Version          : 1.0.0
Status           : Stable
Classification   : Critical
Dependencies      : CORE_PRINCIPLES.md
                    STATE_MACHINE.md
                    VERIFICATION_POLICY.md
Applies To       : Entire System
```

---

# Purpose

The Decision Engine defines how Atlas Trading OS converts verified information into actionable trading decisions.

The objective is NOT to predict future prices.

The objective is to maximize decision quality.

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

# Decision States

Every candidate SHALL belong to exactly one state.

EXECUTE

WATCH

HOLD

REDUCE

EXIT

NO ACTION

---

# Decision Pipeline

```
Verify Data

↓

Evaluate Market

↓

Evaluate Portfolio

↓

Evaluate Candidate

↓

Calculate Risk

↓

Calculate Conviction

↓

Committee Review

↓

Red Team

↓

Decision

↓

Execution Plan
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

No aggressive BUY recommendation shall be produced.

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

Confidence Levels

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

## Investment Committee

Members

Market Analyst

Technical Analyst

Portfolio Manager

Risk Manager

Options Analyst

Voting

BUY

WATCH

PASS

REJECT

Simple majority required.

Tie Result

WATCH

---

# Stage 9

## Red Team

The Red Team SHALL challenge every BUY recommendation.

Questions

What breaks this thesis?

Who is selling?

What catalyst could fail?

What macro risk exists?

Why should capital remain in cash instead?

Every BUY requires a written Bear Case.

---

# Stage 10

## Decision Matrix

Conditions

High Score

High Conviction

PASS Committee

PASS Red Team

↓

EXECUTE

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

# Recommendation Types

EXECUTE

Immediate action justified.

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

# Position Sizing Rule

Position size SHALL depend on

Portfolio Value

Risk Budget

Stop Distance

Correlation

Existing Exposure

Never on conviction alone.

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

# Mandatory Output

Each recommendation SHALL include

Decision

Conviction

Evidence

Bear Case

Reward

Risk

Invalidation

Position Size

Holding Period

Confidence

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

Atlas Trading OS

DECISION_ENGINE.md

Version 1.0.0
```
