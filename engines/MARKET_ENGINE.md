# TRX Trading OS
## MARKET_ENGINE.md

```text
Document ID      : TRX-MKT-001
Document Name    : Market Engine
Version          : 1.0.0
Status           : Stable
Classification   : Critical
Dependencies      : CORE_PRINCIPLES.md
                    STATE_MACHINE.md
                    VERIFICATION_POLICY.md
                    DATA_SOURCE_POLICY.md
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
15A. REGIME TRANSITION CONTROL
-------------------------------------------------------------------------------

A Regime Transition is detected whenever this session's Market Score band
(Exceptional/Strong/Healthy/Weak/Defensive, per §15) differs from the prior
session's band — in either direction, not only downgrades. This is a
mechanical, band-crossing detection, not a subjective "does it feel
different" judgment.

On a detected transition, the current session becomes a mandatory
Transition Period. During a Transition Period:

Recommended exposure is capped one Risk Multiplier tier (§16) more
conservative than the raw Market Score alone would imply — e.g. a Market
Score newly at 82 (raw tier: 75% per §16) is capped at the 70+ tier's 50%
during the Transition Period.

Every Playbook assignment made under the prior regime SHALL be re-verified
against current conditions at `PLAYBOOK_ENGINE.md` State 10 before being
reused — a playbook match from before the transition is not carried forward
automatically, because regime-dependent setups (e.g. PB-010 Defense
Rotation assumes risk-off; PB-001 Momentum Breakout assumes a supportive
regime) may no longer fit.

The Transition Period ends, and the one-tier exposure cap lifts, at the next
session in which the Market Score band matches the current session's band
(i.e., the band has held for two consecutive sessions).

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

TRX Trading OS

MARKET_ENGINE.md

Version 1.0.0
```
