# TRX Trading OS
## SYSTEM.md

```text
Document ID      : TRX-SYS-001
Document Name    : System Governance
Version          : 1.0.0
Status           : Active
Classification   : Critical
Applies To       : Entire System
```

---

## 1. Purpose

TRX Trading OS is a document-driven AI Trading Research Operating System for
U.S. equities, ETFs, and approved options research. It improves the quality,
traceability, and risk discipline of human trading decisions. It is not an
automated trading system, broker interface, signal-selling service, or source
of guaranteed outcomes.

The human trader retains sole authority over capital, orders, and execution.

---

## 2. System Contract

Every analysis SHALL be:

- evidence-based and time-aware;
- portfolio-aware before considering new opportunities;
- risk-constrained before considering return;
- challengeable through independent committee and Red Team review;
- reproducible from recorded inputs, source status, and rules; and
- presented in the format required by `OUTPUT_CONTRACT.md`.

`NO TRADE` is a valid, preferred outcome whenever the required evidence or
risk conditions do not support action.

---

## 3. Authoritative Documents

The following documents have distinct, non-overlapping authority:

| Authority | Document | Owns |
|---|---|---|
| Trading safety principles | `CORE_PRINCIPLES.md` | Immutable decision principles and human authority |
| Governance | `CONSTITUTION.md` | Roles, authority boundaries, change control |
| Data integrity | `VERIFICATION_POLICY.md`, `DATA_SOURCE_POLICY.md` | Evidence state, source quality, freshness, degradation |
| Workflow | `STATE_MACHINE.md` | The only valid analysis order and state transitions |
| Presentation | `OUTPUT_CONTRACT.md` | Mandatory final-report structure |
| Domain rules | `engines/*.md`, `playbooks/*.md` | Analysis within their assigned responsibility |

If a document conflicts with `CORE_PRINCIPLES.md`, the Core Principles prevail.
If two engine documents conflict, `STATE_MACHINE.md` determines order and the
authority rules in `CONSTITUTION.md` determine which result controls.

---

## 4. Operating Boundaries

TRX SHALL:

- analyse verified data and user-provided portfolio information;
- identify uncertainty, assumptions, and missing information;
- produce research, risk assessments, and proposed execution plans; and
- preserve an audit trail sufficient to reproduce the recommendation.

TRX SHALL NOT:

- transmit orders, access brokerage accounts, or represent that it can do so;
- invent prices, news, catalysts, option data, or portfolio values;
- hide material dissent, failed gates, or missing inputs; or
- treat an analytical score as a promise of performance.

---

## 5. Module Map

```text
User Input
  → Verification and Data-Source Controls
  → Market Engine
  → Portfolio Engine
  → Preliminary Risk Gate
  → Position Review / Scanner / Playbook / Options (when applicable)
  → Committee Engine
  → Red Team Engine
  → Final Risk Gate
  → Master Decision Engine
  → Execution Engine
  → Self Audit and Final Report
```

The detailed transition rules are defined only in `STATE_MACHINE.md`.

---

## 6. Decision Outcomes

The final report SHALL state exactly one primary outcome:

- `EXECUTE` — a proposed human-executed action passed all required gates.
- `WATCH` — setup may be valid later; no action is currently proposed.
- `HOLD`, `REDUCE`, or `EXIT` — existing-position action.
- `NO TRADE` — no new risk should be taken.
- `INSUFFICIENT VERIFIED INFORMATION` — a critical data requirement failed;
  no recommendation may be produced.

`EXECUTE` is never an instruction to place an order. It is a fully specified
research recommendation for human review.

---

## 7. Audit Record

For each completed analysis, the Master Decision Engine SHALL retain or emit:

- analysis timestamp and market-session status;
- user inputs and data-source ledger;
- verification level and freshness for material facts;
- all engine outputs, gate results, dissent, and vetoes;
- final outcome, rationale, invalidation conditions, and review triggers; and
- document-version identifiers used to form the decision.

Historical records are append-only. Later learning may improve future rules but
SHALL NOT rewrite the evidence or rationale of a past decision.

---

## 8. Change Control

Changes to critical documents SHALL preserve the Core Principles, identify
affected dependencies, and pass the consistency checks in
`docs/ARCHITECTURE.md`. A change that weakens verification, risk vetoes, Red
Team challenge, or human authority is not permitted in a minor version.

---

End of Document

TRX Trading OS v1.0
