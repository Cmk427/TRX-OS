# TRX Trading OS
## RED_TEAM_ENGINE.md

```text
Document ID      : TRX-RED-001
Document Name    : Red Team Engine
Version          : 1.3.0
Status           : Stable
Classification   : Critical
Dependencies     : COMMITTEE_ENGINE.md
                   DECISION_ENGINE.md
                   SCANNER_ENGINE.md
                   MARKET_ENGINE.md
                   CONSTITUTION.md
                   DATA_SOURCE_POLICY.md
Applies To       : All Investment Recommendations
```

-------------------------------------------------------------------------------
1. PURPOSE
-------------------------------------------------------------------------------

The Red Team Engine exists to invalidate investment ideas before capital is
committed.

Unlike other engines that search for opportunities, the Red Team searches
for reasons why a recommendation should NOT be executed.

A recommendation survives only after it withstands systematic criticism.

-------------------------------------------------------------------------------
2. PHILOSOPHY
-------------------------------------------------------------------------------

Every trade thesis is presumed incorrect until proven resilient.

The burden of proof belongs to the recommendation.

The Red Team assumes the market owes the trader nothing.

-------------------------------------------------------------------------------
3. PRIMARY OBJECTIVE
-------------------------------------------------------------------------------

The Red Team SHALL attempt to

Destroy weak ideas

Expose hidden assumptions

Reveal overlooked risks

Identify superior alternatives

Reduce confirmation bias

Prevent emotional trading

-------------------------------------------------------------------------------
4. REVIEW PROCESS
-------------------------------------------------------------------------------

Every recommendation SHALL undergo, in order, every mandatory attack category
in §4A — not a free-form bearish opinion, and not a subset chosen by the Red
Team itself:

Bear Thesis Review (Thesis Attack)

↓

Data Attack (§4A)

↓

Assumption Audit

↓

Macro Challenge

↓

Technical Challenge

↓

Portfolio Challenge

↓

Risk Challenge

↓

Catalyst Challenge

↓

Valuation Challenge

↓

Timing Challenge

↓

Execution Challenge

↓

Alternative Comparison

↓

Final Verdict

-------------------------------------------------------------------------------
4A. MANDATORY ATTACK CATEGORY INDEX
-------------------------------------------------------------------------------

Seven baseline categories are required for every review, mapped to sections
below. Four additional specialised attacks (Macro, Technical, Portfolio,
Catalyst) are also mandatory per the sequence above — they exist because
this system's original design already went further than a minimal
seven-category framework, and removing them would be a regression, not a
simplification.

| Category | Section | What it attacks |
|---|---|---|
| THESIS ATTACK | §4 Bear Thesis Review | The core investment thesis itself — why the directional or setup call could simply be wrong |
| DATA ATTACK | this section, below | Whether the verified facts the thesis relies on are actually as solid as claimed |
| ASSUMPTION ATTACK | §5 Assumption Audit | Hidden or unstated assumptions behind the thesis |
| VALUATION ATTACK | §12 Valuation Challenge | Whether the entry price/strike already reflects the good news |
| RISK ATTACK | §9 Risk Challenge | Whether stated risk is understated relative to `RISK_ENGINE.md`'s models |
| TIMING ATTACK | §13 Timing Challenge | Whether "now" is actually the right time, independent of direction being correct |
| EXECUTION ATTACK | §10 Execution Challenge | Whether the plan is actually executable at the stated size/liquidity/spread |

**Data Attack** (new, this section): the Red Team SHALL independently
re-check whether each material fact the thesis depends on is genuinely at
the V-label claimed in the Verification ledger (`VERIFICATION_POLICY.md`),
not merely assume the upstream engines labelled it correctly. This is
distinct from the Assumption Audit, which attacks *reasoning*, not *evidence
quality*. A V3 estimate quietly treated as if it were V1 fact anywhere in the
upstream analysis is a Data Attack finding, reported here even if no
downstream engine flagged it.

Every attack category above SHALL produce an explicit finding — "survived"
or "failed," with the specific reason — in the Output Format (§20). A
category with no finding recorded is treated as not having been attacked at
all, per §22 Final Rule.

**"Not applicable" (a third, narrowly-scoped finding value).** VALUATION
ATTACK and EXECUTION ATTACK — and only these two categories — may be
recorded as `not applicable` instead of survived/failed, but only when the
candidate was rejected or excluded *before* the pipeline stage that
produces the thing being attacked: no entry pricing exists to attack
(VALUATION) if the candidate never reached Playbook/Options review, and no
execution plan exists to attack (EXECUTION) if the candidate never reached
State 17. The other nine categories always have a live subject regardless
of how early a candidate was rejected — the thesis, the underlying data,
the assumptions, the macro/technical/portfolio/risk/catalyst context, and
the timing question all exist the moment a candidate is considered at all
— so `not applicable` is never a valid finding for them. Marking a category
`not applicable` when its subject genuinely exists is the same violation as
leaving it blank (§22).

-------------------------------------------------------------------------------
5. ASSUMPTION AUDIT
-------------------------------------------------------------------------------

Every recommendation SHALL identify

Verified Facts

Inferred Conclusions

Unknown Variables

Critical Assumptions

Every assumption SHALL be challenged independently.

-------------------------------------------------------------------------------
6. MACRO CHALLENGE
-------------------------------------------------------------------------------

Questions

What macro event could invalidate this trade?

Could interest rates affect the thesis?

Could CPI or FOMC reverse sentiment?

Could geopolitical news change market direction?

Output

Macro Threat Level

LOW

MEDIUM

HIGH

EXTREME

-------------------------------------------------------------------------------
7. TECHNICAL CHALLENGE
-------------------------------------------------------------------------------

Questions

Is the breakout genuine?

Could this be a bull trap?

Could this be buying exhaustion?

Is volume confirmation weak?

Where would professionals sell?

Output

Technical Failure Probability — a categorical estimate, not a numeric
score (avoiding false precision on an inherently judgment-based call, per
`CORE_PRINCIPLES.md`):

LOW

MEDIUM

HIGH

EXTREME

-------------------------------------------------------------------------------
8. PORTFOLIO CHALLENGE
-------------------------------------------------------------------------------

Questions

Does this increase concentration?

Does this duplicate exposure?

Does this reduce diversification?

Does this consume excessive buying power?

Could cash produce a better expected outcome?

Output

Portfolio Compatibility — this is the Red Team's own adversarial finding
for the Portfolio Challenge attack category (§4A), distinct from
`PORTFOLIO_ENGINE.md` §17's New Position Compatibility check and
`DECISION_ENGINE.md`'s pass/fail Portfolio Compatibility gate — those are
upstream checks; this is the Red Team re-attacking the same question
independently:

COMPATIBLE

MARGINAL

INCOMPATIBLE

`INCOMPATIBLE` here is a `failed` finding for the Portfolio Challenge
category in §20's Attack Category Results.

-------------------------------------------------------------------------------
9. RISK CHALLENGE
-------------------------------------------------------------------------------

Questions

Maximum realistic loss?

Gap risk?

Liquidity risk?

Volatility risk?

Execution risk?

Catalyst failure?

Output

Overall Risk Severity — categorical, matching the convention already used
for Macro Threat Level (§6) and Event Risk (`RISK_ENGINE.md` §16):

LOW

MEDIUM

HIGH

EXTREME

-------------------------------------------------------------------------------
10. EXECUTION CHALLENGE
-------------------------------------------------------------------------------

Questions

Can the position be exited quickly?

Is the spread acceptable?

Could slippage materially affect returns?

Would partial fills be likely?

Is the order type appropriate?

-------------------------------------------------------------------------------
11. CATALYST CHALLENGE
-------------------------------------------------------------------------------

Questions

Is the catalyst already priced in?

Is market expectation unrealistic?

What if earnings disappoint?

What if guidance is neutral?

Could news already be fully reflected?

-------------------------------------------------------------------------------
12. VALUATION CHALLENGE
-------------------------------------------------------------------------------

Questions

Is current valuation justified?

Is enthusiasm excessive?

Could multiple contraction occur?

Would institutions still buy here?

-------------------------------------------------------------------------------
13. TIMING CHALLENGE
-------------------------------------------------------------------------------

Questions

Why buy today?

Why not tomorrow?

Why not next week?

Would waiting improve probability?

Output

Timing Assessment

Optimal

Acceptable

Premature

Late

-------------------------------------------------------------------------------
14. ALTERNATIVE ANALYSIS
-------------------------------------------------------------------------------

The Red Team SHALL search for superior alternatives.

Questions

Is another stock stronger?

Is another ETF safer?

Would an option provide better asymmetry?

Would holding cash produce better optionality?

-------------------------------------------------------------------------------
15. FAILURE SCENARIOS
-------------------------------------------------------------------------------

Construct realistic failure cases.

Scenario A

Market sells off.

Scenario B

Sector weakens.

Scenario C

Catalyst disappoints.

Scenario D

Liquidity disappears.

Scenario E

Volatility spikes.

Estimate — each of the following is categorical per scenario, not a
numeric score (a specific % probability for a hypothetical scenario would
be false precision this system does not claim):

Probability: LOW | MEDIUM | HIGH

Impact: LOW | MEDIUM | HIGH | EXTREME

Recovery Difficulty: LOW | MEDIUM | HIGH | EXTREME

-------------------------------------------------------------------------------
16. WORST CASE ANALYSIS
-------------------------------------------------------------------------------

The report SHALL include

Maximum Expected Drawdown

Capital at Risk

Time to Recovery

Portfolio Impact

Psychological Impact

-------------------------------------------------------------------------------
17. THESIS BREAK CONDITIONS
-------------------------------------------------------------------------------

Every recommendation SHALL specify

The exact condition that invalidates the thesis.

Examples

Loss of support

Trend reversal

Failed breakout

Unexpected earnings

Macro deterioration

Once invalidated

Recommendation Status

INVALID

-------------------------------------------------------------------------------
18. DECISION DOWNGRADE
-------------------------------------------------------------------------------

If Red Team discovers

Critical flaw

↓

Recommendation

EXECUTE

becomes

WATCH

or

REJECT

The Red Team has veto authority for Critical Risk.

The Red Team issues the critical-risk finding and required downgrade. The Master
Decision Engine alone publishes the final report, and it SHALL preserve the
finding rather than override it.

-------------------------------------------------------------------------------
19. RED TEAM SCORE
-------------------------------------------------------------------------------

Every recommendation receives a Resilience Score — this is the same number
`MASTER_DECISION_ENGINE.md` §7 refers to as "Red Team Score"; one score, two
names, defined only here.

**Formula.** Starts at 100. For each of the 11 mandatory attack categories in
§4A that reports `failed`, subtract 10 for a baseline category (Thesis,
Data, Assumption, Valuation, Risk, Timing, Execution) or 5 for a specialized
category (Macro, Technical, Portfolio, Catalyst), floored at 0. A category
missing its required finding entirely (§4A, §22) counts as `failed` for this
calculation — silence is not a pass. A category correctly recorded
`not applicable` (§4A — VALUATION ATTACK / EXECUTION ATTACK only, and only
when their subject genuinely does not exist yet) is excluded entirely — no
deduction, and it does not lower the maximum achievable score; it is simply
not evaluated, the same way a `Not Applicable` Options Analysis section
(`OUTPUT_CONTRACT.md` §3 item 8) does not penalise a report that never
reached options review.

Explicit, non-overlapping bands (matches the convention fixed in
`RISK_ENGINE.md` §24):

100 (exact) — Exceptional (no category failed)

90–99 — Strong

80–89 — Acceptable

70–79 — Weak

Below 70 — Reject

A critical-risk finding (§18 Decision Downgrade) is independent of this
score and remains binding regardless of the numeric Resilience Score — a
high score does not waive a critical-risk veto, and a low score does not by
itself create one; they are two separate signals.

-------------------------------------------------------------------------------
20. OUTPUT FORMAT
-------------------------------------------------------------------------------

Every Red Team Report SHALL contain

Original Thesis

**Attack Category Results** — one row per §4A category (Thesis, Data,
Assumption, Valuation, Risk, Timing, Execution, plus Macro/Technical/
Portfolio/Catalyst): category name, finding, and result — `survived`,
`failed`, or (VALUATION/EXECUTION only, per §4A's narrow rule) `not
applicable`. A report missing any required category's row is incomplete
per §22.

Primary Weakness

Counter Evidence

Bear Case

Failure Scenarios

Alternative Investment

Cash Alternative

Resilience Score

Recommendation Adjustment

-------------------------------------------------------------------------------
21. SUCCESS CRITERIA
-------------------------------------------------------------------------------

The Red Team succeeds when

Weak ideas are rejected

Risk becomes visible

Bias is reduced

Better opportunities are identified

Capital is protected

-------------------------------------------------------------------------------
22. FINAL RULE
-------------------------------------------------------------------------------

A recommendation is considered complete only after

Market Engine

Portfolio Engine

Scanner Engine

Committee Engine

AND

Red Team Engine

have all completed successfully.

-------------------------------------------------------------------------------
End of Document

TRX Trading OS

RED_TEAM_ENGINE.md

Version 1.3.0
```
