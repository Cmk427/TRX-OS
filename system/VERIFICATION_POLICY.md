# TRX Trading OS
## VERIFICATION_POLICY.md

```text
Document ID      : TRX-VRF-001
Document Name    : Verification Policy
Version          : 1.2.0
Status           : Active
Classification   : Critical
Dependencies     : CORE_PRINCIPLES.md
                   DATA_SOURCE_POLICY.md
                   STATE_MACHINE.md
Applies To       : Entire System
```

---

## 1. Purpose

This policy defines how TRX Trading OS classifies information before analysis
or recommendation. `DATA_SOURCE_POLICY.md` defines source tiers, freshness, and
critical-data treatment; this policy defines evidence labels and reporting
behaviour.

No important statement may appear without a known verification status.

---

## 2. Evidence Labels

| Level | Meaning | Permitted presentation |
|---|---|---|
| V1 — Verified | Independently confirmed reliable, current information | State as a verified fact with source and timestamp |
| V2 — Partially Verified | Most material details confirmed; one or more remain unavailable | State limitation and reduce confidence |
| V3 — Estimated | Calculation derived from recorded inputs | Show method and input quality; never call it a fact |
| V4 — Inferred | Logical conclusion from labelled facts | Label as analysis / inference |
| V5 — Unknown | Materiality or value cannot be verified | List as unknown and explain impact |

The labels apply to market, company, financial, macro, news, technical,
portfolio, and option data. A recommendation must separate facts, calculations,
inferences, assumptions, and unknowns.

---

## 3. Verification Sequence

The State Machine requires verification in this order:

1. user input validity and portfolio identity;
2. current date, market session, and required market prices;
3. material scheduled events and macro context;
4. company, technical, and catalyst evidence;
5. option data when an option structure is considered; and
6. derived analysis, sizing, and recommendation eligibility.

No downstream score compensates for a failed critical upstream check.

---

## 4. Recommendation Eligibility

An `EXECUTE` candidate is eligible only if required data is usable under
`DATA_SOURCE_POLICY.md`, evidence is sufficient, risk is measurable, portfolio
impact is understood, a counterargument is completed, and every binding gate
passes. A critical V5 item produces `INSUFFICIENT VERIFIED INFORMATION`.

A non-critical unknown may be present only when it is explicitly declared,
does not support the thesis, and reduces confidence. A no-trade conclusion may
be reached with incomplete data when the uncertainty itself justifies not taking
risk.

---

## 4A. Verification Confidence Score

`MASTER_DECISION_ENGINE.md` §7 uses "Verification Confidence" as a 0–100
weighted input to the Confidence Model. It is defined here, once, so it is
never left for an agent to invent by analogy:

Verification Confidence is set by the **single worst V-label** among the
material facts feeding the decision (a run that reached State 16 has, by
definition, no critical fact at V5 — that would have already stopped the
run at State 04 per §4 above):

| Worst material V-label present | Verification Confidence |
|---|---|
| All material facts V1 | 100 |
| Worst is V2 | 90 |
| Worst is V3 | 75 |
| Worst is V4 | 60 |
| Worst is V5 (non-critical only) | 40 |

If more than one material fact sits at the worst label, the score does not
drop further per additional fact — the worst label alone sets the tier;
listing every affected fact is what the Evidence Status section (§5 below,
`OUTPUT_CONTRACT.md` §3 item 2) is for, not further score erosion. This
keeps the score simple and auditable rather than compounding uncertainty
twice (once in the score, once in the disclosed conflict list).

**Scope of "material facts": raw evidence only, not derived scores.** Every
V3 calculation (Risk Score, Opportunity Score, Candidate Quality Score, and
similarly derived numbers) is, by §2's own definition, a V3 estimate — if
these counted toward "worst material fact," almost no run could ever reach
Verification Confidence 100, since nearly every completed analysis has at
least one calculated score, making the top band practically unreachable and
defeating the score's purpose. Verification Confidence measures the quality
of the **raw evidentiary facts** the analysis is built on (prices, events,
catalysts, filings, portfolio data) — the calculations built from those
facts are a separate concern, already governed by their own formulas and
bands, and are excluded from this determination.

---

## 5. Contradictions and Missing Information

If sources disagree, the system SHALL identify the conflict, its source and
time context, its decision impact, and the resulting lower confidence. It SHALL
not quietly select a convenient source. Missing information appears in the
Evidence Status section of the final report.

---

## 6. Prohibited Behaviour

The system SHALL NOT invent prices, volume, earnings, catalysts, option flow,
analyst ratings, macro events, technical indicators, probabilities, or portfolio
values. It SHALL explicitly state when it cannot verify required information.

---

## 7. Audit Requirement

Before publication, the Master Decision Engine checks that every material
factual claim has a verification label, every V3 calculation retains inputs,
every V4 inference is not stated as fact, unknowns are disclosed, and no
recommendation depends on a failed critical-data gate.

---

End of Document

TRX Trading OS v1.0
