# TRX Trading OS
## DATA_SOURCE_POLICY.md

```text
Document ID      : TRX-DSP-001
Document Name    : Data Source Policy
Version          : 1.0.0
Status           : Active
Classification   : Critical
Dependencies     : CORE_PRINCIPLES.md
                   VERIFICATION_POLICY.md
                   STATE_MACHINE.md
Applies To       : All external and user-provided data
```

---

## 1. Purpose

This policy operationalises the verification requirements of
`VERIFICATION_POLICY.md`. It defines acceptable source classes, freshness,
critical-data handling, and the minimum audit record for any factual claim.

No source tier converts an unverified claim into a fact. Source quality,
timestamp, market-session context, and cross-check status determine the final
verification level.

---

## 2. Source Classes

| Tier | Typical source | Permitted use |
|---|---|---|
| S1 — Primary | Exchange, regulator, central bank, SEC filing, issuer filing | Authoritative facts and official events |
| S2 — Official market / issuer | Exchange-approved market feed, issuer IR release, broker statement supplied by user | Current quotes, account data, issuer communications |
| S3 — Established independent | Reputable financial news or research provider with attributable source and timestamp | Corroboration, context, discovery |
| S4 — Discovery only | Social media, anonymous commentary, unattributed aggregators | Lead generation only; never a verified fact |
| S5 — User-provided | Portfolio export, trading plan, manually entered value | Input pending validation; record provenance |

S1 or S2 is required for current price, market session, official filings,
corporate actions, and scheduled economic events whenever such data is material.

---

## 3. Critical Data and Freshness

| Data item | Required source / validation | Freshness rule | Failure behaviour |
|---|---|---|---|
| Current price, bid/ask, volume | S1/S2 market data | Timestamp and session stated; current to the analysis | Stop affected trade analysis |
| Market session and trading status | S1/S2 | Current session | Stop affected trade analysis |
| Option chain, Greeks, IV, OI | S1/S2 option data | Current to the analysis | No option recommendation |
| Earnings date, corporate action, filing | S1/S2, cross-check if conflicting | Publication date stated | Restrict or stop affected analysis |
| Macro release / calendar | S1 official source preferred | Event time and timezone stated | Reduce confidence; stop if critical |
| Portfolio positions, cash, buying power | Validated S5 or S2 statement | User-confirmed as of stated time | No new-position recommendation |
| Calculated score or sizing result | V3 calculation from recorded inputs | Inputs must satisfy their own freshness rules | Mark estimated; do not call verified |

Historical context may use an older timestamp only when it is explicitly marked
historical and is not presented as a current market fact.

---

## 4. Verification and Usability

The verification labels V1–V5 in `VERIFICATION_POLICY.md` are mandatory.

- V1: independently confirmed material fact.
- V2: partly confirmed; identify the missing material detail.
- V3: calculation derived from recorded V1/V2 inputs.
- V4: inference; never presented as fact.
- V5: unknown; identify the decision impact.

A critical item at V5 fails the critical-data gate. A non-critical V5 item may
remain in the report only as an explicit unknown and must reduce confidence.
An `EXECUTE` outcome may not depend on a V5 critical item.

---

## 5. Conflict Resolution

When sources disagree, the system SHALL record each source, time, and claimed
value; prefer the more authoritative and timely source; explain the remaining
uncertainty; and lower the verification level when the conflict is material.
The system SHALL not average contradictory values or silently select the more
convenient claim.

---

## 6. Evidence Ledger

Every material factual claim used in a recommendation SHALL be traceable in an
evidence ledger containing:

- claim or data field;
- source name and source tier;
- source timestamp and retrieval timestamp;
- market-session / timezone context where relevant;
- verification level and freshness status;
- calculation method for V3 values; and
- limitations, conflicts, or missing details.

The ledger may be included in the final report or retained as an appendix, but
its material results must be visible in the report's Verified Facts, Assumptions,
and Missing Information sections.

---

## 7. Prohibited Practices

TRX SHALL NOT use stale data as current without a timestamp, cite S4 material
as evidence, conceal unavailable data, infer a quote from narrative text, or
use a data source to suggest the system has broker access or execution ability.

---

End of Document

TRX Trading OS v1.0
