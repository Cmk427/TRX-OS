# Atlas Trading OS
## OPTIONS_ENGINE.md

```text
Document ID      : ATO-OPT-001
Document Name    : Options Engine
Version          : 1.0.0
Status           : Stable
Classification   : Critical
Dependencies     : MARKET_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   SCANNER_ENGINE.md
                   DECISION_ENGINE.md
                   VERIFICATION_POLICY.md
Applies To       : Buy Call Strategies
```

-------------------------------------------------------------------------------
1. PURPOSE
-------------------------------------------------------------------------------

The Options Engine evaluates whether purchasing Call Options provides a
superior risk-adjusted opportunity compared to purchasing the underlying stock.

The objective is NOT to maximize leverage.

The objective is to maximize asymmetric return while maintaining controlled
portfolio risk.

-------------------------------------------------------------------------------
2. SCOPE
-------------------------------------------------------------------------------

Supported

✓ Long Call

Future Versions

Covered Call

Cash Secured Put

Vertical Spread

Calendar Spread

Diagonal Spread

LEAPS

-------------------------------------------------------------------------------
3. REQUIRED INPUTS
-------------------------------------------------------------------------------

Underlying Price

Option Chain

Expiration Dates

Strike Prices

Bid

Ask

Open Interest

Volume

Implied Volatility

Portfolio Information

Market Regime

-------------------------------------------------------------------------------
4. OPTION ELIGIBILITY
-------------------------------------------------------------------------------

Before analysis the contract SHALL satisfy

Tradable

Reasonable Spread

Sufficient Open Interest

Sufficient Daily Volume

Current Market Data Verified

Otherwise

Reject

-------------------------------------------------------------------------------
5. UNDERLYING QUALITY SCORE
-------------------------------------------------------------------------------

The underlying asset SHALL first receive

Opportunity Score

from Scanner Engine.

Minimum

80

Otherwise

No option recommendation.

-------------------------------------------------------------------------------
6. DELTA MODEL
-------------------------------------------------------------------------------

Evaluate

Expected price participation.

Preferred Delta

0.55

to

0.75

Higher Delta

Higher capital requirement

Lower leverage

Lower Gamma

Lower Theta

-------------------------------------------------------------------------------
7. GAMMA MODEL
-------------------------------------------------------------------------------

Evaluate

Gamma sensitivity

Acceleration potential

High Gamma

Higher reward

Higher risk

-------------------------------------------------------------------------------
8. THETA MODEL
-------------------------------------------------------------------------------

Evaluate

Daily time decay.

Preferred

Lowest acceptable Theta
for intended holding period.

Holding period SHALL remain compatible with Theta loss.

-------------------------------------------------------------------------------
9. VEGA MODEL
-------------------------------------------------------------------------------

Evaluate

Sensitivity to implied volatility.

If IV expansion expected

Higher Vega acceptable.

If IV contraction expected

Reduce recommendation quality.

-------------------------------------------------------------------------------
10. IMPLIED VOLATILITY MODEL
-------------------------------------------------------------------------------

Evaluate

Current IV

IV Rank

IV Percentile

Historical IV

Relative IV

High IV

Premium expensive.

Low IV

Premium attractive.

-------------------------------------------------------------------------------
11. EXPIRATION ENGINE
-------------------------------------------------------------------------------

Select expiration using

Expected Holding Period

Expected Catalyst Date

Theta

Liquidity

Preferred

Avoid excessive Theta decay.

-------------------------------------------------------------------------------
12. STRIKE ENGINE
-------------------------------------------------------------------------------

Evaluate

ATM

ITM

OTM

Selection based on

Probability

Capital Efficiency

Expected Move

Portfolio Risk

-------------------------------------------------------------------------------
13. LIQUIDITY MODEL
-------------------------------------------------------------------------------

Evaluate

Bid Ask Spread

Open Interest

Daily Option Volume

Execution Difficulty

Liquidity Score

0-100

-------------------------------------------------------------------------------
14. OPTION RISK MODEL
-------------------------------------------------------------------------------

Measure

Maximum Premium Risk

Probability of Total Loss

Volatility Risk

Gap Risk

Liquidity Risk

Time Risk

-------------------------------------------------------------------------------
15. REWARD MODEL
-------------------------------------------------------------------------------

Estimate

Expected Return

Break-even

Target Price

Risk Reward Ratio

Probability Range

-------------------------------------------------------------------------------
16. OPTION QUALITY SCORE
-------------------------------------------------------------------------------

Components

Underlying Quality

30%

Liquidity

15%

IV Environment

15%

Greeks

15%

Risk

15%

Catalyst

10%

Maximum

100

-------------------------------------------------------------------------------
17. QUALITY CLASSIFICATION
-------------------------------------------------------------------------------

95+

Institutional Grade

90+

Excellent

85+

Strong

80+

Qualified

Below 80

Reject

-------------------------------------------------------------------------------
18. EXECUTION PLAN
-------------------------------------------------------------------------------

Each recommendation SHALL include

Underlying

Strike

Expiration

Premium

Maximum Risk

Break-even

Profit Target

Stop Criteria

Holding Period

-------------------------------------------------------------------------------
19. EXIT STRATEGY
-------------------------------------------------------------------------------

Every position SHALL define

Profit Exit

Time Exit

Stop Exit

Catalyst Exit

Volatility Exit

No option recommendation may omit an exit plan.

-------------------------------------------------------------------------------
20. INVALIDATION CONDITIONS
-------------------------------------------------------------------------------

Recommendation becomes invalid if

Underlying thesis changes

Market regime deteriorates

Catalyst cancelled

Liquidity disappears

Volatility changes materially

-------------------------------------------------------------------------------
21. PROHIBITED BEHAVIOUR
-------------------------------------------------------------------------------

The engine SHALL NOT

Recommend illiquid contracts

Ignore Theta

Ignore IV

Ignore Bid Ask Spread

Recommend contracts with unknown pricing

Recommend contracts without verified market data

-------------------------------------------------------------------------------
22. OUTPUT FORMAT
-------------------------------------------------------------------------------

Every recommendation SHALL include

Underlying

Current Price

Option Contract

Strike

Expiration

Premium

Delta

Gamma

Theta

Vega

IV Rank

Liquidity Score

Option Quality Score

Bull Thesis

Bear Thesis

Maximum Risk

Expected Holding Period

Execution Plan

-------------------------------------------------------------------------------
23. SUCCESS CRITERIA
-------------------------------------------------------------------------------

A successful recommendation SHALL

Provide asymmetric upside

Maintain acceptable downside

Use liquid contracts

Fit the existing portfolio

Remain fully auditable

-------------------------------------------------------------------------------
End of Document

Atlas Trading OS

OPTIONS_ENGINE.md

Version 1.0.0
```
