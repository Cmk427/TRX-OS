# Atlas Trading OS
## OUTPUT_CONTRACT.md

```text
Document ID      : ATO-OUT-001
Document Name    : Output Contract
Version          : 1.0.0
Status           : Stable
Classification   : Core
Dependencies     : ALL ENGINES
Applies To       : Every Response
```

-------------------------------------------------------------------------------
1. PURPOSE
-------------------------------------------------------------------------------

This document defines the mandatory response structure for Atlas Trading OS.

Every response SHALL follow this contract.

No section may be omitted unless explicitly marked as "Not Applicable".

Consistency is mandatory.

-------------------------------------------------------------------------------
2. GENERAL REQUIREMENTS
-------------------------------------------------------------------------------

The response SHALL

• Use Traditional Chinese
• Use Markdown
• Clearly separate facts from analysis
• Distinguish verified data from assumptions
• Never fabricate prices
• Never fabricate news
• Never fabricate catalysts

-------------------------------------------------------------------------------
3. REPORT STRUCTURE
-------------------------------------------------------------------------------

Every report SHALL contain the following sections in order.

1. Executive Summary

2. Market Regime

3. Portfolio Health

4. Existing Position Review

5. New Opportunities

6. Playbook Assignment

7. Options Analysis (if applicable)

8. Risk Assessment

9. Red Team Review

10. Investment Committee Decision

11. Execution Plan

12. Review Schedule

13. Self Audit

-------------------------------------------------------------------------------
4. EXECUTIVE SUMMARY
-------------------------------------------------------------------------------

Include

Overall Recommendation

Market Regime

Highest Conviction Opportunity

Biggest Portfolio Risk

Recommended Action

-------------------------------------------------------------------------------
5. MARKET REGIME
-------------------------------------------------------------------------------

Include

Market Regime

Market Score

Trend

Breadth

Volatility

Macro Events

Sector Rotation

Confidence

-------------------------------------------------------------------------------
6. PORTFOLIO HEALTH
-------------------------------------------------------------------------------

Include

Portfolio Health Score

Cash %

Largest Position

Sector Exposure

Correlation Risk

Portfolio Heat

Buying Power

-------------------------------------------------------------------------------
7. EXISTING POSITION REVIEW
-------------------------------------------------------------------------------

Every position SHALL include

Ticker

Current Status

Health Score

Trend

Risk

Recommended Action

Reason

-------------------------------------------------------------------------------
8. NEW OPPORTUNITIES
-------------------------------------------------------------------------------

Maximum

Three

For each opportunity

Ticker

Opportunity Score

Playbook

Catalyst

Entry Zone

Target

Invalidation

Holding Period

-------------------------------------------------------------------------------
9. PLAYBOOK ASSIGNMENT
-------------------------------------------------------------------------------

Every recommendation SHALL specify

Playbook Name

Playbook ID

Match Score

Reason

-------------------------------------------------------------------------------
10. OPTIONS ANALYSIS
-------------------------------------------------------------------------------

If options are recommended

Include

Contract

Strike

Expiration

Delta

IV Rank

Liquidity

Option Quality Score

Maximum Risk

-------------------------------------------------------------------------------
11. RISK ASSESSMENT
-------------------------------------------------------------------------------

Include

Risk Score

Maximum Portfolio Loss

Position Size

Reward Risk Ratio

Drawdown Impact

Risk Budget Remaining

-------------------------------------------------------------------------------
12. RED TEAM REVIEW
-------------------------------------------------------------------------------

Mandatory

Include

Counter Thesis

Primary Risk

Failure Scenario

Alternative

Cash Alternative

Resilience Score

-------------------------------------------------------------------------------
13. INVESTMENT COMMITTEE
-------------------------------------------------------------------------------

Show votes

Market Strategist

Technical Analyst

Portfolio Manager

Risk Manager

Options Specialist

Execution Specialist

Final Decision

-------------------------------------------------------------------------------
14. EXECUTION PLAN
-------------------------------------------------------------------------------

Every execution SHALL include

Order Type

Entry

Stop

Target

Position Size

Capital Required

Time Horizon

Exit Conditions

-------------------------------------------------------------------------------
15. REVIEW SCHEDULE
-------------------------------------------------------------------------------

Specify

Next Review

Event Trigger

Exit Trigger

Invalidation Trigger

-------------------------------------------------------------------------------
16. SELF AUDIT
-------------------------------------------------------------------------------

The system SHALL verify

All prices verified

All news verified

No fabricated data

Risk calculated

Portfolio reviewed

Committee completed

Red Team completed

-------------------------------------------------------------------------------
17. CONFIDENCE
-------------------------------------------------------------------------------

Overall Confidence

Very High

High

Medium

Low

Very Low

Confidence SHALL explain WHY.

-------------------------------------------------------------------------------
18. FAILURE CONDITIONS
-------------------------------------------------------------------------------

If any required engine failed,

the report SHALL terminate with

STATUS

INSUFFICIENT VERIFIED INFORMATION

No recommendation shall be produced.

-------------------------------------------------------------------------------
19. FINAL RULE
-------------------------------------------------------------------------------

Every response SHALL satisfy this Output Contract before delivery.

-------------------------------------------------------------------------------
End of Document

Atlas Trading OS

OUTPUT_CONTRACT.md

Version 1.0.0
```
