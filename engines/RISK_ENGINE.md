# TRX Trading OS
## RISK_ENGINE.md

```text
Document ID      : TRX-RISK-001
Document Name    : Risk Engine
Version          : 1.3.0
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

Heat Levels (interval convention: lower bound inclusive, upper bound
exclusive, except the top band which includes 100% — this resolves the
boundary itself, e.g. exactly 20% is Normal, not Very Low)

0–20%

Very Low

20–40%

Normal

40–60%

Elevated

60–80%

High

80–100%

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

**Units.** The formula above produces a dimensionless ratio (e.g. 0.35), not
a percentage. Before comparing the result to the §7 bands or citing it in
any report, output, or the Parameter Registry, it SHALL be multiplied by 100
and expressed as a percentage (0.35 → 35%) — the bands, the Registry, and
every downstream reference to "Portfolio Heat" are stated in percent, never
as a raw fraction.

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

Drawdown Levels (same interval convention as §7: lower bound inclusive,
upper bound exclusive, except the top band)

0-5%

Normal

5-10%

Caution

10-15%

Defensive

15-20%

Recovery Mode

20-100%

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

This Reject is a **hard override**, not a point deduction: a candidate
below the 2:1 minimum is Rejected regardless of what the §24A deduction
arithmetic would otherwise compute. See §24A's Hard Reject Flags list —
this rule is one of them, so it cannot be "outvoted" by an otherwise-clean
scorecard.

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

Explicit, non-overlapping bands (every integer 0–100 has exactly one label —
see §24A for why the discrete-point notation used in earlier drafts of this
table was ambiguous and is superseded by these ranges):

100 (exact) — Minimal Risk

90–99 — Low Risk

80–89 — Controlled Risk

70–79 — Elevated Risk

Below 70 — Reject

-------------------------------------------------------------------------------
24A. RISK SCORE FORMULA
-------------------------------------------------------------------------------

Risk Score has two layers, evaluated in this order — Hard Reject Flags
first, deduction model second. This exists because a single unacceptable
condition (e.g. reward/risk below minimum) must reliably force Reject, not
merely contribute points to an average that a clean scorecard elsewhere
could outweigh.

**Layer 1 — Hard Reject Flags.** If any of the following is true, Risk
Score is reported as `0 — Reject` directly; the Layer 2 arithmetic is not
computed and cannot change this result:

- Reward/Risk below the §19 minimum of 2:1.
- Any §26 Failure Condition (position size unknown, stop undefined, risk
  unmeasurable, liquidity unacceptable, portfolio exposure excessive).
- Any §26A Missing Risk Input (a required input is `UNKNOWN`).
- Critical Portfolio Heat (§7A Heat above 80%).
- Extreme event risk (§16).

**Layer 2 — Deduction model.** Only reached if no Hard Reject Flag applies.
Starts at 100 and subtracts fixed point penalties for each triggered flag
below, floored at 70 (a score cannot fall below 70 via Layer 2 alone —
falling below 70 is exclusively a Layer 1 Reject, never a Layer 2 result,
which is what makes the §24 bands non-overlapping and deterministic):

| Flag | Condition | Deduction |
|---|---|---|
| Elevated Portfolio Heat | §7A Heat in the 60–80% band | −10 |
| High correlation | Position materially correlated (§14) with existing exposure | −10 |
| High event risk | §16 event classification is High | −10 |
| Poor liquidity | Liquidity Risk flagged under §5 | −10 |
| Active drawdown mode | Account is in Defensive, Recovery, or Capital Protection Mode (§9) | −15 |
| Non-structural stop | Stop is an arbitrary percentage rather than structure/ATR-based (§17) | −20 |

Multiple flags stack; overlapping Heat flags do not (only Elevated can
apply here — Critical Heat is a Layer 1 Reject, not a Layer 2 flag). This
model is deliberately simple and auditable rather than statistically
optimised — precision beyond whole point deductions would misrepresent how
well-calibrated these penalties are.

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

Version 1.3.0
```
