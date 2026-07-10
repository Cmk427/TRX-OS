# TRX Trading OS
## Dependency Map

```text
Document ID      : TRX-DEP-001
Document Name    : Dependency Map
Version          : 1.0.0
Status           : Active
Classification   : Reference
Dependencies     : STATE_MACHINE.md
                   ARCHITECTURE.md
Applies To       : Entire System
```

---

## 1. Purpose

This document traces every declared `Dependencies:` reference across
`system/`, `engines/`, and `playbooks/`, so a document change can be checked
against two rules before it is accepted:

1. **No forward dependency.** An engine SHALL NOT declare a hard dependency on
   an engine that runs at a *later* `STATE_MACHINE.md` state, because that
   engine's output does not exist yet when the earlier one runs.
2. **No self-referential decision.** A component that produces a final or
   binding output SHALL NOT be a declared input of a document that feeds it.

This is the "Decision depends on itself" check referenced in
`ARCHITECTURE.md`'s consistency checklist.

---

## 2. Foundational Core (peer cluster)

`CORE_PRINCIPLES.md` is the only document with no dependencies — it is the
root. `SYSTEM.md` and `CONSTITUTION.md` do not declare a formal
`Dependencies:` field but reference `CORE_PRINCIPLES.md` in prose as their
basis. `VERIFICATION_POLICY.md`, `DATA_SOURCE_POLICY.md`, and
`STATE_MACHINE.md` mutually reference one another:

```text
CORE_PRINCIPLES.md   (root — no dependencies)
  ├─ SYSTEM.md               (prose reference, no formal field)
  ├─ CONSTITUTION.md         (prose reference, no formal field)
  ├─ VERIFICATION_POLICY.md  ←→ DATA_SOURCE_POLICY.md ←→ STATE_MACHINE.md
  └─ OUTPUT_CONTRACT.md      (depends on STATE_MACHINE, VERIFICATION_POLICY, DATA_SOURCE_POLICY)
```

The three-way mutual reference between Verification, Data Source, and State
Machine is treated as an **allowed peer cluster**, not a violation: none of
the three is a downstream consumer of the others' final decision output — they
jointly define the evidence and workflow substrate every engine sits on. It is
declared here explicitly so a future change does not "fix" it into a strict
hierarchy that breaks their intentionally shared authority over State 04.

---

## 3. Engine Layer — by State Machine order

| State | Engine | Declared dependencies | Forward-dependency check |
|---|---|---|---|
| 05 | `MARKET_ENGINE.md` | Core Principles, State Machine, Verification, Data Source | None — governance only |
| 06 | `PORTFOLIO_ENGINE.md` | Core Principles, Verification, Data Source, State Machine | None — governance only |
| 07 / 15 | `RISK_ENGINE.md` | Market (05), Portfolio (06), State Machine, Constitution, Data Source | Pass — all upstream |
| 09 | `SCANNER_ENGINE.md` | Market (05), Portfolio (06), Risk (07/15), Verification, Data Source | Pass — all upstream |
| 10 | `PLAYBOOK_ENGINE.md` | Market (05), Scanner (09), `../playbooks/PLAYBOOK_LIBRARY.md` | Pass — see §5, corrected |
| 11 | `OPTIONS_ENGINE.md` | Market (05), Portfolio (06), Scanner (09), Playbook (10), Risk (07/15), Verification, Data Source | Pass — all upstream |
| 12 | `DECISION_ENGINE.md` | Core Principles, State Machine, Verification, Constitution, Data Source | Pass — governance only |
| 13 | `COMMITTEE_ENGINE.md` | Market (05), Portfolio (06), Scanner (09), Options (11), Decision (12), Constitution, State Machine | Pass — all upstream |
| 14 | `RED_TEAM_ENGINE.md` | Committee (13), Decision (12), Scanner (09), Market (05), Constitution, Data Source | Pass — all upstream |
| 16 | `MASTER_DECISION_ENGINE.md` | ALL ENGINES (05–15) | Pass by design — it is the aggregator; see §5 |
| 17 | `EXECUTION_ENGINE.md` | State Machine, Master Decision (16), Risk, Output Contract, Data Source | Pass — downstream of 16 |

`playbooks/PLAYBOOK_LIBRARY.md` depends on `PLAYBOOK_ENGINE.md` (parent),
`SCANNER_ENGINE.md` (09, upstream of 10), `RISK_ENGINE.md`, and
`VERIFICATION_POLICY.md` — pass.

---

## 4. Reverse Index — "who depends on me"

| Document | Depended on by |
|---|---|
| `CORE_PRINCIPLES.md` | Market, Portfolio, Decision, State Machine, Verification, Data Source |
| `STATE_MACHINE.md` | Output Contract, Execution, Market, Portfolio, Decision, Committee, Verification, Data Source |
| `CONSTITUTION.md` | Risk, Decision, Committee, Red Team |
| `VERIFICATION_POLICY.md` | Data Source, State Machine, Output Contract, Market, Portfolio, Scanner, Decision |
| `DATA_SOURCE_POLICY.md` | Verification, State Machine, Output Contract, Market, Portfolio, Risk, Scanner, Options, Red Team, Execution, Playbook Library |
| `MARKET_ENGINE.md` | Risk, Scanner, Options, Committee, Red Team, Playbook |
| `PORTFOLIO_ENGINE.md` | Risk, Scanner, Options, Committee |
| `RISK_ENGINE.md` | Scanner, Options, Committee (via Risk Manager vote), Execution, Playbook Library |
| `SCANNER_ENGINE.md` | Playbook, Options, Committee, Red Team, Playbook Library |
| `PLAYBOOK_ENGINE.md` | Options |
| `DECISION_ENGINE.md` | Committee, Red Team |
| `COMMITTEE_ENGINE.md` | Red Team |
| `MASTER_DECISION_ENGINE.md` | Execution |
| `OUTPUT_CONTRACT.md` | Execution (and every final report) |

No document in this index is depended on by anything upstream of its own
State Machine position — this is what keeps the graph acyclic outside the
declared peer cluster in §2.

---

## 5. Corrected Issue — Playbook ↔ Master Decision cycle

`PLAYBOOK_ENGINE.md` previously listed `MASTER_DECISION_ENGINE.md` as a
dependency. Since `MASTER_DECISION_ENGINE.md` declares `ALL ENGINES` as its
own dependency, this created a cycle:

```text
PLAYBOOK_ENGINE.md → MASTER_DECISION_ENGINE.md → (ALL ENGINES) → PLAYBOOK_ENGINE.md
```

This also violated the forward-dependency rule in §1: Playbook Assignment is
State 10; Master Decision is State 16, eleven states and two binding gates
later. Playbook Engine's own body never references Master Decision Engine in
its rules — the line was corrected by removing it. Playbook Engine's
dependencies are now Market Engine (05), Scanner Engine (09), and
`PLAYBOOK_LIBRARY.md`, all consistent with §1.

---

## 6. Consistency Checklist (run on any dependency change)

- [ ] The new/changed dependency points to an earlier or equal-priority
      State Machine state, or to a governance document with no state.
- [ ] The change does not make `MASTER_DECISION_ENGINE.md` a dependency of
      any engine other than `EXECUTION_ENGINE.md`.
- [ ] The change does not make any engine a dependency of
      `VERIFICATION_POLICY.md` or `DATA_SOURCE_POLICY.md` (they must remain
      foundational to every engine, never the reverse).
- [ ] `docs/ARCHITECTURE.md` and `docs/RESPONSIBILITY_MATRIX.md` still match
      the corrected authority and dependency picture.

---

End of Document

TRX Trading OS v1.0
