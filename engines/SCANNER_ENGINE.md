# TRX Trading OS
## SCANNER_ENGINE.md

```text
Document ID      : TRX-SCN-001
Document Name    : Scanner Engine
Version          : 1.0.0
Status           : Active
Classification   : Core
Dependencies     : MARKET_ENGINE.md
                   PORTFOLIO_ENGINE.md
                   RISK_ENGINE.md
                   VERIFICATION_POLICY.md
                   DATA_SOURCE_POLICY.md
Applies To       : Candidate Discovery and Ranking
```

---

## 1. Purpose

The Scanner Engine creates a traceable, ranked candidate universe for further
research. It discovers opportunities; it does not authorise a trade, set final
position size, or replace the Decision, Committee, Red Team, Risk, or Master
Decision engines.

New-candidate scanning begins only after Market and Portfolio review and the
preliminary Risk Gate. Existing-position review remains higher priority.

---

## 2. Candidate Universe

The supported universe is U.S. stocks and ETFs. A candidate may be marked as a
Long Call candidate only for subsequent Options Engine review; no option
contract is screened or recommended here.

Each scan SHALL record the universe definition, scan timestamp, market session,
exclusions, source coverage, and any survivorship or data-availability limit.

---

## 3. Required Inputs

The Scanner uses verified or explicitly classified data for market regime,
symbol identity, current price, liquidity, volume, trend, relative strength,
technical structure, catalyst status, and sector context. It also receives
portfolio restrictions, prohibited exposures, and current risk/deployment
limits.

Critical quote or session failure prevents the affected candidate from being
ranked. Unknown non-critical fields are recorded as V5 and reduce confidence;
they are never silently assumed bullish.

---

## 4. Screening Stages

1. **Eligibility** — valid U.S. symbol, tradable instrument, sufficient data,
   and no portfolio or risk exclusion.
2. **Liquidity** — assess average volume, spread, tradability, and expected
   exit difficulty.
3. **Market Alignment** — test fit with the current regime, sector rotation,
   volatility, and macro-event constraints.
4. **Setup Quality** — assess trend, relative strength, momentum, volume
   expansion, price structure, and a verified or explicitly absent catalyst.
5. **Portfolio Fit** — identify duplicate exposure, correlation, sector/theme
   concentration, and buying-capacity impact.
6. **Ranking** — rank surviving candidates and preserve exclusions with their
   reasons.

---

## 5. Candidate Quality Score

The Scanner produces a preliminary Candidate Quality Score, not the final
Decision Engine Opportunity Score. It uses these transparent weights:

| Component | Weight |
|---|---:|
| Market and sector alignment | 20% |
| Trend and technical structure | 20% |
| Relative strength and momentum | 15% |
| Volume and liquidity | 15% |
| Catalyst evidence | 15% |
| Portfolio fit | 15% |

Scores are only comparable within the same recorded scan universe and data
timestamp. Suggested labels are: 90+ Elite Research Candidate; 80–89 Qualified;
70–79 Watchlist; below 70 Exclude. Score labels are research priorities, not
recommendations.

---

## 6. Output Contract

The Scanner SHALL provide a ranked list with at most ten research candidates.
For each candidate report ticker, source timestamp, Candidate Quality Score,
market/sector fit, key verified facts, identified setup, liquidity status,
catalyst state, portfolio conflict, unknowns, and the next required gate.

The final report may surface at most three new opportunities, as required by
`OUTPUT_CONTRACT.md`. All excluded candidates with a material risk or
verification issue must retain an exclusion reason in the audit record.

---

## 7. Failure Conditions

The Scanner returns `NO ELIGIBLE CANDIDATE` when the market does not support new
risk, portfolio capacity is unavailable, data coverage is inadequate, or no
candidate passes eligibility. This is a successful protective outcome and is
not a reason to relax filters.

---

End of Document

TRX Trading OS v1.0
