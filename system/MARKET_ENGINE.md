# Atlas Trading OS
## MARKET_ENGINE.md

```text
Document ID      : ATO-MKT-001
Document Name    : Market Engine
Version          : 1.0.0
Status           : Stable
Classification   : Critical
Dependencies      : CORE_PRINCIPLES.md
                    STATE_MACHINE.md
                    VERIFICATION_POLICY.md
                    DECISION_ENGINE.md
Applies To       : Entire System
```

-------------------------------------------------------------------------------
1. PURPOSE
-------------------------------------------------------------------------------

The Market Engine determines whether market conditions support taking risk.

The Market Engine SHALL execute before:

• Portfolio Engine
• Scanner Engine
• Options Engine
• Execution Engine

No BUY recommendation may bypass Market Engine.

-------------------------------------------------------------------------------
2. PRIMARY OBJECTIVE
-------------------------------------------------------------------------------

Determine:

• Whether capital should be deployed
• How aggressively capital should be deployed
• Which sectors deserve attention
• Whether defensive positioning is preferred

-------------------------------------------------------------------------------
3. MARKET REGIME
-------------------------------------------------------------------------------

Every session SHALL receive one Market Regime.

Regimes

STRONG RISK ON

RISK ON

NEUTRAL

RISK OFF

STRONG RISK OFF

Every downstream module SHALL adjust behaviour according to
the current regime.

-------------------------------------------------------------------------------
4. REGIME INPUTS
-------------------------------------------------------------------------------

The Market Engine SHALL evaluate

SPY

QQQ

IWM

DIA

VIX

Treasury Yield

US Dollar

Market Breadth

Sector Rotation

Macro Events

Market Liquidity

-------------------------------------------------------------------------------
5. MARKET TREND
-------------------------------------------------------------------------------

Trend Categories

Strong Bull

Bull

Sideways

Bear

Strong Bear

Trend shall consider

Higher Highs

Higher Lows

Moving Average Structure

Volume Confirmation

Market Breadth

-------------------------------------------------------------------------------
6. MARKET STAGE
-------------------------------------------------------------------------------

Every market SHALL be classified.

Accumulation

Markup

Distribution

Markdown

Transition

Unknown

-------------------------------------------------------------------------------
7. VOLATILITY ENGINE
-------------------------------------------------------------------------------

Inputs

VIX

Average True Range

Gap Frequency

Intraday Range

Volatility Rating

LOW

NORMAL

HIGH

EXTREME

High volatility reduces recommended position size.

-------------------------------------------------------------------------------
8. MARKET BREADTH
-------------------------------------------------------------------------------

Review

Advancers

Decliners

New Highs

New Lows

Up Volume

Down Volume

Breadth Rating

Very Strong

Strong

Neutral

Weak

Very Weak

-------------------------------------------------------------------------------
9. LIQUIDITY ENGINE
-------------------------------------------------------------------------------

Evaluate

Average Volume

Index Liquidity

Bid Ask Spread

Market Participation

Institutional Activity

Output

Liquidity Score

0-100

-------------------------------------------------------------------------------
10. SECTOR ROTATION
-------------------------------------------------------------------------------

Rank sectors

Technology

Semiconductors

Financial

Healthcare

Industrials

Consumer

Energy

Utilities

Communication

Materials

Real Estate

Each sector receives

Momentum Score

Relative Strength

Capital Flow Rating

-------------------------------------------------------------------------------
11. MARKET MOMENTUM
-------------------------------------------------------------------------------

Evaluate

Trend Persistence

Breakout Success Rate

Gap Continuation

Relative Strength

Momentum Rating

Strong

Moderate

Weak

-------------------------------------------------------------------------------
12. MACRO RISK
-------------------------------------------------------------------------------

Evaluate

FOMC

CPI

PPI

PCE

GDP

NFP

Bond Auction

Major Earnings

Geopolitical Events

Macro Risk

LOW

NORMAL

HIGH

EXTREME

-------------------------------------------------------------------------------
13. EVENT RISK
-------------------------------------------------------------------------------

Upcoming Events

Market Close

Earnings

Fed Speakers

Options Expiration

Triple Witching

Major Economic Releases

Event Risk shall affect

Position Size

Holding Period

Recommended Exposure

-------------------------------------------------------------------------------
14. MARKET CONFIDENCE
-------------------------------------------------------------------------------

Overall Market Confidence

Score

0-100

Components

Trend

25%

Breadth

20%

Liquidity

15%

Volatility

15%

Macro

15%

Sector Rotation

10%

-------------------------------------------------------------------------------
15. MARKET SCORE
-------------------------------------------------------------------------------

Final Market Score

90+

Exceptional

80+

Strong

70+

Healthy

60+

Weak

Below 60

Defensive

-------------------------------------------------------------------------------
16. RISK MULTIPLIER
-------------------------------------------------------------------------------

Market Score

Recommended Risk

90+

100%

80+

75%

70+

50%

60+

25%

Below 60

No New Position

-------------------------------------------------------------------------------
17. CAPITAL DEPLOYMENT
-------------------------------------------------------------------------------

Recommended Cash Deployment

Very Aggressive

Aggressive

Balanced

Defensive

Cash Only

Recommendation depends on

Market Score

Macro Risk

Volatility

Liquidity

-------------------------------------------------------------------------------
18. KILL SWITCH
-------------------------------------------------------------------------------

The system SHALL suspend aggressive buying if

Market Score below minimum

Extreme VIX

Major macro event pending

Exchange disruption

Liquidity collapse

Verified data unavailable

Output

NO NEW POSITION

-------------------------------------------------------------------------------
19. MARKET OUTPUT
-------------------------------------------------------------------------------

Every report SHALL include

Market Regime

Trend

Stage

Breadth

Volatility

Liquidity

Macro Risk

Sector Leaders

Sector Laggards

Market Confidence

Recommended Exposure

-------------------------------------------------------------------------------
20. FAILURE CONDITIONS
-------------------------------------------------------------------------------

Stop analysis if

Market data unavailable

Index verification fails

Macro verification fails

Price verification fails

Inform the user.

Do not fabricate market conditions.

-------------------------------------------------------------------------------
21. SUCCESS CRITERIA
-------------------------------------------------------------------------------

A successful Market Engine SHALL

Correctly classify market conditions

Protect capital during adverse environments

Increase exposure only when justified

Support downstream decision quality

-------------------------------------------------------------------------------
End of Document

Atlas Trading OS

MARKET_ENGINE.md

Version 1.0.0
```
