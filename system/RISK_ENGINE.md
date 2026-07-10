# Atlas Trading OS
## RISK_ENGINE.md

```text
Document ID      : ATO-RISK-001
Document Name    : Risk Engine
Version          : 1.0.0
Status           : Stable
Classification   : Critical
Dependencies     : MARKET_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   DECISION_ENGINE.md
                   RED_TEAM_ENGINE.md
Applies To       : Entire Trading System
```

-------------------------------------------------------------------------------
1. PURPOSE
-------------------------------------------------------------------------------

The Risk Engine is the highest-priority execution engine in Atlas Trading OS.

No expected return shall override unacceptable risk.

Every recommendation shall satisfy Risk Engine requirements before execution.

-------------------------------------------------------------------------------
2. RISK HIERARCHY
-------------------------------------------------------------------------------

Priority Order

1. Capital Preservation

↓

2. Portfolio Survival

↓

3. Risk Control

↓

4. Capital Growth

↓

5. Profit Maximization

-------------------------------------------------------------------------------
3. RISK BUDGET
-------------------------------------------------------------------------------

Every account SHALL define

Maximum Portfolio Risk

Maximum Position Risk

Daily Risk Budget

Weekly Risk Budget

Monthly Risk Budget

The Risk Engine SHALL reject recommendations exceeding any budget.

-------------------------------------------------------------------------------
4. ACCOUNT RISK PROFILE
-------------------------------------------------------------------------------

Risk Profiles

Conservative

Balanced

Growth

Aggressive

Maximum Aggressive

Each profile modifies

Maximum Position Size

Maximum Portfolio Exposure

Daily Loss Limit

-------------------------------------------------------------------------------
5. POSITION RISK
-------------------------------------------------------------------------------

Each position SHALL calculate

Entry Price

Stop Price

Maximum Loss

Risk %

ATR Risk

Gap Risk

Liquidity Risk

Expected Holding Period

-------------------------------------------------------------------------------
6. POSITION SIZING
-------------------------------------------------------------------------------

Position size SHALL depend upon

Portfolio Value

Risk Budget

Stop Distance

Current Volatility

Liquidity

Correlation

Portfolio Heat

Position size SHALL NOT depend upon conviction alone.

-------------------------------------------------------------------------------
7. PORTFOLIO HEAT
-------------------------------------------------------------------------------

Portfolio Heat measures

Total capital simultaneously exposed to loss.

Heat Levels

0–20%

Very Low

20–40%

Normal

40–60%

Elevated

60–80%

High

Above 80%

Critical

-------------------------------------------------------------------------------
8. POSITION HEAT
-------------------------------------------------------------------------------

Every position receives

Heat Score

Based upon

Position Size

Volatility

Liquidity

Gap Risk

-------------------------------------------------------------------------------
9. DRAWDOWN CONTROL
-------------------------------------------------------------------------------

The system SHALL monitor

Current Drawdown

Maximum Drawdown

Recovery Progress

Drawdown Levels

0-5%

Normal

5-10%

Caution

10-15%

Defensive

15-20%

Recovery Mode

Above 20%

Capital Protection Mode

-------------------------------------------------------------------------------
10. DAILY LOSS LIMIT
-------------------------------------------------------------------------------

Maximum Daily Loss

Configured by account profile.

Upon breach

No new positions

Close only

-------------------------------------------------------------------------------
11. WEEKLY LOSS LIMIT
-------------------------------------------------------------------------------

Upon exceeding weekly limit

Reduce

Position Size

Exposure

Trade Frequency

-------------------------------------------------------------------------------
12. MONTHLY LOSS LIMIT
-------------------------------------------------------------------------------

Upon breach

Mandatory Recovery Mode

New aggressive trades prohibited.

-------------------------------------------------------------------------------
13. VOLATILITY ADJUSTMENT
-------------------------------------------------------------------------------

Higher volatility SHALL reduce

Position Size

Holding Period

Risk Budget

Lower volatility MAY increase allocation.

-------------------------------------------------------------------------------
14. CORRELATION RISK
-------------------------------------------------------------------------------

Measure correlation between

Positions

Sectors

Themes

Indexes

High correlation reduces available risk budget.

-------------------------------------------------------------------------------
15. GAP RISK
-------------------------------------------------------------------------------

Gap Risk SHALL increase before

Earnings

FOMC

CPI

PPI

Major News

Weekend Holding

-------------------------------------------------------------------------------
16. EVENT RISK
-------------------------------------------------------------------------------

The system SHALL classify

Upcoming events

Low

Medium

High

Extreme

Extreme Event Risk

↓

Reduce exposure

-------------------------------------------------------------------------------
17. STOP LOSS POLICY
-------------------------------------------------------------------------------

Every recommendation SHALL include

Initial Stop

Invalidation Level

Review Level

Emergency Exit

Stop placement SHALL be based on

Market Structure

ATR

Support

Not arbitrary percentages.

-------------------------------------------------------------------------------
18. TAKE PROFIT POLICY
-------------------------------------------------------------------------------

Profit targets SHALL consider

Resistance

Expected Move

Risk Reward

Volatility

Partial exits encouraged when appropriate.

-------------------------------------------------------------------------------
19. RISK / REWARD
-------------------------------------------------------------------------------

Minimum

2 : 1

Preferred

3 : 1

Exceptional

4 : 1+

Below minimum

Reject.

-------------------------------------------------------------------------------
20. CAPITAL ALLOCATION
-------------------------------------------------------------------------------

Capital Categories

Reserved Cash

Working Capital

High Conviction Capital

Defensive Capital

Emergency Reserve

-------------------------------------------------------------------------------
21. RECOVERY MODE
-------------------------------------------------------------------------------

Recovery Mode activates after

Material drawdown

Rules

Reduce size

Reduce frequency

Higher evidence requirement

Higher confidence requirement

-------------------------------------------------------------------------------
22. KILL SWITCH
-------------------------------------------------------------------------------

Trading SHALL stop immediately if

Critical market disruption

Portfolio loss beyond limit

Liquidity collapse

Data verification failure

Extreme macro uncertainty

Kill Switch overrides all engines.

-------------------------------------------------------------------------------
23. POSITION REVIEW FREQUENCY
-------------------------------------------------------------------------------

Every open position SHALL be reviewed

Before Market Open

During Market Hours

After Market Close

Before Major Events

-------------------------------------------------------------------------------
24. RISK SCORE
-------------------------------------------------------------------------------

Every recommendation receives

Risk Score

100

Minimal Risk

90

Low Risk

80

Controlled Risk

70

Elevated Risk

Below 70

Reject

-------------------------------------------------------------------------------
25. RISK REPORT
-------------------------------------------------------------------------------

Every report SHALL include

Portfolio Heat

Largest Risk

Largest Position

Cash %

Sector Concentration

Correlation Risk

Gap Risk

Maximum Expected Loss

Risk Budget Remaining

-------------------------------------------------------------------------------
26. FAILURE CONDITIONS
-------------------------------------------------------------------------------

Reject recommendation if

Position size unknown

Stop cannot be defined

Risk cannot be measured

Liquidity unacceptable

Portfolio exposure excessive

-------------------------------------------------------------------------------
27. SUCCESS CRITERIA
-------------------------------------------------------------------------------

The Risk Engine succeeds when

Capital survives adverse conditions

Losses remain controlled

Portfolio remains flexible

Risk remains measurable

Decision quality improves

-------------------------------------------------------------------------------
28. FINAL AUTHORITY
-------------------------------------------------------------------------------

The Risk Engine has absolute veto authority.

If Risk Engine rejects a recommendation,

no downstream module may override the decision.

-------------------------------------------------------------------------------
End of Document

Atlas Trading OS

RISK_ENGINE.md

Version 1.0.0
```
