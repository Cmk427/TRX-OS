# TRX Trading OS
## DECISION_SNAPSHOT_POLICY.md

```text
Document ID      : TRX-DSN-001
Document Name    : Decision Snapshot Policy
Version          : 1.3.0
Status           : Active
Classification   : Critical
Dependencies     : VERIFICATION_POLICY.md
                   DATA_SOURCE_POLICY.md
                   MASTER_DECISION_ENGINE.md
Applies To       : Every completed analysis (State 16 onward)
```

---

## 1. Purpose

Without this policy, the same ticker analysed on the same day could produce
a different answer six months later with no way to explain why — was it new
data, a changed rule, a changed weighting model, or a changed playbook
definition? A Decision Snapshot is the fixed record that answers that
question. It is what makes a past decision auditable rather than merely
loggable.

Every analysis that reaches State 16 (Master Decision) SHALL produce exactly
one full Decision Snapshot (§2 below), regardless of outcome — `EXECUTE`,
`NO TRADE`, `WATCH`, or any other terminal status. The snapshot is appended
to the Master Decision Engine's audit record (`MASTER_DECISION_ENGINE.md`
§19); this document defines the required fields, not a separate storage
location.

**Early termination (never reaches State 16).** A run that stops at State
03 (malformed input) or State 04 (`INSUFFICIENT VERIFIED INFORMATION`) never
reaches the Master Decision Engine, so it cannot produce the full snapshot
above — there is no Master Decision to append one to. This is not a gap in
the audit trail: `OUTPUT_CONTRACT.md`'s Profile A report itself **is** the
audit record for this case, and SHALL include a **Reduced Snapshot**: Run ID
and timestamp, the specific failed critical input, its source/freshness
status, and the document versions of whichever policy (Verification or Data
Source) detected the failure. The engine or policy that detects the State
03/04 failure is responsible for this reduced record — not Master Decision
Engine, which never ran. `SYSTEM REVIEW REQUIRED` (State Machine §6A) is
also an early termination in this sense (it never reaches a clean State 16
outcome); it uses the same Reduced Snapshot fields plus the revision count
and the states involved in the unresolved loop.

---

## 2. Required Snapshot Fields

| Field | Content | Source |
|---|---|---|
| Decision ID | A unique, permanent identifier for this specific decision — distinct from Run ID, since one run may cover several candidates/positions, each needing its own Decision ID | Master Decision, per candidate/position |
| Run ID and timestamp | Unique identifier and the analysis timestamp | State 02 |
| Market data timestamp | As-of time for every material price/quote used | Verification ledger |
| Source ledger | Per material fact: source tier (S1–S5), timestamp, V-label (V1–V5) | `VERIFICATION_POLICY.md`, `DATA_SOURCE_POLICY.md` |
| Engine document versions | The `Version` field from every engine document invoked in this run (Market, Portfolio, Risk, Scanner, Playbook, Options, Decision, Committee, Red Team, Master Decision, Execution) | Each engine's header block |
| Weighting / Confidence Model version | The version of `MASTER_DECISION_ENGINE.md` §7 used to compute the WCS | `MASTER_DECISION_ENGINE.md` header |
| Playbook version | The `Version` of `playbooks/PLAYBOOK_LIBRARY.md` and the specific PB-ID assigned | `PLAYBOOK_LIBRARY.md` header |
| Prompt version *(reserved)* | If a future tool-level implementation drives this analysis with an LLM prompt, the prompt's version identifier | Reserved — not applicable to v1.0 manual/document-driven use |
| Model identifier *(reserved)* | If a future tool-level implementation uses a specific model to generate analysis, its identifier | Reserved — not applicable to v1.0 |
| Revision count | The §6A revision counter value from `STATE_MACHINE.md` at publication | State Machine |
| Final outcome and gate results | The published outcome plus each binding gate's pass/fail state | Master Decision |
| Human Action *(filled in after publication)* | What the human actually did: `EXECUTED AS PLANNED`, `EXECUTED DIFFERENTLY` (with what changed), `DECLINED`, `NO ACTION YET`, or `HUMAN OVERRIDE` (see `CONSTITUTION.md` §6A — includes what was overridden, reason, timestamp, risk-acceptance statement) | Human-supplied, appended post-publication |
| Outcome *(filled in during a later Position Review or Journal entry)* | What actually happened to the position/candidate afterward, append-only | Portfolio Engine Position Review (`RISK_ENGINE.md` §23) or a future Journal entry (`docs/ROADMAP.md` v1.2) |

The two "reserved" fields SHALL be present but marked `NOT APPLICABLE — v1.0
DOCUMENT-DRIVEN ANALYSIS` until a tool-level implementation exists. They are
listed now so that when `workflows/` (see `workflows/WORKFLOWS.md`) is
eventually populated, no snapshot silently omits them.

Human Action and Outcome are the only two fields that are legitimately
incomplete at publication time — they are appended later, append-only, per
§4 Immutability. Every other field SHALL be complete at publication.

---

## 3. Reproducibility Rule

A Decision Snapshot's purpose is to let a later reviewer answer: "given these
exact document versions and this exact data, would the same rules have
produced the same answer?" It is not a performance record and does not
imply the decision was correct — only that it is traceable.

If any required field cannot be populated, the analysis SHALL still
complete, but the Self Audit (State 20) SHALL flag the incomplete snapshot as
a defect per `OUTPUT_CONTRACT.md` §4.

---

## 4. Immutability

Per `CONSTITUTION.md` §7 (Versioning and Amendments) and
`SYSTEM.md` §7 (Audit Record), a Decision Snapshot is append-only. A later
document-version change never rewrites a prior snapshot's recorded versions
— it only affects snapshots for analyses run after the change.

---

## 5. Decision History

Day-over-day comparison (e.g. "MUU: yesterday Hold → today Reduce, because
—") requires more than a single snapshot; it requires retaining prior
snapshots to compare against. This section defines what that retention
must contain, without prescribing a runtime storage mechanism — TRX v1.0
has no implementation layer yet (`workflows/` is a placeholder; this
document's own Prompt-version/Model-identifier fields in §2 are marked N/A
for the identical reason).

**Required for comparison, once a future implementation exists:**

- One retained entry per Decision ID (§2), append-only, never overwritten.
- For each retained entry: ticker, the prior Decision (if any), the new
  Decision, and the as-of timestamp of both, so a later reviewer can state
  not just *what* changed but *between which two points in time*.
- The comparison itself SHALL cite the reason already recorded in the
  newer Decision Snapshot's rationale — it SHALL NOT infer a new
  explanation not already present in that record.

**Storage format and path: `RESERVED — NOT APPLICABLE to v1.0
document-driven analysis.`** This is listed now, the same way the two
"reserved" fields in §2 are, so that when `workflows/` (see
`workflows/WORKFLOWS.md`) is eventually populated, no implementation omits
day-over-day comparison as an afterthought. Concrete file paths, formats,
or a `.history/`-style layout are an implementation-layer concern
(`docs/ROADMAP.md` v2.1) — not a v1.0 document-driven decision.

---

End of Document

TRX Trading OS v1.0
