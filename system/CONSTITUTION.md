# TRX Trading OS
## CONSTITUTION.md

```text
Document ID      : TRX-CON-001
Document Name    : System Constitution
Version          : 1.0.0
Status           : Active
Classification   : Critical
Applies To       : Entire System
```

---

## 1. Purpose

This Constitution assigns responsibility and authority without replacing the
trading-safety principles in `CORE_PRINCIPLES.md`. It prevents an analytical
engine from presenting a conclusion as a final recommendation or overriding a
valid safety veto.

---

## 2. Non-Negotiable Constitutional Rules

1. Human authority over capital and order execution is absolute.
2. Verified facts, analysis, inferences, assumptions, and recommendations
   SHALL remain distinct.
3. No module may bypass the workflow in `STATE_MACHINE.md`.
4. A hard veto is binding; a lower-priority engine cannot override it.
5. Every `EXECUTE` outcome requires an auditable record and a written
   counter-thesis.
6. Cash and `NO TRADE` remain valid portfolio decisions.

---

## 3. Responsibility Boundaries

| Component | May do | May not do |
|---|---|---|
| Verification / Data Policy | Classify evidence and its usability | Infer a trade thesis |
| Market Engine | Classify market environment and deployment regime | Select a final trade |
| Portfolio Engine | Assess holdings, capacity, concentration, and portfolio fit | Override a risk veto |
| Risk Engine | Measure risk and issue binding risk vetoes | Override data failure or human authority |
| Scanner Engine | Produce a ranked candidate universe | Authorize execution |
| Playbook Engine | Match a candidate to one repeatable playbook | Invent a setup absent evidence |
| Options Engine | Compare approved option structures with underlying exposure | Recommend unverified or illiquid contracts |
| Committee Engine | Produce independent role-based assessments and preserve dissent | Publish the final recommendation |
| Red Team Engine | Seek counter-evidence and issue a binding critical-risk veto | Suppress the original thesis or dissent |
| Master Decision Engine | Integrate valid outputs and publish one final outcome | Override verification, Risk, or Red Team vetoes |
| Execution Engine | Translate an approved outcome into a human review checklist | Place, transmit, or imply placement of an order |

---

## 4. Authority Hierarchy

```text
Human Trader
  └─ Core Principles and critical-data requirements
       └─ Risk Engine hard veto / Red Team critical-risk veto
            └─ State Machine transition requirements
                 └─ Master Decision Engine final integration
                      └─ Committee and specialist analysis
```

The hierarchy describes constraint authority, not analytical value. The Master
Decision Engine is the only component allowed to publish the final integrated
outcome, but it SHALL publish `NO TRADE` or
`INSUFFICIENT VERIFIED INFORMATION` when an upstream authority requires it.

---

## 5. Gates and Vetoes

| Gate | Owner | Binding result |
|---|---|---|
| Critical data gate | Verification Policy / Data Source Policy | Stop with `INSUFFICIENT VERIFIED INFORMATION` |
| Market gate | Market Engine | Restrict deployment or new-risk eligibility |
| Portfolio gate | Portfolio Engine | Require portfolio action before new exposure |
| Preliminary and final risk gates | Risk Engine | `REJECT` / `NO TRADE`; no override |
| Committee review | Committee Engine | `WATCH` on no consensus; Risk Manager rejection is a risk veto |
| Counter-evidence gate | Red Team Engine | Critical risk veto or recommendation downgrade |
| Final integration | Master Decision Engine | One final report and outcome |

The Risk Manager's committee vote is an input to the Committee. A binding risk
veto is recorded and enforced by the Risk Engine, avoiding two competing risk
authorities.

---

## 6. Independence and Dissent

Committee roles SHALL review the same verified evidence independently. The Red
Team SHALL receive the original thesis, assumptions, and evidence ledger, but
shall actively search for disconfirming evidence. Dissent, failed gates, and
unresolved conflicts MUST appear in the final report.

---

## 7. Versioning and Amendments

Critical-rule changes require a documented rationale, affected-file list,
compatibility review, and version update. No amendment may retrospectively
alter a completed analysis. Changes that affect risk, evidence, authority, or
reporting must update `docs/ARCHITECTURE.md` and pass the consistency checklist.

---

End of Document

TRX Trading OS v1.0
