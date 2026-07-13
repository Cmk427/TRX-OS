# TRX Trading OS
## PLAYBOOK_LIBRARY.md

```text
Document ID      : TRX-PBL-001
Document Name    : Playbook Library
Version          : 1.3.0
Status           : Active
Classification   : Core
Dependencies     : PLAYBOOK_ENGINE.md
                   SCANNER_ENGINE.md
                   MARKET_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   RISK_ENGINE.md
                   VERIFICATION_POLICY.md
                   FAILURE_TAXONOMY.md
Applies To       : Opportunity Classification
```

---

## 1. Purpose

This Library turns the original Playbook Engine index into repeatable research
rules. It does not claim that any setup has been backtested or is profitable.
Each candidate may be assigned to exactly one playbook; if none matches with
sufficient verified evidence, the outcome is `NO TRADE` or `WATCH`.

All playbooks require Market and Portfolio review, preliminary Risk approval,
verified or explicitly labelled data, defined invalidation, measurable maximum
risk, a cash alternative, and final Committee, Red Team, Risk, and Master
Decision review.

---

## 2. Shared Rule Format

Every playbook in §3 uses the identical 12-field template below, in the
identical order, so a new playbook cannot be added in a different style and
an existing one cannot drift:

1. **Objective** — one sentence: the specific evidence/edge this setup is
   trying to capture, not a restatement of its name.
2. **Applicable Market** — which `MARKET_ENGINE.md` regime(s) (§3) and
   volatility conditions (§7) this setup is designed for.
3. **Scanner Criteria** — the candidate-universe-level conditions
   `SCANNER_ENGINE.md` §4 must already have confirmed (trend, relative
   strength, volume, liquidity) before this playbook may even be
   considered.
4. **Market Criteria** — the specific `MARKET_ENGINE.md` outputs (regime,
   sector rotation, breadth, volatility rating) to check at assignment
   time — the mechanical check behind the Applicable Market scope above.
5. **Portfolio Criteria** — any `PORTFOLIO_ENGINE.md` condition specific to
   this playbook, beyond the standing §9A concentration limits that apply
   to every playbook by default.
6. **Required Data** — the specific verified data points a candidate must
   have before this playbook may be assigned, distinct from the Entry
   trigger itself. If any listed item is unverified or unavailable, the
   playbook cannot be assigned.
7. **Entry** — the observable, timestamped trigger condition; never a
   prediction.
8. **Stop** — the initial stop-placement rule (structure/ATR-based per
   `RISK_ENGINE.md` §17, never an arbitrary percentage).
9. **Exit** — profit-taking, partial-exit, and review logic, distinct from
   the Stop.
10. **Risk** — risk factors specific to this setup beyond the generic stop
    (event risk, false-signal risk, concentration risk) — context for
    `RISK_ENGINE.md`, not a substitute for its formulas.
11. **Invalidation** — the condition that breaks the setup before or after
    entry, independent of whether the Stop has been hit.
12. **Failure Code** — which `FAILURE_TAXONOMY.md` §2 category applies when
    this playbook's No-Trade condition fires. Most No-Trade conditions
    below are `DATA_FAILURE` (a required item could not be verified to the
    minimum level `DATA_SOURCE_POLICY.md` requires). A playbook simply not
    matching despite fully verified data is an **ordinary non-match**, not
    a Failure Taxonomy category — it falls through to `NO TRADE` or
    `WATCH` per `STATE_MACHINE.md` State 10 without needing a code, the
    same way a candidate scoring low on Scanner's Candidate Quality Score
    does. Each entry below states which applies.

**Output** is deliberately not a 13th per-playbook field: every playbook
produces the identical output shape — a `playbook_id`, a Playbook Match
Score, and (if below threshold or excluded) a `no_trade_reason` — defined
once, globally, in `PLAYBOOK_ENGINE.md` §7–8 and
`system/ENGINE_INTERFACE_CONTRACT.md` §6/`schemas/playbook.schema.yaml`.
Repeating that identical sentence 15 times would be boilerplate, not
information; what varies between playbooks is the 12 fields above, not the
output shape.

The Playbook Match Score evaluates fit with the complete rule set: 90+ Excellent,
80+ Qualified, 70+ Weak, below 70 Reject. `PLAYBOOK_ENGINE.md` requires 85+ for
execution eligibility. The score is not evidence of future performance.

---

## 3. Playbooks

### PB-001 — Momentum Breakout

1. **Objective:** capture continuation after a verified breakout above a
   defined resistance/trigger level, in a supportive regime with
   confirming volume.
2. **Applicable Market:** Risk On or Strong Risk On regime; liquid sector
   leadership present.
3. **Scanner Criteria:** verified trend, relative strength, and volume
   expansion versus average.
4. **Market Criteria:** regime is not Risk Off / Strong Risk Off; sector
   leadership confirmed via `MARKET_ENGINE.md` §10 Sector Rotation.
5. **Portfolio Criteria:** standard §9A concentration limits only — no
   playbook-specific addition.
6. **Required Data:** price/volume history sufficient to establish trend
   and the resistance/trigger level, current relative-strength comparison,
   and current volume versus average.
7. **Entry:** a confirmed break above the recorded trigger/resistance area
   with volume confirmation — not a single noisy print.
8. **Stop:** structure-based stop below the breakout level.
9. **Exit:** take profits only against recorded resistance / risk-reward
   logic; review follow-through before adding.
10. **Risk:** false-breakout risk; material market/sector deterioration
    risk.
11. **Invalidation:** failed breakout, loss of the breakout level, or
    material market/sector deterioration.
12. **Failure Code:** `DATA_FAILURE` if volume or trend cannot be
    verified. Ordinary non-match (no code) if liquidity is genuinely weak,
    price is extended without a definable stop, or the regime is hostile.
    No Trade: weak liquidity, unverified volume, extended price without a
    defined stop, or hostile market regime.

### PB-002 — Gap & Go

1. **Objective:** capture sustained intraday strength following a
   catalyst-driven gap, distinct from a gap that merely fills.
2. **Applicable Market:** any regime, provided the gap's catalyst and
   liquidity are independently verified.
3. **Scanner Criteria:** liquid asset with a verified opening gap and
   sustained (not merely pre-market) strength.
4. **Market Criteria:** no material regime conflict (e.g. a bullish gap
   during Strong Risk Off warrants extra scrutiny under §5A Regime
   Transition Control).
5. **Portfolio Criteria:** standard §9A limits only.
6. **Required Data:** verified gap catalyst source, pre-market and opening
   liquidity/volume, and current bid-ask spread.
7. **Entry:** current quote plus sustained intraday strength confirming
   the gap, not the pre-market move alone.
8. **Stop:** pre-defined intraday stop, structure-based.
9. **Exit:** time-aware review given the position's short intended
   holding period.
10. **Risk:** gap and slippage risk must be stated explicitly; this
    playbook carries materially higher gap risk than most others in this
    Library.
11. **Invalidation:** gap fill or loss of the defined intraday support /
    volume confirmation.
12. **Failure Code:** `DATA_FAILURE` if the catalyst source cannot be
    verified. Ordinary non-match (no code) if spread is wide, liquidity is
    thin, or entry is late. No Trade: unverified catalyst, wide spread,
    thin liquidity, or late entry.

### PB-003 — Post Earnings Continuation

1. **Objective:** capture continuation of a verified, sustained market
   reaction to an earnings event, not the earnings gap itself.
2. **Applicable Market:** any regime, provided the reaction is
   independently sustained rather than assumed from the headline result.
3. **Scanner Criteria:** verified post-event price/volume behaviour
   consistent with a genuine reaction, not a single-session spike.
4. **Market Criteria:** no material market risk-off change since the
   event.
5. **Portfolio Criteria:** standard §9A limits only.
6. **Required Data:** official (S1) earnings release and guidance text,
   post-event price/volume, and the pre-event price for reaction-size
   context.
7. **Entry:** confirmed post-event support holding with continuation
   volume.
8. **Stop:** structure-based stop below post-event support.
9. **Exit:** identify continuation / failure levels; review at the next
   scheduled event.
10. **Risk:** event gap risk already realised must be accounted for, not
    re-flagged as new risk; guidance-contradiction risk.
11. **Invalidation:** reversal of the reaction structure, guidance
    contradiction, or market risk-off change.
12. **Failure Code:** `DATA_FAILURE` if earnings details or the current
    reaction cannot be verified. No Trade: earnings details or current
    reaction cannot be verified.

### PB-004 — Trend Pullback

1. **Objective:** capture a resumption of an established uptrend after a
   verified, orderly retracement to support.
2. **Applicable Market:** supportive-to-neutral regime with an established
   liquid uptrend already in place.
3. **Scanner Criteria:** trend history establishing the uptrend and an
   orderly (not disorderly) retracement.
4. **Market Criteria:** regime does not conflict with continued upside
   (not Risk Off / Strong Risk Off).
5. **Portfolio Criteria:** standard §9A limits only.
6. **Required Data:** trend history establishing the uptrend, the specific
   support/trend level being tested, and current relative strength/volume
   at the retracement.
7. **Entry:** retracement to a recorded support/trend area with renewed
   relative strength or volume confirmation.
8. **Stop:** below the structural invalidation level.
9. **Exit:** manage against prior highs and reward/risk requirements.
10. **Risk:** disorderly-pullback risk (a sharp, high-volume retracement
    is a different risk profile than an orderly one).
11. **Invalidation:** break of the trend structure or support that defines
    the thesis.
12. **Failure Code:** ordinary non-match (no code) — this playbook's
    No-Trade conditions are structural, not data-verification failures.
    No Trade: no established trend, pullback is disorderly, or support is
    undefined.

### PB-005 — Relative Strength Leader

1. **Objective:** capture continuation from an asset demonstrating durable,
   verified relative strength versus its index/sector/peers.
2. **Applicable Market:** any regime in which the relative-strength
   comparison remains valid (not a stale pre-regime-change comparison).
3. **Scanner Criteria:** persistent relative strength with a liquid trend
   and a valid entry structure.
4. **Market Criteria:** sector/peer context confirms the strength is
   relative, not merely a broad market move lifting everything.
5. **Portfolio Criteria:** control concentration if the portfolio already
   holds the same leader or sector — this playbook's Portfolio Criteria
   is stricter than the §9A default because relative-strength leaders
   cluster by construction.
6. **Required Data:** relative-strength history versus the stated
   index/sector/peers over a sustained period, plus current portfolio
   holdings for duplicate-exposure comparison.
7. **Entry:** a valid entry structure confirming persistent relative
   strength.
8. **Stop:** structure-based stop tied to the entry structure.
9. **Exit:** review if relative strength stalls even before a structural
   stop is hit.
10. **Risk:** concentration risk (§5 Portfolio Criteria above) is this
    playbook's dominant non-market risk.
11. **Invalidation:** relative strength deterioration plus structural
    failure.
12. **Failure Code:** `DATA_FAILURE` if the relative-strength comparison
    is stale. Ordinary non-match (no code) if it would duplicate existing
    exposure. No Trade: strength is based on stale comparisons or
    duplicates exposure.

### PB-006 — Sector Rotation

1. **Objective:** capture a broad, verified sector-level leadership or
   capital-flow shift through one liquid representative instrument.
2. **Applicable Market:** any regime in which a genuine sector-level (not
   single-ticker) shift is verified.
3. **Scanner Criteria:** sector-level relative strength and breadth data,
   not a single ticker's move.
4. **Market Criteria:** `MARKET_ENGINE.md` §10 Sector Rotation confirms the
   shift at the sector level.
5. **Portfolio Criteria:** diversify exposure across the sector rather
   than concentrating in one representative instrument beyond §9A limits.
6. **Required Data:** sector-level relative-strength and breadth data (not
   a single ticker), and liquidity data for the chosen representative
   stock/ETF.
7. **Entry:** a liquid representative stock or ETF confirming the
   sector-level shift.
8. **Stop:** sector-level invalidation applied to the representative
   instrument's structure.
9. **Exit:** review as sector-level relative strength evolves, not just
   the representative instrument's own price action.
10. **Risk:** single-ticker risk if the "sector" evidence turns out to be
    one stock's move — this is the primary failure mode this playbook
    exists to filter out.
11. **Invalidation:** rotation evidence reverses or sector leadership loses
    trend.
12. **Failure Code:** ordinary non-match (no code) — a single ticker move
    is a structural non-match, not a data-verification failure. No Trade:
    a single ticker move is the only evidence of rotation.

### PB-007 — High Volume Reversal

1. **Objective:** capture a verified reversal at a meaningful
   support/resistance level, confirmed by exceptional volume and a second
   session, not a single noisy bar.
2. **Applicable Market:** any regime, provided the reversal level and
   volume are independently verified.
3. **Scanner Criteria:** exceptional relative volume and a confirming
   second bar or session.
4. **Market Criteria:** no specific regime requirement beyond standard
   verification.
5. **Portfolio Criteria:** standard §9A limits only.
6. **Required Data:** intraday/daily volume versus average, the specific
   support/resistance level tested, and a second confirming bar or
   session.
7. **Entry:** price rejection or recovery at the level, confirmed by a
   second bar/session, not the first reaction alone.
8. **Stop:** tighter-than-default structural stop, reflecting elevated
   reversal-failure probability.
9. **Exit:** review promptly given the higher failure-rate profile of
   reversal setups generally.
10. **Risk:** reversal-failure probability is materially higher than
    trend-following setups in this Library; risk controls are
    correspondingly tighter (see Stop above).
11. **Invalidation:** failure of the reversal level or renewed adverse
    trend.
12. **Failure Code:** `DATA_FAILURE` if volume cannot be verified or
    liquidity/catalyst risk is unknown and material. No Trade: volume is
    unverified, liquidity is weak, or catalyst risk is unknown and
    material.

### PB-008 — Short Squeeze

1. **Objective:** capture a high-risk, event-driven squeeze using only
   verified price/volume behaviour, with any short-interest narrative
   explicitly labelled as inference rather than fact.
2. **Applicable Market:** any regime; this is an idiosyncratic, single-name
   event setup independent of broad regime.
3. **Scanner Criteria:** verified liquidity (spread, depth) sufficient to
   exit a small, predefined position quickly.
4. **Market Criteria:** none beyond standard verification — this playbook
   is deliberately not regime-dependent.
5. **Portfolio Criteria:** risk budget must be able to absorb a sharp
   reversal without breaching account-level limits — checked explicitly,
   not assumed.
6. **Required Data:** current price/volume behaviour, verified liquidity
   (spread, depth), and, if used, short-interest / days-to-cover data
   explicitly labelled with its source and freshness (V-label per
   `VERIFICATION_POLICY.md`).
7. **Entry:** actual, verified price/volume behaviour — never an inferred
   short-interest narrative alone.
8. **Stop:** small, predefined risk only, tighter than this Library's
   default structural stop given extreme gap risk.
9. **Exit:** account for extreme gap and execution risk in the exit plan
   itself, not only at entry.
10. **Risk:** this is the highest single-name gap/execution risk playbook
    in the Library; risk budget sizing (§5 above) is the primary control.
11. **Invalidation:** loss of momentum / liquidity or a structural
    reversal.
12. **Failure Code:** `DATA_FAILURE` if liquidity is unknown or the thesis
    is unverified. `RISK_REJECTION` if the account's risk budget cannot
    absorb a sharp reversal (a `RISK_ENGINE.md` determination, not a
    Playbook-level one). No Trade: unknown liquidity, unverified thesis,
    or risk budget cannot absorb a sharp reversal.

### PB-009 — AI Momentum

1. **Objective:** apply PB-001's momentum evidence standard to an
   AI-related company or ETF, explicitly rejecting thematic narrative as a
   substitute for that evidence.
2. **Applicable Market:** same as PB-001 (Risk On or Strong Risk On,
   liquid sector leadership).
3. **Scanner Criteria:** identical to PB-001 — verified trend, relative
   strength, and volume expansion; the "AI" label itself is not evidence.
4. **Market Criteria:** identical to PB-001, plus confirmation that AI/theme
   leadership specifically (not just broad tech) is verified via §10.
5. **Portfolio Criteria:** current portfolio AI/theme exposure SHALL be
   checked for concentration beyond §9A's theme-concentration limit — this
   playbook's dominant portfolio risk is theme clustering.
6. **Required Data:** same as PB-001 Momentum Breakout, plus current
   portfolio AI/theme exposure for concentration comparison.
7. **Entry:** identical trigger to PB-001, applied to the AI-labelled
   instrument.
8. **Stop:** identical to PB-001 — structure-based stop below the
   breakout level.
9. **Exit:** identical to PB-001, plus a review if AI-theme concentration
   (§5 above) approaches its limit independent of this position's own
   price action.
10. **Risk:** theme-concentration risk is this playbook's primary
    additional risk versus PB-001, since AI-labelled positions correlate
    heavily with each other.
11. **Invalidation:** thesis / trend failure or theme-concentration
    breach.
12. **Failure Code:** ordinary non-match (no code) — a popular narrative
    without PB-001-equivalent evidence is a structural non-match. No
    Trade: the only rationale is a popular AI narrative.

### PB-010 — Defense Rotation

1. **Objective:** capture verified defensive-sector leadership during
   neutral, risk-off, or elevated-volatility conditions.
2. **Applicable Market:** Neutral, Risk Off, or Strong Risk Off regime, or
   elevated/extreme volatility per `MARKET_ENGINE.md` §7.
3. **Scanner Criteria:** defensive relative strength and a liquid entry
   structure.
4. **Market Criteria:** current market regime/volatility classification
   explicitly supports a defensive tilt (this is the one playbook in this
   Library whose Market Criteria is a precondition, not a soft
   preference).
5. **Portfolio Criteria:** match lower-beta intent with current portfolio
   heat and cash needs — a defensive position sized as if it were a
   momentum position defeats its own purpose.
6. **Required Data:** defensive-sector relative strength, current market
   regime/volatility classification, and liquidity of the chosen
   instrument.
7. **Entry:** defensive relative strength confirmed against the current
   market risk context.
8. **Stop:** structure-based stop, sized consistently with lower-beta
   intent (§5 above).
9. **Exit:** review as the regime normalises — this playbook's thesis is
   explicitly regime-conditional, per `MARKET_ENGINE.md` §15A Regime
   Transition Control.
10. **Risk:** regime-normalisation risk — if the broad market shifts back
    to Risk On, this thesis's edge disappears even if the instrument's own
    price action looks unchanged.
11. **Invalidation:** regime normalises or defensive leadership breaks
    down.
12. **Failure Code:** ordinary non-match (no code). No Trade: broad market
    is strongly risk-on without supporting defensive evidence.

### PB-011 — Large Cap Leadership

1. **Objective:** capture continuation from a liquid large-cap leader with
   durable trend and verified institutional-participation evidence.
2. **Applicable Market:** any regime supportive of continued large-cap
   leadership.
3. **Scanner Criteria:** verified liquidity, relative strength, trend, and
   a defined entry structure.
4. **Market Criteria:** no material conflict with continued large-cap/
   index-level strength.
5. **Portfolio Criteria:** respect concentration in index-heavy / mega-cap
   exposure — large-cap leaders often overlap with existing index-fund or
   mega-cap holdings.
6. **Required Data:** trend/relative-strength history, current liquidity
   (volume, spread), and current portfolio index/mega-cap concentration.
7. **Entry:** a defined entry structure confirming durable trend and
   institutional participation.
8. **Stop:** structure-based stop tied to the trend structure.
9. **Exit:** review against leadership deterioration, not only price
   action.
10. **Risk:** index/mega-cap concentration risk (§5 above) is this
    playbook's dominant additional risk.
11. **Invalidation:** structural trend failure or leadership
    deterioration.
12. **Failure Code:** ordinary non-match (no code) — inferred leadership
    without data is a structural non-match. No Trade: leadership is
    inferred from reputation rather than data.

### PB-012 — ETF Swing

1. **Objective:** express a verified market, sector, factor, or defensive
   view through a liquid ETF over a short (1–7 trading-day default)
   horizon.
2. **Applicable Market:** whichever regime the specific ETF's thesis
   requires (market/sector/factor/defensive) — this playbook's Applicable
   Market is thesis-dependent, not fixed.
3. **Scanner Criteria:** ETF liquidity and trend data sufficient to
   support a short-horizon swing.
4. **Market Criteria:** market-regime fit with the ETF's specific thesis
   (broad, sector, factor, or defensive).
5. **Portfolio Criteria:** account for overlap with existing sector and
   index exposure — an ETF position can silently duplicate exposure
   already held directly.
6. **Required Data:** ETF holdings/exposure breakdown, ETF liquidity
   (volume, spread, creation/redemption health if relevant), and trend
   data for the ETF itself.
7. **Entry:** ETF liquidity and trend confirming the underlying exposure
   thesis.
8. **Stop:** structure-based stop on the ETF itself.
9. **Exit:** default 1–7 trading-day horizon; review for underlying-thesis
   validity, not just the ETF's own chart.
10. **Risk:** creation/redemption or holdings-transparency risk for
    less-liquid or complex ETFs.
11. **Invalidation:** loss of ETF trend or underlying thesis.
12. **Failure Code:** `DATA_FAILURE` if holdings/exposure cannot be
    understood. Ordinary non-match (no code) if ETF liquidity is
    insufficient. No Trade: holdings / exposure cannot be understood or
    ETF liquidity is insufficient.

### PB-013 — Buy Call Momentum

1. **Objective:** express a qualified PB-001-equivalent momentum thesis
   through a Long Call when it improves risk-defined exposure over the
   underlying.
2. **Applicable Market:** same as PB-001 — Risk On or Strong Risk On,
   liquid sector leadership.
3. **Scanner Criteria:** all PB-001 Scanner Criteria, at the underlying
   level.
4. **Market Criteria:** all PB-001 Market Criteria, at the underlying
   level.
5. **Portfolio Criteria:** standard §9A limits at the underlying level;
   Options-specific portfolio aggregation is via `RISK_ENGINE.md` §28A
   Options Risk Bridge, not this Library.
6. **Required Data:** all PB-001 required data, plus the full
   `OPTIONS_ENGINE.md` §7 contract data set (chain, Greeks, IV Rank,
   liquidity) and the named risk factors in `OPTIONS_ENGINE.md` §5A.
7. **Entry:** a passing `OPTIONS_ENGINE.md` contract review on top of all
   PB-001 requirements.
8. **Stop:** maximum premium risk (Premium at Risk, not a price-distance
   stop) per `OPTIONS_ENGINE.md` §5A and `RISK_ENGINE.md` §28A.
9. **Exit:** SHALL follow `OPTIONS_ENGINE.md` §8A Time Risk Rules and §8B
   Profit and Loss Exit Rules in full (min 30 DTE at entry, hold ≤ 60% of
   entry DTE, mandatory review at 20% theta-driven premium loss, mandatory
   review on a ≥15-point intra-session IV Rank drop without a
   compensating price move, exit or re-justify at 5 DTE, mandatory review
   at 100%-of-premium gain or 50%-of-premium loss) — a correct underlying
   direction does not exempt this playbook from any of those seven
   triggers.
10. **Risk:** theta decay, IV crush, and time risk in addition to every
    PB-001 risk — this is the one playbook in this Library where time
    itself is a named risk factor, not merely a holding-period
    consideration.
11. **Invalidation:** underlying thesis failure, option liquidity
    deterioration, IV change, time exit, or expiry constraint.
12. **Failure Code:** `DATA_FAILURE` if any required option datum is
    unverified. Ordinary non-match (no code) if contract quality is below
    the `OPTIONS_ENGINE.md` §6 threshold. No Trade: any required option
    datum is unverified or contract quality is below threshold.

### PB-014 — Institutional Accumulation

1. **Objective:** capture continuation from a sustained, attributable
   accumulation pattern, explicitly rejecting an unattributed
   "institutional buying" narrative as a substitute for evidence.
2. **Applicable Market:** any supportive regime.
3. **Scanner Criteria:** repeated relative strength, volume behaviour, and
   price structure consistent with sustained accumulation.
4. **Market Criteria:** no material conflict with continued accumulation.
5. **Portfolio Criteria:** standard §9A limits only.
6. **Required Data:** sustained volume/price-structure history and any
   attributable source for the accumulation claim (e.g. filings); absent
   an attributable source, the claim must be labelled inference, not
   evidence.
7. **Entry:** confirmed accumulation structure with attributable evidence
   where available.
8. **Stop:** structure-based stop below the accumulation structure.
9. **Exit:** review against distribution signs, not a fixed target alone.
10. **Risk:** overconfidence risk from inferred participant identity —
    "institutional" without data is V4/V5 per `VERIFICATION_POLICY.md`.
11. **Invalidation:** loss of accumulation structure or material
    distribution.
12. **Failure Code:** `DATA_FAILURE` — this playbook's No-Trade condition
    is specifically an evidence gap. No Trade: the accumulation claim has
    no attributable evidence.

### PB-015 — Event Driven

1. **Objective:** trade a scheduled or confirmed event with a verified
   timing and a measurable, scenario-based thesis — not a prediction of
   the event's outcome.
2. **Applicable Market:** any regime; event risk is evaluated independent
   of broad regime.
3. **Scanner Criteria:** current liquidity sufficient to enter and exit
   around the event window.
4. **Market Criteria:** no specific regime requirement beyond standard
   verification; gap-risk timing per `EXECUTION_ENGINE.md` §3F applies at
   execution.
5. **Portfolio Criteria:** standard §9A limits only.
6. **Required Data:** official (S1) event date/time and details, current
   liquidity, and a defined scenario analysis for the plausible outcomes.
7. **Entry:** entry timing consistent with event and gap risk, per a
   defined scenario analysis.
8. **Stop:** structure-based stop, explicitly re-justified for gap
   exposure per `EXECUTION_ENGINE.md` §3F if held through the event.
9. **Exit:** review immediately after the event; account for gap risk
   realised.
10. **Risk:** this playbook explicitly SHALL define whether the position
    is held through the event — gap risk is materially different for a
    held-through-event position than one closed beforehand.
11. **Invalidation:** event cancellation, thesis contradiction, or adverse
    event outcome.
12. **Failure Code:** `DATA_FAILURE` — event timing, material facts, or
    risk capacity being unknown are all verification gaps. No Trade:
    event timing, material facts, or risk capacity are unknown.

---

## 4. Library Governance

New playbooks require documented rule fields (the identical 12-field
template in §2), data requirements, risk logic, failure conditions, and an
architecture consistency review. A playbook may be validated through
research or backtesting in a future release, but no untested result may be
represented as historical edge.

---

## 5. Playbook Lifecycle

Every playbook in §3 carries an implicit lifecycle status; when not stated
otherwise, a playbook is **Active**. The four states are:

| Status | New assignment allowed? | Existing positions | Trigger |
|---|---|---|---|
| **Active** | Yes | Followed per original rules | Default status for a documented, reviewed playbook |
| **Under Review** | Yes, with a flagged caveat in the report | Followed per original rules | Market-structure change suspected but not yet confirmed (e.g., a playbook's typical setups have been failing at an unusual rate) |
| **Suspended** | No | Existing positions continue to follow their original entry-time rules unchanged | Confirmed structural failure — the setup's evidence/trigger no longer reliably means what it used to |
| **Deprecated** | No | Historical record only — no open positions should reference it | Permanently retired, kept for audit/history, not reactivated without a new Library Governance review |

A candidate SHALL NOT be matched to a Suspended or Deprecated playbook — the
Playbook Engine's `NO INVENTION — MATCH ONLY` rule (`PLAYBOOK_ENGINE.md`
§3A) treats a Suspended/Deprecated entry the same as if it were absent from
the Library for new-assignment purposes. Changing a playbook's lifecycle
status is itself a change subject to `CONSTITUTION.md` §7 change control —
it is not a per-analysis judgment call by any engine.

---

End of Document

TRX Trading OS v1.0
