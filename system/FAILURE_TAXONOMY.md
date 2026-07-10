# TRX Trading OS
## FAILURE_TAXONOMY.md

```text
Document ID      : TRX-FTX-001
Document Name    : Failure Taxonomy
Version          : 1.0.0
Status           : Active
Classification   : Critical
Dependencies     : STATE_MACHINE.md
                   OUTPUT_CONTRACT.md
                   RISK_ENGINE.md
                   DATA_SOURCE_POLICY.md
Applies To       : Every non-EXECUTE terminal outcome
```

---

## 1. Purpose

`STATE_MACHINE.md` and `OUTPUT_CONTRACT.md` already define terminal
outcomes (`NO TRADE`, `INSUFFICIENT VERIFIED INFORMATION`,
`DO NOT EXECUTE — REVERIFY`, and the new `SYSTEM REVIEW REQUIRED`), but they
don't classify *why* a run ended that way in an auditable, filterable way.
This taxonomy exists so that, across many runs, someone auditing the system
can ask "how often do we fail because of bad data versus because of risk
rejection versus because the engines disagreed with each other" — a
question the terminal-status vocabulary alone cannot answer.

This document adds a classification layer on top of existing outcomes. It
does **not** create new terminal statuses beyond `SYSTEM REVIEW REQUIRED`
(already introduced in `STATE_MACHINE.md` §6A) and does not change any
engine's authority or veto behaviour.

---

## 2. Categories

| Category | Definition | Maps to terminal status | Typical trigger |
|---|---|---|---|
| `DATA_FAILURE` | A required input could not be verified to the minimum level `DATA_SOURCE_POLICY.md` requires | `INSUFFICIENT VERIFIED INFORMATION` | Stale/missing price, unverifiable catalyst, unavailable option chain |
| `MODEL_FAILURE` | A required calculation could not be computed because a model input was `UNKNOWN`, per `RISK_ENGINE.md` §26A or `MASTER_DECISION_ENGINE.md` §7 | Routes to `RISK_REJECTION` (if the uncomputable model is Risk Score/Heat) or `SYSTEM REVIEW REQUIRED` (if the failure recurs across revisions) | Missing stop price, missing position size, WCS inputs incomplete beyond redistribution rules |
| `CONFLICT_FAILURE` | Two or more engines/gates produced results that could not be reconciled within the allowed revision budget, or Committee reached no consensus | `WATCH` (Committee no-consensus, §STATE_MACHINE §4) or `SYSTEM REVIEW REQUIRED` (revision-cycle limit exceeded, `STATE_MACHINE.md` §6A) | Repeated Master Decision ↔ Risk ↔ Self Audit disagreement; split Committee vote |
| `RISK_REJECTION` | A binding Risk Engine or Red Team veto blocked the candidate or required existing-position action | `NO TRADE`, `REDUCE`, or `EXIT` | Risk Score below 70, Portfolio Heat critical, Red Team critical-risk finding |
| `EXECUTION_INVALID` | An otherwise-approved plan could not be executed as planned due to stale or invalid execution-time data | `DO NOT EXECUTE — REVERIFY` | Quote gone stale between publication and review, market session mismatch, halted security |
| `SYSTEM_ERROR` | A tool-level runtime failure unrelated to trading logic (reserved for a future implementation; not a v1.0 document-driven outcome) | Implementation-defined | Reserved for `workflows/` — no v1.0 document-level trigger exists |

---

## 3. Classification Rule

Every completed analysis that does not end in `EXECUTE` SHALL record exactly
one primary category from §2 in its Decision Snapshot
(`DECISION_SNAPSHOT_POLICY.md`). If more than one category's trigger
condition is present, record the earliest-encountered one in the State
Machine order (`STATE_MACHINE.md` §2) — for example, a run that hits a data
failure at State 04 is `DATA_FAILURE` even if it would also have failed Risk
later, because it never reached the Risk Gate.

---

## 4. Non-Goals

This taxonomy does not rank failure categories by severity, does not imply
any category is more "acceptable" than another, and does not change the
binding nature of any veto described in `CONSTITUTION.md` §4A. `NO TRADE`
classified as `RISK_REJECTION` is exactly as valid and complete an outcome as
`EXECUTE` — this document exists for audit clarity, not to discourage
protective outcomes.

---

End of Document

TRX Trading OS v1.0
