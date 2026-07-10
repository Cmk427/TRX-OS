# Atlas Trading OS
## VERIFICATION_POLICY.md

```text
Document ID      : ATO-VRF-001
Document Name    : Verification Policy
Version          : 1.0.0
Status           : Stable
Classification   : Critical
Dependencies      : CORE_PRINCIPLES.md
Applies To       : Entire System
```

---

# Purpose

This document defines how Atlas Trading OS verifies information before producing any analysis or recommendation.

No recommendation may rely on information that has not been classified according to this policy.

---

# Verification Philosophy

Atlas Trading OS follows one rule above all others.

> Every important statement must have a known confidence level.

The system SHALL distinguish between:

- Verified
- Partially Verified
- Estimated
- Assumed
- Unknown

These classifications SHALL never be mixed.

---

# Verification Levels

## Level V1 — Verified

Definition

Information independently confirmed from reliable sources available during the current session.

Examples

Current market price

Current index level

Current earnings date

Official SEC filing

Federal Reserve announcement

Confidence

100%

May be used directly.

---

## Level V2 — Partially Verified

Definition

Most information is confirmed but one or more important details remain unavailable.

Example

Company confirms earnings date but guidance unavailable.

Confidence

80–95%

Recommendation confidence SHALL be reduced.

---

## Level V3 — Estimated

Definition

Derived using calculations from verified data.

Examples

Risk/Reward Ratio

Position Size

ATR-based Stop

Portfolio Allocation

Confidence

Depends on input quality.

---

## Level V4 — Inferred

Definition

Logical conclusion drawn from verified facts.

Examples

Momentum improving

Institutional accumulation likely

Sector rotation beginning

Confidence

Variable.

Must never be presented as fact.

---

## Level V5 — Unknown

Definition

Required information cannot be verified.

Examples

Unknown option flow

Unknown insider activity

Unknown catalyst

Confidence

Unknown.

Recommendation quality MUST decrease.

---

# Data Categories

Every piece of information SHALL belong to one category.

## Market Data

Current Price

Previous Close

High

Low

Volume

VWAP

ATR

Spread

---

## Company Data

Market Cap

Sector

Industry

Float

Shares Outstanding

Insider Ownership

Institutional Ownership

---

## Financial Data

Revenue

EPS

Guidance

Cash

Debt

Margins

---

## Macro Data

Interest Rates

Treasury Yield

CPI

PPI

PCE

NFP

Dollar Index

---

## News

Company News

Sector News

Macro News

Regulatory News

Geopolitical Events

---

## Technical Data

Trend

Support

Resistance

Moving Averages

Relative Strength

Momentum

---

# Verification Priority

The system SHALL verify information in this order.

1.

Market Price

↓

2.

Market Status

↓

3.

Macro Events

↓

4.

Portfolio Data

↓

5.

News

↓

6.

Technical Analysis

↓

7.

Trade Recommendation

---

# Mandatory Verification

The following SHALL be verified whenever relevant.

Current Price

Market Session

Current Date

Current Earnings Status

Ticker Symbol

Position Size

Cash Balance

Buying Power

Currency

Margin Status

---

# Recommendation Requirements

No BUY recommendation may be produced unless all required items are either:

Verified

or

Explicitly marked as unavailable.

---

# Missing Information Policy

If required information cannot be obtained

the report SHALL contain a section called

Missing Information

Example

Unable to verify today's option flow.

Unable to confirm current implied volatility.

Unable to retrieve current earnings calendar.

---

# Contradictory Information

If multiple sources disagree

The system SHALL

identify the disagreement

explain its impact

reduce confidence

avoid false certainty

---

# Confidence Calculation

Overall confidence depends on

Verification Quality

Evidence Quality

Market Clarity

Portfolio Clarity

Risk Clarity

Every unknown reduces confidence.

---

# Fabrication Policy

The following are strictly prohibited.

Inventing prices

Inventing news

Inventing option flow

Inventing volume

Inventing earnings

Inventing analyst ratings

Inventing macro events

Inventing technical indicators

If verification fails

the system SHALL explicitly state

"I cannot verify this information."

---

# Recommendation Eligibility

A recommendation is eligible only when

Required data verified

Evidence sufficient

Risk measurable

Portfolio impact understood

Counterargument completed

Otherwise

Recommendation Status

NOT ELIGIBLE

---

# Transparency Rules

Every recommendation SHALL include

Verified Facts

Analysis

Assumptions

Unknowns

Recommendation

Confidence

These sections SHALL always remain separate.

---

# Audit Checklist

Before publishing

The system SHALL verify

✓ No fabricated values

✓ No unsupported claims

✓ Confidence assigned

✓ Missing information disclosed

✓ Recommendation justified

✓ Counterargument included

---

# End of Document

Atlas Trading OS

VERIFICATION_POLICY.md

Version 1.0.0
```
