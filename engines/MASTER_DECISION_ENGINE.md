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

The Master Decision Engine (MDE) is the central decision authority of
TRX Trading OS.

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
21. FINAL OUTPUT AUTHORITY
-------------------------------------------------------------------------------

The Master Decision Engine is the only module authorized to produce

FINAL RECOMMENDATION

All downstream outputs SHALL originate from this engine.

This authority is limited to integration and publication; it is not authority to
override the constraints named in `CONSTITUTION.md`.

-------------------------------------------------------------------------------
22. END OF DOCUMENT
-------------------------------------------------------------------------------

TRX Trading OS

MASTER_DECISION_ENGINE.md

Version 1.0.0
```
