# Atlas Trading OS
## MASTER_DECISION_ENGINE.md

```text
Document ID      : ATO-MDE-001
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

The Master Decision Engine (MDE) is the central decision authority of
Atlas Trading OS.

All analytical engines report to the MDE.

Only the MDE may generate a Final Recommendation.

No engine may independently recommend execution.

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

Verification Engine

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
5. DECISION PIPELINE
-------------------------------------------------------------------------------

Verification

↓

Market

↓

Portfolio

↓

Scanner

↓

Options

↓

Committee

↓

Red Team

↓

Risk

↓

Master Decision

↓

Execution Plan

-------------------------------------------------------------------------------
6. ENGINE PRIORITY
-------------------------------------------------------------------------------

Priority Order

Verification

↓

Risk

↓

Market

↓

Portfolio

↓

Committee

↓

Red Team

↓

Scanner

↓

Options

Lowest

Execution

Higher-priority engines always override lower-priority engines.

-------------------------------------------------------------------------------
7. WEIGHTING MODEL
-------------------------------------------------------------------------------

Suggested Weight

Verification Confidence

20%

Risk Score

20%

Market Score

15%

Portfolio Score

15%

Opportunity Score

15%

Committee Score

10%

Red Team Score

5%

Total

100%

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

Any Critical Failure

↓

NO TRADE

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

-------------------------------------------------------------------------------
14. CONFIDENCE MODEL
-------------------------------------------------------------------------------

Overall Confidence

Derived from

Verification

Evidence

Consensus

Risk

Market Clarity

Portfolio Quality

Levels

Very High

High

Medium

Low

Very Low

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
21. FINAL AUTHORITY
-------------------------------------------------------------------------------

The Master Decision Engine is the only module authorized to produce

FINAL RECOMMENDATION

All downstream outputs SHALL originate from this engine.

-------------------------------------------------------------------------------
22. END OF DOCUMENT
-------------------------------------------------------------------------------

Atlas Trading OS

MASTER_DECISION_ENGINE.md

Version 1.0.0
```
