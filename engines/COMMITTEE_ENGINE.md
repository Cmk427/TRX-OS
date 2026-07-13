# TRX Trading OS
## COMMITTEE_ENGINE.md

```text
Document ID      : TRX-COM-001
Document Name    : Investment Committee Engine
Version          : 1.4.0
Status           : Stable
Classification   : Critical
Dependencies     : MARKET_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   SCANNER_ENGINE.md
                   OPTIONS_ENGINE.md
                   DECISION_ENGINE.md
                   CONSTITUTION.md
                   STATE_MACHINE.md
Applies To       : Multi-Role Investment Evaluation
```

-------------------------------------------------------------------------------
1. PURPOSE
-------------------------------------------------------------------------------

The Investment Committee is a multi-role evaluation and challenge layer before
the Master Decision Engine publishes a final recommendation.

Its objective is to reduce cognitive bias by evaluating every opportunity
from multiple independent perspectives.

No new-risk recommendation (an `EXECUTE CANDIDATE` disposition per
`DECISION_ENGINE.md`) shall bypass Committee Review.

The Committee does not publish the final recommendation and cannot override a
critical verification, Risk Engine, or Red Team veto.

-------------------------------------------------------------------------------
2. PHILOSOPHY
-------------------------------------------------------------------------------

Every investment idea deserves opposition.

Every opportunity shall be challenged before capital is deployed.

Consensus must be earned.

-------------------------------------------------------------------------------
3. COMMITTEE MEMBERS
-------------------------------------------------------------------------------

The Committee consists of six independent roles.

1.

Chief Market Strategist

2.

Technical Analyst

3.

Portfolio Manager

4.

Risk Manager

5.

Options Specialist

6.

Execution Specialist

Each member evaluates independently.

Members SHALL NOT inherit conclusions from previous members.

-------------------------------------------------------------------------------
4. MEMBER RESPONSIBILITIES
-------------------------------------------------------------------------------

Chief Market Strategist

Focus

Overall market.

Questions

Is the market favorable?

Should exposure increase?

Is cash preferable?

Output

Market Vote

-------------------------------------------------------------------------------

Technical Analyst

Focus

Chart quality.

Evaluate

Trend

Support

Resistance

Momentum

Volume

Pattern

Output

Technical Vote

-------------------------------------------------------------------------------

Portfolio Manager

Focus

Portfolio impact.

Evaluate

Diversification

Sector Exposure

Correlation

Capital Allocation

Buying Power

Output

Portfolio Vote

-------------------------------------------------------------------------------

Risk Manager

Focus

Capital preservation.

Evaluate

Maximum Loss

Position Size

Volatility

Liquidity

Gap Risk

Output

Risk Vote

-------------------------------------------------------------------------------

Options Specialist

Focus

Capital efficiency.

Evaluate

Stock

versus

Buy Call

Compare

Reward

Risk

IV

Theta

Expiration

Output

Options Vote

-------------------------------------------------------------------------------

Execution Specialist

Focus

Execution quality.

Evaluate

Liquidity

Spread

Order Type

Entry Timing

Exit Strategy

Output

Execution Vote

-------------------------------------------------------------------------------
5. VOTING OPTIONS
-------------------------------------------------------------------------------

Each member SHALL select exactly one.

STRONG BUY

BUY

WATCH

PASS

REDUCE

REJECT

-------------------------------------------------------------------------------
5A. VOTE-TO-POINTS MAPPING
-------------------------------------------------------------------------------

Each of the six voting options in §5 maps to a fixed point value used to
compute the Consensus / Committee Score in §10:

STRONG BUY = 100

BUY = 80

WATCH = 60

PASS = 40

REDUCE = 20

REJECT = 0

-------------------------------------------------------------------------------
6. WEIGHTED VOTING
-------------------------------------------------------------------------------

Suggested Weight

Market Strategist

25%

Risk Manager

25%

Portfolio Manager

20%

Technical Analyst

15%

Execution Specialist

10%

Options Specialist

5%

-------------------------------------------------------------------------------
7. COMMITTEE CONDITIONS
-------------------------------------------------------------------------------

A recommendation may be marked Committee Qualified only if the weighted
consensus supports it and no member identifies an unresolved critical concern.
The Risk Manager's objections are preserved and escalated to the Risk Engine,
which owns the binding risk veto under `CONSTITUTION.md`.

-------------------------------------------------------------------------------
8. COMMITTEE QUESTIONS
-------------------------------------------------------------------------------

Every member SHALL answer

Why now?

Why this asset?

Why not alternatives?

Why should capital remain in cash?

What is the greatest risk?

What evidence is strongest?

What evidence is weakest?

-------------------------------------------------------------------------------
9. REQUIRED OUTPUT
-------------------------------------------------------------------------------

Each member SHALL provide

Decision

Confidence

Supporting Evidence

Primary Concern

Risk Level

Confidence here is each member's own qualitative self-rating of their vote
— `High | Medium | Low` — and is distinct from `MASTER_DECISION_ENGINE.md`
§7's formal, numeric Confidence Model (computed later, at State 16, from a
weighted formula). The two share a name but not a scale or a method, the
same distinction `DECISION_ENGINE.md` draws with its own "Conviction
Rating."

-------------------------------------------------------------------------------
10. CONSENSUS LEVEL
-------------------------------------------------------------------------------

Consensus Score formula (this is the "Committee Score" referenced as an
input in `MASTER_DECISION_ENGINE.md` §7 — one name, one number, defined only
here):

Consensus Score = Σ (role weight from §6 × vote points from §5A)

Example: if the Market Strategist (25%) and Portfolio Manager (20%) vote BUY
(80 pts), Risk Manager (25%) votes WATCH (60 pts), Technical Analyst (15%)
votes STRONG BUY (100 pts), Execution Specialist (10%) votes BUY (80 pts),
and Options Specialist (5%) votes PASS (40 pts), the score is
(0.25×80)+(0.20×80)+(0.25×60)+(0.15×100)+(0.10×80)+(0.05×40) = 76 — Weak
Consensus per the bands below (76 falls in the 70+ band, not the 80+
Moderate band — a useful reminder that "mostly BUY votes" does not
automatically read as Moderate or better; the Risk Manager's WATCH at 25%
weight pulls this example below that threshold).

Consensus Score

95+

Unanimous

90+

Strong Consensus

80+

Moderate Consensus

70+

Weak Consensus

Below 70

No Consensus

-------------------------------------------------------------------------------
11. DISSENT REPORT
-------------------------------------------------------------------------------

Minority opinions SHALL be preserved.

The report SHALL include

Who disagreed

Why

What assumptions differ

Potential consequences

-------------------------------------------------------------------------------
12. ESCALATION
-------------------------------------------------------------------------------

If consensus cannot be reached

Recommendation

WATCH

No immediate execution.

Independently of the overall Consensus Score: if the Risk Manager votes
REDUCE or REJECT, this SHALL force a mandatory escalation to
`RISK_ENGINE.md` for a formal re-evaluation, regardless of how the other
five roles voted or what the weighted Consensus Score computes to. This is
an escalation, not an automatic veto — the Committee itself has no veto
power (`CONSTITUTION.md` §4A); only `RISK_ENGINE.md`'s own subsequent
evaluation can turn this into a binding rejection. A high Consensus Score
achieved by outvoting a dissenting Risk Manager does not skip this
escalation.

-------------------------------------------------------------------------------
13. COMMITTEE FAILURE
-------------------------------------------------------------------------------

The Committee SHALL reject recommendations when

Evidence insufficient

Portfolio conflict

Macro conflict

Liquidity unacceptable

Risk excessive

"Confidence too low" is deliberately not a Committee rejection trigger — the
Confidence Model (`MASTER_DECISION_ENGINE.md` §7) is not even computed until
State 16, two states after Committee Review (State 13); the Committee has no
Confidence number to evaluate at the time it votes. If the underlying reason
for what would become low confidence is one of the conditions above
(insufficient evidence, excessive risk, etc.), that condition — not a
Confidence score that does not yet exist — is the Committee's actual basis
for rejection.

-------------------------------------------------------------------------------
14. COMMITTEE DISPOSITION
-------------------------------------------------------------------------------

Possible Results

EXECUTE

WATCH

HOLD

REDUCE

EXIT

PASS

`EXECUTE` in a committee vote means the idea is supported for Master Decision
review; it is not a final recommendation or an order instruction.

-------------------------------------------------------------------------------
15. SUCCESS CRITERIA
-------------------------------------------------------------------------------

The Committee succeeds when

Multiple perspectives considered

Bias reduced

Portfolio protected

Decision quality improved

Reasoning transparent

-------------------------------------------------------------------------------
End of Document

TRX Trading OS

COMMITTEE_ENGINE.md

Version 1.4.0
```
