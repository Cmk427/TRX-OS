# TRX Trading OS
## Responsibility Matrix

```text
Document ID      : TRX-RSM-001
Document Name    : Responsibility Matrix
Version          : 1.0.0
Status           : Active
Classification   : Reference
Dependencies     : CONSTITUTION.md
                   STATE_MACHINE.md
                   ARCHITECTURE.md
Applies To       : Entire System
```

---

## 1. Purpose

This is a quick-reference index of who owns what. It restates, in one table,
the responsibility boundaries already defined in `system/CONSTITUTION.md` §3
and the engine boundaries in `docs/ARCHITECTURE.md`. If this table and
`CONSTITUTION.md` ever disagree, **`CONSTITUTION.md` prevails** — this
document is a derived view, not an independent source of authority.

---

## 2. Governance Layer

| Component | Primary responsibility | Authority | Cannot do |
|---|---|---|---|
| Human trader | Capital allocation, order execution | Absolute and final | Delegate execution authority to the system |
| `CORE_PRINCIPLES.md` | Immutable trading-safety principles | Overrides every other document | Be overridden by a lower document |
| `CONSTITUTION.md` | Roles, authority boundaries, change control | Defines every veto below | Replace Core Principles |
| `STATE_MACHINE.md` | Sole valid processing order | Defines legal transitions and recovery | Be redefined by any engine |
| `VERIFICATION_POLICY.md` | V1–V5 evidence classification | Gates State 04 (critical-data stop) | Convert an estimate into a fact |
| `DATA_SOURCE_POLICY.md` | Source tier, freshness, critical-data rules | Gates State 04 alongside Verification | Waive freshness for a material claim |
| `OUTPUT_CONTRACT.md` | Mandatory final-report structure | Defines what "complete" means | Add or remove a workflow state |

---

## 3. Engine Layer

| Engine | State(s) | May do | May not do | Veto power |
|---|---|---|---|---|
| Market Engine | 05 | Classify regime, trend, breadth, volatility, macro, sector rotation | Select a trade | Restrict deployment / new-risk eligibility |
| Portfolio Engine | 06, 08 | Assess holdings, capacity, concentration, correlation, cash | Override a risk veto | Require portfolio action before new exposure |
| Risk Engine | 07, 15 | Measure risk, size positions, issue binding vetoes | Override data failure or human authority | Hard veto — binding `NO TRADE` / `REDUCE` / `EXIT`, no override |
| Scanner Engine | 09 | Build ranked candidate universe with exclusions | Authorize execution | None (advisory ranking only) |
| Playbook Engine | 10 | Match exactly one repeatable playbook | Invent a setup absent evidence | Remove candidate from ranking on no-match |
| Options Engine | 11 | Compare Long Call vs. underlying, Greeks, IV, liquidity | Recommend unverified/illiquid contracts, form an independent thesis | Remove option structure on failed check |
| Decision Engine | 12 | Rank candidates, compute Opportunity Score | Publish a final outcome | Remove candidate on insufficient evidence |
| Committee Engine | 13 | Six independent role votes, preserve dissent | Publish the final recommendation | `WATCH` on no consensus; Risk Manager dissent routes to Risk Engine |
| Red Team Engine | 14 | Seek counter-evidence, counter-thesis, alternatives | Suppress the original thesis or dissent | Critical-risk veto — binding downgrade or `NO TRADE` |
| Master Decision Engine | 16 | Integrate all valid outputs into one outcome | Override verification, Risk, or Red Team vetoes | None — it enforces upstream vetoes, it does not hold one |
| Execution Engine | 17 | Produce human-reviewable entry/stop/target/size plan | Place, transmit, or imply placement of an order | `DO NOT EXECUTE — REVERIFY` on stale critical input |

---

## 4. Playbook Layer

| Component | Primary responsibility | Authority | Cannot do |
|---|---|---|---|
| `playbooks/PLAYBOOK_LIBRARY.md` | Repeatable setup rules (context, trigger, invalidation, risk, no-trade conditions) | Defines the only setups Playbook Engine may assign | Claim backtested or guaranteed performance |

---

## 5. Reading the Veto Column

"Veto power" means a **binding** result that a later, lower-priority engine
cannot reverse. Per `CONSTITUTION.md` §4–5, the enforceable order is:

```text
Human Trader
  → Core Principles / critical-data gate
     → Risk Engine hard veto / Red Team critical-risk veto
        → State Machine transition requirements
           → Master Decision Engine final integration
              → Committee and specialist analysis
```

A component not listed with a veto (Market, Portfolio, Scanner, Playbook,
Options, Decision, Committee, Master Decision) still constrains eligibility or
ranking — it simply cannot issue a binding override the way Risk and Red Team
can. See [Dependency Map](DEPENDENCY_MAP.md) for how these responsibilities
translate into document-level dependencies, and [Architecture](ARCHITECTURE.md)
for the full data-flow diagram.

---

End of Document

TRX Trading OS v1.0
