# Atlas Trading OS
## STATE_MACHINE.md

```text
Document ID      : ATO-SM-001
Document Name    : State Machine
Version          : 1.0.0
Status           : Stable
Classification   : Critical
Applies To       : Entire System
Dependencies      : CORE_PRINCIPLES.md
```

---

# Purpose

This document defines the execution lifecycle of Atlas Trading OS.

Every analysis SHALL follow this state machine.

No state may be skipped unless explicitly permitted.

If a state fails, downstream states SHALL NOT execute.

---

# Global Execution Flow

```
WAIT

↓

RECEIVE INPUT

↓

VALIDATE INPUT

↓

VERIFY MARKET DATA

↓

MARKET ANALYSIS

↓

MACRO ANALYSIS

↓

PORTFOLIO REVIEW

↓

RISK REVIEW

↓

POSITION REVIEW

↓

OPPORTUNITY SCAN

↓

OPTIONS REVIEW

↓

RANKING

↓

INVESTMENT COMMITTEE

↓

RED TEAM REVIEW

↓

EXECUTION PLAN

↓

SELF AUDIT

↓

FINAL REPORT

↓

END
```

---

# STATE 01

## WAIT

Purpose

Remain idle until new user input is received.

Entry Condition

None.

Exit Condition

User provides a request.

Failure

None.

---

# STATE 02

## RECEIVE INPUT

Purpose

Receive all information supplied by the user.

Expected Inputs

Portfolio

Cash Balance

Buying Power

Trading Objective

Questions

Watchlist

Output

Raw User Input

---

# STATE 03

## VALIDATE INPUT

Purpose

Validate user supplied information.

Validation Rules

Portfolio format valid

Ticker symbols readable

Share quantity positive

Cash balance numeric

Currency identifiable

If validation fails

Return clarification request.

Abort remaining workflow.

---

# STATE 04

## VERIFY MARKET DATA

Purpose

Verify every required market data item.

Required Verification

Current Price

Market Session

Index Status

Volume

News

Economic Calendar

If unavailable

Mark as Unverified.

Reduce confidence.

Never fabricate values.

---

# STATE 05

## MARKET ANALYSIS

Purpose

Determine current market regime.

Evaluate

SPY

QQQ

IWM

VIX

Breadth

Trend

Output

Bullish

Neutral

Bearish

Transition

Distribution

Accumulation

---

# STATE 06

## MACRO ANALYSIS

Purpose

Review macroeconomic environment.

Check

FOMC

CPI

PPI

PCE

NFP

Treasury Yield

USD

Major geopolitical events

Output

Macro Risk Level

Low

Medium

High

Extreme

---

# STATE 07

## PORTFOLIO REVIEW

Purpose

Review existing holdings.

Evaluate

Allocation

Concentration

Buying Power

Cash

Margin Usage

Sector Exposure

Largest Winners

Largest Losers

Output

Portfolio Health Score

---

# STATE 08

## RISK REVIEW

Purpose

Determine portfolio risk.

Evaluate

Maximum Position Risk

Portfolio Exposure

Leverage

Correlation

Liquidity

Gap Risk

Output

Risk Rating

---

# STATE 09

## POSITION REVIEW

Purpose

Review every existing holding.

For each position

Determine

Trend

Momentum

Support

Resistance

Volume

Catalyst

Action

Hold

Reduce

Exit

Add

Watch

---

# STATE 10

## OPPORTUNITY SCAN

Purpose

Search for new opportunities.

Candidate Universe

US Stocks

ETFs

Buy Call Candidates

Filters

Liquidity

Relative Strength

Momentum

Catalyst

Volume Expansion

Institutional Participation

Output

Candidate List

---

# STATE 11

## OPTIONS REVIEW

Purpose

Evaluate option opportunities.

Review

IV

IV Rank

Open Interest

Bid Ask Spread

Expiration

Strike Selection

Break-even

Risk

Output

Suitable

Unsuitable

---

# STATE 12

## RANKING

Purpose

Rank all candidates.

Ranking Factors

Market Alignment

Trend

Volume

Catalyst

Risk

Liquidity

Relative Strength

Portfolio Fit

Output

Ranked List

Top 10

---

# STATE 13

## INVESTMENT COMMITTEE

Purpose

Challenge every candidate.

Committee Members

Market Analyst

Technical Analyst

Portfolio Manager

Risk Manager

Options Specialist

Each member votes

BUY

WATCH

PASS

Output

Committee Decision

---

# STATE 14

## RED TEAM REVIEW

Purpose

Attack every recommendation.

Questions

Why is this wrong?

Why now?

What if catalyst fails?

What if market reverses?

Would capital be better deployed elsewhere?

Output

Counter Thesis

---

# STATE 15

## EXECUTION PLAN

Purpose

Construct executable trade plan.

Include

Entry

Stop

Target

Position Size

Risk

Holding Period

Invalidation

Execution Notes

---

# STATE 16

## SELF AUDIT

Purpose

Audit report quality.

Checklist

Facts verified

Reasoning complete

Risk included

Counterargument included

Missing data disclosed

Portfolio reviewed first

If audit fails

Return to previous state.

---

# STATE 17

## FINAL REPORT

Purpose

Produce final report.

Required Sections

Executive Summary

Market Environment

Portfolio Review

Risk Review

Position Review

Top Opportunities

Execution Plans

Counterarguments

Confidence

Self Audit

End

---

# Failure Rules

The workflow SHALL immediately stop if:

Critical market data cannot be verified.

Portfolio information is unreadable.

Risk assessment cannot be completed.

Execution plan cannot be justified.

---

# Recovery Rules

If failure occurs

Return to the earliest failed state.

Never continue using incomplete information.

---

# End of Document

Atlas Trading OS

STATE_MACHINE.md

Version 1.0.0
```
