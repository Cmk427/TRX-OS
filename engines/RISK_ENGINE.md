# TRX Trading OS
## RISK_ENGINE.md

```text
Document ID      : TRX-RISK-001
Document Name    : Risk Engine
Version          : 1.0.0
Status           : Stable
Classification   : Critical
Dependencies     : MARKET_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   STATE_MACHINE.md
                   CONSTITUTION.md
                   DATA_SOURCE_POLICY.md
Applies To       : Entire Trading System
```

-------------------------------------------------------------------------------
1. PURPOSE
-------------------------------------------------------------------------------

The Risk Engine is the highest-priority risk-control engine in TRX Trading OS.

No expected return shall override unacceptable risk.

Every recommendation shall satisfy Risk Engine requirements before execution.

The State Machine invokes this engine twice: a Preliminary Risk Gate before new
opportunity scanning and a Final Risk Gate after Red Team review. A rejection at
either gate is binding; the Master Decision Engine records, but cannot override,
the resulting NO TRADE, REDUCE, or EXIT outcome.

This engine holds **Constraint Authority**, not Publication Authority, per
`CONSTITUTION.md` §4A. It never integrates outputs and never publishes a
final report — it only narrows what the Master Decision Engine is
constitutionally permitted to publish. See §28 Veto Authority.

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
7A. PORTFOLIO HEAT FORMULA
-------------------------------------------------------------------------------

Portfolio Heat is a defined, computable number, not a policy label:

Portfolio Heat =
  Sum over all open positions of (Maximum Loss to Stop for that position)
  ÷ Total Portfolio Value

Maximum Loss to Stop for a position =
  Position Size (shares or contracts) × (Entry Price − Stop Price),
  expressed as a positive loss amount; for an options position use
  Premium at Risk instead of Entry − Stop (see §28A Options Risk Bridge).

This is a stop-loss-based exposure measure, not a raw market-exposure
percentage and not a statistical Value-at-Risk model. Two rejected
alternatives, recorded here so they are not silently reintroduced:

- Raw market exposure % (position value ÷ portfolio value, ignoring the
  stop) is rejected because it cannot distinguish a well-stopped position
  from an unstopped one of the same size.
- A VaR/CVaR model is rejected for v1.0 because it requires volatility and
  correlation assumptions the system does not verify or source, which would
  introduce exactly the false precision `CORE_PRINCIPLES.md` prohibits. It
  may be reconsidered in a future version only with a documented data source
  and methodology.

The 0–20% / 20–40% / 40–60% / 60–80% / 80%+ bands in §7 are the labels for
this number, not an independent metric.

If any position's Stop Price or Position Size is `UNKNOWN`, Portfolio Heat
cannot be computed and the Risk Engine SHALL treat this the same as any
other unmeasurable risk — see §26A Missing Risk Input Rule.

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
8A. POSITION HEAT SCORE FORMULA
-------------------------------------------------------------------------------

Position Heat Score (0–100, higher = more heat) is a weighted combination of
four normalised (0–100) sub-scores:

| Component | Weight | What it measures |
|---|---:|---|
| Position Risk % | 40% | This position's Maximum Loss to Stop as a % of its own contribution to Portfolio Heat |
| Volatility percentile | 25% | The instrument's realised/implied volatility percentile versus its own history |
| Liquidity score (inverted) | 20% | Lower liquidity (wide spread, thin volume/OI) raises heat |
| Gap Risk score | 15% | Proximity to earnings, macro releases, or other scheduled gap events per §15/§16 |

Position Risk % carries the largest weight because it is the only
stop-anchored, non-inferred input; the other three are context modifiers,
not substitutes for a defined stop. If Position Risk % is `UNKNOWN` (no
stop), the Position Heat Score cannot be computed — see §26A.

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
24A. RISK SCORE FORMULA
-------------------------------------------------------------------------------

Risk Score is a deduction model, not an average. It starts at 100 and
subtracts fixed point penalties for each triggered flag below, floored at 0:

| Flag | Condition | Deduction |
|---|---|---|
| Reward/Risk shortfall | Below the §19 minimum of 2:1 | −25 |
| Elevated Portfolio Heat | §7A Heat in the 60–80% band | −10 |
| Critical Portfolio Heat | §7A Heat above 80% | −25 (replaces the −10 above, not additive) |
| High correlation | Position materially correlated (§14) with existing exposure | −10 |
| High event risk | §16 event classification is High | −10 |
| Extreme event risk | §16 event classification is Extreme | −20 (replaces the −10 above) |
| Poor liquidity | Liquidity Risk flagged under §5 | −10 |
| Active drawdown mode | Account is in Defensive, Recovery, or Capital Protection Mode (§9) | −15 |
| Non-structural stop | Stop is an arbitrary percentage rather than structure/ATR-based (§17) | −20 |

Multiple non-overlapping flags stack; overlapping Heat or event-risk flags do
not (the more severe one applies). The result is clamped to the §24 bands
(100/90/80/70/below 70 Reject). This model is deliberately simple and
auditable rather than statistically optimised — precision beyond whole
point deductions would misrepresent how well-calibrated these penalties are.

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
26A. MISSING RISK INPUT RULE
-------------------------------------------------------------------------------

`UNKNOWN` is never treated as zero, average, or any other guessed default.

If Stop Price, Position Size, Portfolio Heat inputs, or any other required
risk input (§26) is `UNKNOWN`, the Risk Score and Portfolio/Position Heat
Score for that candidate or position SHALL NOT be computed. This is not a
missing feature to route around — "Risk cannot be measured" already
requires Reject under §26. This subsection exists only to make explicit
that an agent implementation MAY NOT substitute a default, historical
average, or placeholder value to produce a number anyway. An unmeasurable
risk is a Reject, not a low score.

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
28. VETO AUTHORITY
-------------------------------------------------------------------------------

The Risk Engine has absolute Constraint Authority, as defined in
`CONSTITUTION.md` §4A. It is not Publication Authority.

If Risk Engine rejects a recommendation,

no downstream module — including the Master Decision Engine — may override
the decision.

The Risk Engine itself never integrates other engines' outputs, never
produces the Final Recommendation, and never publishes a report. Its only
output is a pass/reject/restrict result at the Preliminary and Final Risk
Gates (`STATE_MACHINE.md` States 07 and 15). Publishing the outcome that
this veto requires is the Master Decision Engine's job, per
`MASTER_DECISION_ENGINE.md` §21.

-------------------------------------------------------------------------------
28A. OPTIONS RISK BRIDGE
-------------------------------------------------------------------------------

The Position Risk, Position Heat, and Portfolio Heat formulas in this
document (§5, §7A, §8A) are stop-distance models built for equity positions.
They do not directly apply to an options position, which has no stop in the
equity sense.

For any options position:

Maximum Loss to Stop (§7A) is replaced by Premium at Risk — the total
premium paid, since a bought Long Call's maximum loss is the premium itself,
not a price-distance-to-stop calculation.

Position Risk % (§8A) is replaced by Premium at Risk ÷ Portfolio Value.

The options-specific risk factors that this document does NOT own — theta
decay, IV crush, delta exposure, gamma risk, and expiration risk — are
defined and evaluated in `OPTIONS_ENGINE.md` §5 and §8A. This bridge exists
only so an approved options position still rolls up correctly into
portfolio-level Portfolio Heat and the Risk Score deduction model; it does
not duplicate or relax the Options Engine's own risk requirements.

-------------------------------------------------------------------------------
End of Document

TRX Trading OS

RISK_ENGINE.md

Version 1.0.0
```
