# TRX Trading OS
## PLAYBOOK_LIBRARY.md

```text
Document ID      : TRX-PBL-001
Document Name    : Playbook Library
Version          : 1.0.0
Status           : Active
Classification   : Core
Dependencies     : PLAYBOOK_ENGINE.md
                   SCANNER_ENGINE.md
                   RISK_ENGINE.md
                   VERIFICATION_POLICY.md
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

Every playbook uses the following fields:

- **Regime / context** — market and sector conditions in which the setup may be
  researched.
- **Evidence and trigger** — observable, timestamped conditions; never a
  prediction.
- **Invalidation** — the condition that breaks the setup before or after entry.
- **Risk and exit** — a structure-based stop, maximum loss, target / review,
  and event-risk controls under `RISK_ENGINE.md`.
- **No-trade conditions** — situations that prohibit classification or action.

The Playbook Match Score evaluates fit with the complete rule set: 90+ Excellent,
80+ Qualified, 70+ Weak, below 70 Reject. `PLAYBOOK_ENGINE.md` requires 85+ for
execution eligibility. The score is not evidence of future performance.

---

## 3. Playbooks

### PB-001 — Momentum Breakout

- **Context:** supportive market regime, liquid sector leadership, and a defined
  consolidation or resistance area.
- **Evidence and trigger:** verified trend, relative strength, volume expansion,
  and a confirmed break above the recorded trigger area.
- **Invalidation:** failed breakout, loss of the breakout level, or material
  market/sector deterioration.
- **Risk and exit:** structure-based stop; review follow-through and take
  profits only against recorded resistance / risk-reward logic.
- **No trade:** weak liquidity, unverified volume, extended price without a
  defined stop, or hostile market regime.

### PB-002 — Gap & Go

- **Context:** a verified gap driven by a material catalyst in a liquid asset.
- **Evidence and trigger:** current quote, gap source, opening liquidity, and
  sustained intraday strength rather than merely a pre-market move.
- **Invalidation:** gap fill or loss of the defined intraday support / volume
  confirmation.
- **Risk and exit:** use a pre-defined intraday stop and time-aware review;
  gap and slippage risk must be explicit.
- **No trade:** unverified catalyst, wide spread, thin liquidity, or late entry.

### PB-003 — Post Earnings Continuation

- **Context:** verified earnings event with a sustained market/sector-aligned
  reaction.
- **Evidence and trigger:** official earnings source, price response, volume,
  guidance context, and post-event support.
- **Invalidation:** reversal of the reaction structure, guidance contradiction,
  or market risk-off change.
- **Risk and exit:** account for event gap risk already realised and identify
  continuation / failure levels.
- **No trade:** earnings details or current reaction cannot be verified.

### PB-004 — Trend Pullback

- **Context:** established liquid uptrend in a supportive regime.
- **Evidence and trigger:** retracement to a recorded support / trend area with
  renewed relative strength or volume confirmation.
- **Invalidation:** break of the trend structure or support that defines the
  thesis.
- **Risk and exit:** stop below structural invalidation; manage against prior
  highs and reward/risk requirements.
- **No trade:** no established trend, pullback is disorderly, or support is
  undefined.

### PB-005 — Relative Strength Leader

- **Context:** asset demonstrates verified relative strength versus relevant
  index, sector, and peers.
- **Evidence and trigger:** persistent relative strength with liquid trend and
  a valid entry structure.
- **Invalidation:** relative strength deterioration plus structural failure.
- **Risk and exit:** control concentration if the portfolio already holds the
  same leader / sector.
- **No trade:** strength is based on stale comparisons or duplicates exposure.

### PB-006 — Sector Rotation

- **Context:** broad, verified sector leadership or capital-flow shift.
- **Evidence and trigger:** sector relative strength, breadth, market alignment,
  and a liquid representative stock or ETF.
- **Invalidation:** rotation evidence reverses or sector leadership loses trend.
- **Risk and exit:** diversify exposure and use sector-level invalidation.
- **No trade:** a single ticker move is the only evidence of rotation.

### PB-007 — High Volume Reversal

- **Context:** a verified reversal at a meaningful support/resistance area.
- **Evidence and trigger:** exceptional relative volume, price rejection or
  recovery, and confirmation rather than a single noisy bar.
- **Invalidation:** failure of the reversal level or renewed adverse trend.
- **Risk and exit:** tighter structural risk controls due to reversal failure
  probability.
- **No trade:** volume is unverified, liquidity is weak, or catalyst risk is
  unknown and material.

### PB-008 — Short Squeeze

- **Context:** exceptional, high-risk event setup with verified liquidity and
  catalyst / positioning evidence where available.
- **Evidence and trigger:** actual price/volume behaviour and verified source
  context; inferred short interest must be labelled as inference.
- **Invalidation:** loss of momentum / liquidity or a structural reversal.
- **Risk and exit:** small, predefined risk only; account for extreme gap and
  execution risk.
- **No trade:** unknown liquidity, unverified thesis, or risk budget cannot
  absorb a sharp reversal.

### PB-009 — AI Momentum

- **Context:** an AI-related company or ETF has verified relative strength,
  catalyst, and market alignment.
- **Evidence and trigger:** use the same trend, liquidity, and catalyst evidence
  required for Momentum Breakout; thematic label is not evidence.
- **Invalidation:** thesis / trend failure or theme-concentration breach.
- **Risk and exit:** assess correlated AI exposure already in the portfolio.
- **No trade:** the only rationale is a popular AI narrative.

### PB-010 — Defense Rotation

- **Context:** defensive sectors or instruments demonstrate verified leadership
  during neutral, risk-off, or elevated-volatility conditions.
- **Evidence and trigger:** defensive relative strength, market risk context,
  and a liquid entry structure.
- **Invalidation:** regime normalises or defensive leadership breaks down.
- **Risk and exit:** match lower-beta intent with portfolio heat and cash needs.
- **No trade:** broad market is strongly risk-on without supporting defensive
  evidence.

### PB-011 — Large Cap Leadership

- **Context:** a liquid large-cap leader shows durable trend, institutional
  participation evidence, and market alignment.
- **Evidence and trigger:** verified liquidity, relative strength, trend, and
  a defined entry structure.
- **Invalidation:** structural trend failure or leadership deterioration.
- **Risk and exit:** respect concentration in index-heavy / mega-cap exposure.
- **No trade:** leadership is inferred from reputation rather than data.

### PB-012 — ETF Swing

- **Context:** liquid ETF expresses a verified market, sector, factor, or
  defensive view with a 1–7 trading-day horizon by default.
- **Evidence and trigger:** ETF liquidity, underlying exposure, trend, and
  market-regime fit.
- **Invalidation:** loss of ETF trend or underlying thesis.
- **Risk and exit:** account for overlap with existing sector and index exposure.
- **No trade:** holdings / exposure cannot be understood or ETF liquidity is
  insufficient.

### PB-013 — Buy Call Momentum

- **Context:** a qualified momentum underlying has passed stock-level research
  and an option may improve risk-defined exposure.
- **Evidence and trigger:** all PB-001 requirements plus a passing
  `OPTIONS_ENGINE.md` contract review.
- **Invalidation:** underlying thesis failure, option liquidity deterioration,
  IV change, time exit, or expiry constraint.
- **Risk and exit:** maximum premium risk, theta, IV, strike, expiry, and exit
  plan must be explicit.
- **No trade:** any required option datum is unverified or contract quality is
  below threshold.

### PB-014 — Institutional Accumulation

- **Context:** liquid asset shows a sustained, evidence-backed accumulation
  pattern in a supportive market.
- **Evidence and trigger:** repeated relative strength, volume behaviour, price
  structure, and attributable evidence. “Institutional” without data is V4/V5.
- **Invalidation:** loss of accumulation structure or material distribution.
- **Risk and exit:** use structural invalidation and avoid overconfidence from
  inferred participant identity.
- **No trade:** the accumulation claim has no attributable evidence.

### PB-015 — Event Driven

- **Context:** scheduled or confirmed event with a verified timing and a
  measurable trading thesis.
- **Evidence and trigger:** official event source, scenario analysis, liquidity,
  and entry timing consistent with event and gap risk.
- **Invalidation:** event cancellation, thesis contradiction, or adverse event
  outcome.
- **Risk and exit:** explicitly define whether the position is held through the
  event; account for gap risk and review immediately after the event.
- **No trade:** event timing, material facts, or risk capacity are unknown.

---

## 4. Library Governance

New playbooks require documented rule fields, data requirements, risk logic,
failure conditions, and an architecture consistency review. A playbook may be
validated through research or backtesting in a future release, but no untested
result may be represented as historical edge.

---

End of Document

TRX Trading OS v1.0
