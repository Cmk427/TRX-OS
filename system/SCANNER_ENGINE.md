# Atlas Trading OS
## SCANNER_ENGINE.md

```text
Document ID      : ATO-SCN-001
Document Name    : Scanner Engine
Version          : 1.0.0
Status           : Stable
Classification   : Critical
Dependencies     : MARKET_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   VERIFICATION_POLICY.md
                   DECISION_ENGINE.md
Applies To       : Opportunity Discovery
```

-------------------------------------------------------------------------------
1. PURPOSE
-------------------------------------------------------------------------------

The Scanner Engine discovers and ranks trading opportunities that satisfy
Atlas Trading OS quality standards.

The objective is NOT to find the most stocks.

The objective is to identify the highest-quality opportunities after market,
portfolio and risk evaluation.

-------------------------------------------------------------------------------
2. EXECUTION ORDER
-------------------------------------------------------------------------------

Scanner Engine SHALL execute ONLY AFTER

✓ Market Engine

✓ Portfolio Engine

✓ Risk Engine

If any prerequisite fails,

Scanner Engine SHALL NOT execute.

-------------------------------------------------------------------------------
3. SCAN UNIVERSE
-------------------------------------------------------------------------------

Supported Assets

• US Stocks

• US ETFs

• Buy Call Candidates

Default Exclusions

• OTC

• Pink Sheets

• Delisted Securities

• Extremely Illiquid Stocks

• Average Daily Dollar Volume below configured threshold

-------------------------------------------------------------------------------
4. INITIAL FILTER
-------------------------------------------------------------------------------

Every candidate SHALL pass

✓ Tradable

✓ Sufficient Liquidity

✓ Normal Spread

✓ Valid Market Data

✓ No Trading Halt

Failure

Rejected

-------------------------------------------------------------------------------
5. MOMENTUM MODEL
-------------------------------------------------------------------------------

Evaluate

Price Momentum

Relative Momentum

Gap Strength

Trend Continuation

Breakout Quality

Momentum Score

0-100

Weight

20%

-------------------------------------------------------------------------------
6. RELATIVE STRENGTH MODEL
-------------------------------------------------------------------------------

Compare candidate against

SPY

QQQ

Sector ETF

Industry Group

Relative Strength Score

0-100

Weight

15%

-------------------------------------------------------------------------------
7. VOLUME EXPANSION MODEL
-------------------------------------------------------------------------------

Evaluate

Current Volume

Relative Volume (RVOL)

Average Daily Volume

Dollar Volume

Accumulation

Distribution

Volume Score

0-100

Weight

15%

-------------------------------------------------------------------------------
8. INSTITUTIONAL PARTICIPATION MODEL
-------------------------------------------------------------------------------

Evaluate

Large Block Activity

Dark Pool Activity (if available)

Institutional Ownership

Volume Quality

Price Acceptance

Institutional Score

0-100

Weight

10%

-------------------------------------------------------------------------------
9. CATALYST MODEL
-------------------------------------------------------------------------------

Evaluate

Earnings

Guidance

FDA

Contracts

Product Launch

AI

Government Awards

M&A

Analyst Upgrades

Macro Tailwind

Catalyst Score

0-100

Weight

15%

-------------------------------------------------------------------------------
10. TECHNICAL STRUCTURE MODEL
-------------------------------------------------------------------------------

Evaluate

Trend

Support

Resistance

Moving Average Alignment

Breakout Pattern

Base Structure

ATR

VWAP Position

Technical Score

0-100

Weight

15%

-------------------------------------------------------------------------------
11. LIQUIDITY MODEL
-------------------------------------------------------------------------------

Evaluate

Average Volume

Dollar Volume

Bid Ask Spread

Option Liquidity

Exit Difficulty

Liquidity Score

0-100

Weight

5%

-------------------------------------------------------------------------------
12. RISK MODEL
-------------------------------------------------------------------------------

Evaluate

ATR

Gap Risk

News Risk

Volatility

Correlation

Sector Risk

Maximum Drawdown

Risk Score

0-100

Lower risk

Higher score

Weight

5%

-------------------------------------------------------------------------------
13. PORTFOLIO FIT MODEL
-------------------------------------------------------------------------------

Evaluate

Sector Balance

Correlation

Capital Requirement

Position Size

Cash Usage

Existing Exposure

Portfolio Fit Score

0-100

Weight

10%

-------------------------------------------------------------------------------
14. OPPORTUNITY SCORE
-------------------------------------------------------------------------------

Overall Opportunity Score

Formula

Momentum

20%

Relative Strength

15%

Volume

15%

Institutional

10%

Catalyst

15%

Technical

15%

Liquidity

5%

Risk

5%

Portfolio Fit

10%

Maximum

100

-------------------------------------------------------------------------------
15. QUALITY CLASSIFICATION
-------------------------------------------------------------------------------

95-100

Institutional Grade

90-94

Elite

85-89

High Quality

80-84

Qualified

75-79

Watchlist

Below 75

Reject

-------------------------------------------------------------------------------
16. RANKING ENGINE
-------------------------------------------------------------------------------

Candidates SHALL be ranked by

Opportunity Score

Highest first

Only highest-ranked opportunities proceed.

-------------------------------------------------------------------------------
17. EXECUTION ELIGIBILITY
-------------------------------------------------------------------------------

A candidate SHALL be eligible only if

Market supports risk

Portfolio supports allocation

Risk acceptable

Liquidity sufficient

Catalyst present

Data verified

Otherwise

Status

NOT ELIGIBLE

-------------------------------------------------------------------------------
18. OUTPUT FORMAT
-------------------------------------------------------------------------------

Each candidate SHALL include

Ticker

Sector

Industry

Current Price

Opportunity Score

Confidence

Momentum

Catalyst

Risk

Entry Zone

Stop

Target

Holding Period

Invalidation

-------------------------------------------------------------------------------
19. REJECTION RULES
-------------------------------------------------------------------------------

Reject candidates when

No catalyst

Weak liquidity

Poor structure

High spread

Low confidence

Weak market alignment

Poor portfolio fit

-------------------------------------------------------------------------------
20. TOP OPPORTUNITY
-------------------------------------------------------------------------------

The highest-ranked candidate SHALL include

Investment Thesis

Bull Case

Bear Case

Catalyst

Risk Factors

Execution Plan

Exit Conditions

Confidence

-------------------------------------------------------------------------------
21. WATCHLIST
-------------------------------------------------------------------------------

Candidates that fail execution but remain attractive SHALL enter Watchlist.

Each Watchlist item SHALL specify

Reason

Required Trigger

Review Date

-------------------------------------------------------------------------------
22. FAILURE CONDITIONS
-------------------------------------------------------------------------------

Scanner SHALL terminate if

Market Engine failed

Portfolio Engine failed

Risk Engine failed

Market data unavailable

Required verification incomplete

-------------------------------------------------------------------------------
23. SUCCESS CRITERIA
-------------------------------------------------------------------------------

A successful Scanner Engine SHALL

Identify high-quality opportunities

Avoid unnecessary trades

Filter weak candidates

Prioritize institutional-quality setups

Remain fully auditable

-------------------------------------------------------------------------------
End of Document

Atlas Trading OS

SCANNER_ENGINE.md

Version 1.0.0
```
