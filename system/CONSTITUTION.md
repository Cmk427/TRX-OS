# TRX Trading OS
## CONSTITUTION.md

```text
Document ID      : TRX-CON-001
Document Name    : System Constitution
Version          : 1.2.0
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
| Investment Policy | Define position/sector/cash concentration limits and capital-priority principles | Compute risk, decide an outcome, or override a veto |
| Market Engine | Classify market environment and deployment regime | Select a final trade |
| Portfolio Engine | Assess holdings, capacity, concentration, and portfolio fit | Override a risk veto |
| Risk Engine | Measure risk and issue binding risk vetoes | Override data failure or human authority |
| Scanner Engine | Produce a ranked candidate universe | Authorize execution |
| Playbook Engine | Match a candidate to one repeatable playbook | Invent a setup absent evidence |
| Options Engine | Compare approved option structures with underlying exposure | Recommend unverified or illiquid contracts |
| Committee Engine | Produce independent role-based assessments and preserve dissent | Publish the final recommendation |
| Red Team Engine | Seek counter-evidence and issue a binding critical-risk veto | Suppress the original thesis or dissent |
| Master Decision Engine | Integrate valid outputs and publish one final outcome | Override verification, Risk, or Red Team vetoes |
| Portfolio Optimization Engine | Convert a published outcome into target weight, shares, and capital | Re-decide Hold/Reduce/Exit/Execute |
| Capital Allocation Engine | Decide Deploy-vs-Wait for capital already released this run | Invent a new investment opportunity |
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

## 4A. Two Kinds of Authority

Several documents use the word "authority." This section is the single
definition every other document SHALL map to — it exists because
`RISK_ENGINE.md` describes itself as having "absolute veto authority" while
`MASTER_DECISION_ENGINE.md` describes itself as "the central decision
authority." Read literally and independently, those two claims collide. They
do not collide once "authority" is split into two non-competing axes:

| Kind | Definition | Held by | Can it publish the final report? | Can it be overridden? |
|---|---|---|---|---|
| **Constraint Authority** | The binding power to forbid an outcome. Purely negative — it can shrink what is allowed, never choose what is recommended. | Core Principles (absolute) · Verification / Data Source Policy (critical-data gate) · Risk Engine (hard veto) · Red Team Engine (critical-risk veto) | No | Only by a higher Constraint Authority in the §4 hierarchy — never by Publication Authority |
| **Publication Authority** | The sole right to integrate all valid, unconstrained outputs and emit the one final report artifact. Purely a publishing role — it has no power to forbid anything itself. | Master Decision Engine only | Yes — it is the only publisher | N/A — it does not compete with Constraint Authority because it has none to lose |

These are different axes, not a rivalry: Constraint Authority narrows the
menu of legal outcomes *before* Master Decision Engine acts; Publication
Authority only decides how to phrase and integrate what survives that
narrowing. A Risk Engine veto is never "overruled" by MDE publishing a
report — MDE is constitutionally required to publish exactly what the veto
demands (`NO TRADE`, `REDUCE`, or `EXIT`). Conversely, MDE never needs Risk
Engine's "permission" to publish, because publishing is not something Risk
Engine could grant or withhold in the first place — Risk Engine can only
narrow *what* gets published, never *who* publishes it.

Any document that calls a component "the final authority," "the highest
authority," or similar SHALL instead name which of the two kinds above it
means. `RISK_ENGINE.md` and `MASTER_DECISION_ENGINE.md` are both required to
use this terminology (see their respective Veto Authority / Publication
Authority sections).

**A note on Market Engine and Portfolio Engine's gates (§5).** These two
gates also forbid/restrict — Market Engine's gate can "Restrict deployment
or new-risk eligibility," Portfolio Engine's can "Require portfolio action
before new exposure" — which looks like Constraint Authority by the plain
meaning of "forbidding power." They are deliberately **not** listed in the
Constraint Authority row above, because Constraint Authority in this
document means something more specific: a **binding veto** that survives
all the way to the Master Decision Engine and constrains the *published
outcome* directly, overridable by nothing downstream. Market and Portfolio
gates are **eligibility gates**: they narrow what the Scanner (State 09) and
Decision Engine (State 12) may put forward as candidates in the first place,
consumed and absorbed earlier in the pipeline — by the time Master Decision
publishes, a Market/Portfolio restriction has already shaped which
candidates exist to rank, rather than persisting as a separate binding
constraint alongside Risk's and Red Team's vetoes. Put differently: Risk and
Red Team can force `NO TRADE` on a candidate that survived every earlier
stage; Market and Portfolio instead determine which candidates survive to
be evaluated. Both are real, non-optional constraints — only one kind is a
*veto* in the specific sense CONSTITUTION.md uses that word.

---

## 5. Gates and Vetoes

| Gate | Owner | Binding result |
|---|---|---|
| Critical data gate | Verification Policy / Data Source Policy | Stop with `INSUFFICIENT VERIFIED INFORMATION` |
| Market gate | Market Engine | Restrict deployment or new-risk eligibility |
| Portfolio gate | Portfolio Engine | Require portfolio action before new exposure |
| Preliminary and final risk gates | Risk Engine | `REJECT` / `NO TRADE`; no override |
| Committee review | Committee Engine | `WATCH` on no consensus; Risk Manager voting REDUCE/REJECT forces mandatory Risk Engine escalation |
| Counter-evidence gate | Red Team Engine | Critical risk veto or recommendation downgrade |
| Final integration | Master Decision Engine | One final report and outcome |

The Risk Manager's committee vote is an input to the Committee, not a veto in
itself — the Committee holds no veto power (§4A). A REDUCE/REJECT vote from
the Risk Manager forces a mandatory re-evaluation by the Risk Engine
(`COMMITTEE_ENGINE.md` §12); only that Risk Engine evaluation can produce a
binding veto, avoiding two competing risk authorities.

---

## 4B. Conflict Resolution Protocol

When Committee, Risk, and Red Team outputs disagree, priority is fixed and
non-negotiable — this restates §4/§5 as one explicit, quotable order rather
than requiring it to be re-derived from the hierarchy diagram each time:

```text
1. Risk Engine hard veto           (binding; no override — §4A, §5)
2. Red Team critical-risk veto     (binding; no override — §4A, §5)
3. Committee majority / consensus  (advisory; escalates to 1 on Risk Manager dissent)
4. Master Decision integration     (publishes what 1–3 permit; adds nothing)
```

A high Committee Consensus Score never outranks a Risk Engine veto, and a
Red Team critical-risk finding is binding even against unanimous Committee
approval. `MASTER_DECISION_ENGINE.md` §13 Conflict Resolution gives worked
examples of this exact ordering.

---

## 6. Independence and Dissent

Committee roles SHALL review the same verified evidence independently. The Red
Team SHALL receive the original thesis, assumptions, and evidence ledger, but
shall actively search for disconfirming evidence. Dissent, failed gates, and
unresolved conflicts MUST appear in the final report.

---

## 6A. Human Override

Rule 1 (§2) makes human authority over capital and order execution absolute.
TRX cannot prevent a human from acting against its published outcome — e.g.
trading despite a `NO TRADE`, or against a Risk Engine veto — because TRX
never holds broker access in the first place (`SYSTEM.md` §4). This
section governs what happens to the *record*, not what the human is
permitted to do in the real world.

If the human reports back that their real-world action diverged from what
TRX published, this SHALL be recorded (via
`DECISION_SNAPSHOT_POLICY.md`'s Human Action field) as a **Human Override**,
capturing: what was overridden (the specific outcome or veto), the human's
stated reason, a timestamp, and an explicit risk-acceptance statement. This
is a recording contract, not a system permission grant — TRX itself never
produces the overridden recommendation, and no engine's veto is retroactively
described as having been "passed."

---

## 7. Versioning and Amendments

Critical-rule changes require a documented rationale, affected-file list,
compatibility review, and version update. No amendment may retrospectively
alter a completed analysis. Changes that affect risk, evidence, authority, or
reporting must update `docs/ARCHITECTURE.md` and pass the consistency checklist.

### 7A. Change Checklist

Every change to a Critical-classification document (see each document's
`Classification` header field) SHALL confirm, before it is considered
complete:

- [ ] Rationale for the change is written down (why, not just what).
- [ ] Every affected file is listed (cross-references, dependency map,
      responsibility matrix, templates, examples).
- [ ] The `docs/ARCHITECTURE.md` and `docs/DEPENDENCY_MAP.md` consistency
      checklists have been run against the result.
- [ ] The document's `Version` header is bumped.
- [ ] No completed past analysis or Decision Snapshot is altered.

This is a lightweight self-review checklist, not a formal named-approver
workflow — appropriate for a single-owner project today. If TRX gains
additional contributors, this section is where a named approval role would
be added; until then, the checklist itself is the gate.

---

End of Document

TRX Trading OS v1.0
