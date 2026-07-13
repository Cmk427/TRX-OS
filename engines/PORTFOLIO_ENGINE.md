# TRX Trading OS
## PORTFOLIO_ENGINE.md

```text
Document ID      : TRX-PORT-001
Document Name    : Portfolio Engine
Version          : 1.4.0
Status           : Stable
Classification   : Critical
Dependencies     : CORE_PRINCIPLES.md
                   VERIFICATION_POLICY.md
                   DATA_SOURCE_POLICY.md
                   STATE_MACHINE.md
                   INVESTMENT_POLICY.md
Applies To       : Portfolio Analysis
```

-------------------------------------------------------------------------------
1. PURPOSE
-------------------------------------------------------------------------------

The Portfolio Engine is responsible for evaluating the entire portfolio before
any new capital deployment.

Portfolio protection has higher priority than opportunity discovery.

No Opportunity Scan shall begin before Portfolio Review has completed.

-------------------------------------------------------------------------------
2. OBJECTIVES
-------------------------------------------------------------------------------

The Portfolio Engine SHALL:

• Evaluate portfolio health
• Evaluate position quality
• Measure concentration risk
• Measure sector exposure
• Measure correlation risk
• Determine available buying capacity
• Prioritize capital allocation
• Recommend reductions when necessary

-------------------------------------------------------------------------------
3. REQUIRED INPUTS
-------------------------------------------------------------------------------

Portfolio Positions

Cash Balance

Buying Power

Account Type

Margin Status

Open Orders

Watchlist (Optional)

Risk Preference

Trading Horizon

-------------------------------------------------------------------------------
4. PORTFOLIO HEALTH SCORE
-------------------------------------------------------------------------------

Portfolio Health Score

Range

0 — 100

Suggested Rating

90-100

Excellent

80-89

Healthy

70-79

Acceptable

60-69

Weak

Below 60

Critical

-------------------------------------------------------------------------------
5. HEALTH COMPONENTS
-------------------------------------------------------------------------------

Capital Allocation

20%

Risk Exposure

20%

Diversification

15%

Sector Balance

15%

Liquidity

10%

Cash Flexibility

10%

Correlation

10%

Maximum Score

100

-------------------------------------------------------------------------------
6. POSITION HEALTH SCORE
-------------------------------------------------------------------------------

Each holding receives an independent score.

Evaluation Categories

Trend

Momentum

Relative Strength

Liquidity

Risk

Catalyst

Technical Structure

Drawdown

Confidence

Score

0-100

Suggested Interpretation

90+

Core Holding

80+

Healthy

70+

Watch

60+

Weak

Below 60

Exit Candidate

-------------------------------------------------------------------------------
7. POSITION STATUS
-------------------------------------------------------------------------------

Every holding SHALL belong to one category.

CORE

Healthy position.

HOLD

No action required.

WATCH

Needs monitoring.

REDUCE

Reduce exposure.

EXIT

Close position.

INVALID

Data insufficient.

This status is **preliminary** — assigned at State 06/08, before Committee
(13), Red Team (14), and the Final Risk Gate (15) have reviewed it.
Committee/Red Team/Risk MAY change it; Master Decision (16) publishes the
final value, which may differ from this preliminary one.
`PORTFOLIO_OPTIMIZATION_ENGINE.md` (17) acts only on the State-16 final
value — it never reads this preliminary status directly.

-------------------------------------------------------------------------------
8. POSITION REVIEW
-------------------------------------------------------------------------------

Every holding SHALL answer

Current Trend

Current Momentum

Support

Resistance

Risk Level

Expected Holding Period

Portfolio Weight

Sector

Correlation

Exit Trigger

-------------------------------------------------------------------------------
8A. INVALIDATION-LINKED REVIEW
-------------------------------------------------------------------------------

"Exit Trigger" above SHALL NOT be reinterpreted freely at review time. Each
open position has an Invalidation Condition recorded at entry in that
position's Decision Snapshot (`DECISION_SNAPSHOT_POLICY.md` §2). Position
Review SHALL check current conditions specifically against that originally
recorded condition — not a fresh, potentially more lenient re-reading of
"does this still look okay."

If the recorded Invalidation Condition is breached, this escalates to
`RISK_ENGINE.md` immediately, outside the position's normal review cadence
(`RISK_ENGINE.md` §23: Before Market Open / During Market Hours / After
Market Close / Before Major Events). A breach discovered off-cycle does not
wait for the next scheduled review.

The Decision Snapshot's Outcome field (`DECISION_SNAPSHOT_POLICY.md` §2) is
updated at this point if the position is closed or materially changed as a
result.

-------------------------------------------------------------------------------
9. CONCENTRATION RISK
-------------------------------------------------------------------------------

The system SHALL calculate

Largest Position

Top 3 Positions

Top 5 Positions

Sector Concentration

Single Theme Concentration

If concentration exceeds acceptable limits

Portfolio Risk increases.

-------------------------------------------------------------------------------
9A. PORTFOLIO CONSTRUCTION RULES
-------------------------------------------------------------------------------

"Acceptable limits" above are the default maximum concentration
percentages of total portfolio value defined in `INVESTMENT_POLICY.md` §2
(Single Stock, Sector, Theme — a cross-sector grouping such as "AI" or "EV",
see §10 Correlation Review for how hidden theme overlap is identified —
plus Small-Cap and Options exposure). This document does not restate those
numbers; if this section and `INVESTMENT_POLICY.md` §2 ever disagree, the
policy document wins and the mismatch is a bug here, the same relationship
`PARAMETER_REGISTRY.md` has with every engine it indexes.

Those defaults are modified by the account's Risk Profile
(`RISK_ENGINE.md` §4 Account Risk Profile, per `INVESTMENT_POLICY.md` §8) —
a Conservative profile SHALL use tighter limits, not looser ones, and any
profile-specific override SHALL be recorded explicitly rather than silently
assumed.

The New Position Compatibility check (§17) SHALL reject or flag any
candidate whose addition would breach `INVESTMENT_POLICY.md` §2's limits,
before Risk Engine sizing is even attempted — this is a portfolio-shape
constraint, distinct from and prior to Risk Engine's position-level sizing
math. This document owns the enforcement mechanism (this check); it no
longer owns the numbers themselves. (There is no separate numeric
"Portfolio Fit Score" in this document — the concentration limits above and
§17's qualitative check are the actual mechanism; `SCANNER_ENGINE.md` §5's
"Portfolio fit" weighted component is a different, Scanner-owned number and
SHALL NOT be conflated with this check.)

-------------------------------------------------------------------------------
10. CORRELATION REVIEW
-------------------------------------------------------------------------------

Highly correlated positions SHALL be identified.

Examples

NVDA

AMD

AVGO

TSM

may increase semiconductor exposure.

TSLA

RIVN

LCID

increase EV exposure.

The report SHALL identify hidden concentration.

-------------------------------------------------------------------------------
11. SECTOR EXPOSURE
-------------------------------------------------------------------------------

Portfolio allocation by sector.

Technology

Financial

Healthcare

Energy

Consumer

Industrial

Utilities

Communication

Real Estate

Materials

Cash

Output

Pie chart optional.

Risk commentary required.

-------------------------------------------------------------------------------
12. LIQUIDITY REVIEW
-------------------------------------------------------------------------------

Every position SHALL be reviewed for

Average Volume

Spread

Exit Difficulty

Market Hours

Extended Hours Liquidity

Low-liquidity positions receive lower quality scores.

-------------------------------------------------------------------------------
13. CAPITAL ALLOCATION
-------------------------------------------------------------------------------

Capital SHALL be classified.

Cash

Invested Capital

Margin

Reserved Capital

Available Buying Power

Recommended Cash Reserve

-------------------------------------------------------------------------------
14. BUYING POWER MODEL
-------------------------------------------------------------------------------

Buying Power SHALL NOT equal Recommended Deployment.

Recommended deployment depends on

Market Regime

Portfolio Health

Risk

Macro Conditions

Volatility

-------------------------------------------------------------------------------
15. EXIT PRIORITY
-------------------------------------------------------------------------------

Every position receives an Exit Priority.

Priority 1

Immediate Exit

Priority 2

Reduce Soon

Priority 3

Monitor

Priority 4

Healthy

Priority 5

Core Position

-------------------------------------------------------------------------------
16. CAPITAL REALLOCATION
-------------------------------------------------------------------------------

If positions are reduced

capital SHALL be classified.

Immediate Redeployment

Hold Cash

Reserve for Earnings

Reserve for Volatility

Wait for Better Setup

-------------------------------------------------------------------------------
17. NEW POSITION COMPATIBILITY
-------------------------------------------------------------------------------

Every new position SHALL answer

Does this duplicate an existing position?

Does this increase concentration?

Does this increase portfolio quality?

Does this reduce flexibility?

If not

Recommendation quality decreases.

-------------------------------------------------------------------------------
18. PORTFOLIO ACTIONS
-------------------------------------------------------------------------------

Available actions — this list is now aligned to `STATE_MACHINE.md` State 08's
required result for existing-position review: **HOLD, REDUCE, EXIT,
WATCH**. This document previously also listed BUY, ADD, TRIM, ROTATE, and
NO ACTION as if they were separate standalone actions here — they are not,
and listing them created exactly the kind of unreconciled-outcome-vocabulary
problem `MASTER_DECISION_ENGINE.md` §9 was corrected to remove (ADD/ROTATE
dropped from its own Recommendation States for the same reason). Reconciled:

HOLD

REDUCE

EXIT

WATCH

"BUY" (opening a new position) is out of scope for this section entirely —
it belongs to Scanner/Decision Engine's candidate pipeline for *new*
opportunities (States 09–12), not to existing-position review. "ADD" and
"TRIM" describe finer-grained sizing nuances of REDUCE (or, for ADD, a new
risk decision that must go through the full new-candidate pipeline, not an
existing-position shortcut) — they are not separate outcomes. "ROTATE"
(exiting one holding to fund another) is expressed as the paired EXIT (this
position) and, separately, EXECUTE (the new position) once that candidate
completes its own full pipeline — never a single atomic action here.

Like §7's Position Status, every action assigned here is the **preliminary**
State 06/08 assessment, not the final one. This document's own §15 Exit
Priority (a 1–5 urgency ranking of existing positions, used pre-decision) is
a different concept from `EXECUTION_ENGINE.md` §3G's Execution Priority
(P1–P3, used post-decision to order actually-approved trades) — the two
share the word "priority" but rank different things at different pipeline
stages and SHALL NOT be conflated.

-------------------------------------------------------------------------------
19. PORTFOLIO PRIORITY
-------------------------------------------------------------------------------

The capital-priority order (Protect Capital → Reduce Weak Positions →
Strengthen Strong Positions → Increase Cash if below the minimum → Deploy
New Capital) is defined once, canonically, in `INVESTMENT_POLICY.md` §6.
This document does not restate it — `PORTFOLIO_OPTIMIZATION_ENGINE.md` §11
follows the identical cross-reference, so the order exists in exactly one
place.

-------------------------------------------------------------------------------
20. PORTFOLIO DASHBOARD
-------------------------------------------------------------------------------

Every report SHALL include

Portfolio Health Score

Overall Risk

Cash %

Invested %

Sector Allocation

Largest Position

Weakest Position

Strongest Position

Exit Candidates

Buying Capacity

Recommended Actions

-------------------------------------------------------------------------------
21. FAILURE CONDITIONS
-------------------------------------------------------------------------------

The Portfolio Engine SHALL stop if

Portfolio data unreadable

Ticker invalid

Position size invalid

Cash unavailable

Buying power unavailable

User clarification required before continuing.

-------------------------------------------------------------------------------
22. SUCCESS CRITERIA
-------------------------------------------------------------------------------

A successful Portfolio Review SHALL

Protect capital

Identify hidden risk

Improve allocation

Improve decision quality

Support future opportunity selection

-------------------------------------------------------------------------------
End of Document

TRX Trading OS

PORTFOLIO_ENGINE.md

Version 1.3.0
```
