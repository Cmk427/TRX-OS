# TRX Trading OS
## Responsibility Matrix

```text
Document ID      : TRX-RSM-001
Document Name    : Responsibility Matrix
Version          : 1.2.0
Status           : Active
Classification   : Reference
Dependencies     : CONSTITUTION.md
                   STATE_MACHINE.md
                   ARCHITECTURE.md
                   DECISION_SNAPSHOT_POLICY.md
                   FAILURE_TAXONOMY.md
                   PARAMETER_REGISTRY.md
                   DOCUMENTATION_GOVERNANCE.md
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
| `STATE_MACHINE.md` | Sole valid processing order | Defines legal transitions, recovery, and the §6A revision-cycle limit (max 2, then `SYSTEM REVIEW REQUIRED`) | Be redefined by any engine |
| `VERIFICATION_POLICY.md` | V1–V5 evidence classification | Gates State 04 (critical-data stop) | Convert an estimate into a fact |
| `DATA_SOURCE_POLICY.md` | Source tier, freshness, critical-data rules | Gates State 04 alongside Verification | Waive freshness for a material claim |
| `OUTPUT_CONTRACT.md` | Mandatory final-report structure | Defines what "complete" means | Add or remove a workflow state |
| `DECISION_SNAPSHOT_POLICY.md` | What must be recorded per published decision | Defines the audit/reproducibility record | Alter a past snapshot |
| `ENGINE_INTERFACE_CONTRACT.md` | Structured engine-to-engine schema | Naming/shape only | Change any engine's authority or veto behaviour |
| `FAILURE_TAXONOMY.md` | Classification of non-`EXECUTE` outcomes | Defines audit categories only | Create a new terminal status or rank vetoes by severity |
| `PARAMETER_REGISTRY.md` | Numeric-threshold index | Convenience lookup only | Override its owning engine document if they ever disagree |
| `DOCUMENTATION_GOVERNANCE.md` | Header standard for new documents | Applies to documents created from this point forward | Require retrofitting existing files |

---

## 3. Engine Layer

Per `CONSTITUTION.md` §4A: only Risk and Red Team hold **Constraint
Authority** among the engines below; only Master Decision holds
**Publication Authority**. Every other engine holds neither — its output
constrains eligibility or ranking but is never itself a binding override.

| Engine | State(s) | Kind | May do | May not do | Veto power |
|---|---|---|---|---|---|
| Market Engine | 05 | Neither | Classify regime, trend, breadth, volatility, macro, sector rotation | Select a trade | Restrict deployment / new-risk eligibility |
| Portfolio Engine | 06, 08 | Neither | Assess holdings, capacity, concentration, correlation, cash | Override a risk veto | Require portfolio action before new exposure |
| Risk Engine | 07, 15 | **Constraint** | Measure risk (§ Risk Model Definitions), size positions, issue binding vetoes | Override data failure or human authority, or publish a report | Hard veto — binding `NO TRADE` / `REDUCE` / `EXIT`, no override |
| Scanner Engine | 09 | Neither | Build ranked candidate universe with exclusions | Authorize execution | None (advisory ranking only) |
| Playbook Engine | 10 | Neither | Match exactly one repeatable playbook | Invent a setup absent evidence | Remove candidate from ranking on no-match |
| Options Engine | 11 | Neither | Compare Long Call vs. underlying, Greeks, IV, liquidity, named option risk factors | Recommend unverified/illiquid contracts, form an independent thesis | Remove option structure on failed check |
| Decision Engine | 12 | Neither | Rank candidates, compute Opportunity Score | Publish a final outcome, an execution/BUY-SELL decision, or a capital allocation instruction | Remove candidate on insufficient evidence |
| Committee Engine | 13 | Neither | Six independent role votes, compute Consensus Score (§10 formula), preserve dissent | Publish the final recommendation or veto anything itself | `WATCH` on no consensus; Risk Manager voting REDUCE/REJECT forces mandatory Risk Engine escalation (not an automatic veto) |
| Red Team Engine | 14 | **Constraint** | Seek counter-evidence, counter-thesis, alternatives | Suppress the original thesis or dissent | Critical-risk veto — binding downgrade or `NO TRADE` |
| Master Decision Engine | 16 | **Publication** | Integrate all valid outputs into one outcome; compute Confidence (never outcome) via §7 Weighted Composite Score | Override verification, Risk, or Red Team vetoes, or use Confidence to relax a gate | None — it enforces upstream vetoes, it does not hold one |
| Portfolio Optimization Engine | 17 | Neither | Convert an already-published Master Decision outcome into target weight, share count, capital released/required, and reallocation | Re-decide Hold/Reduce/Exit/Execute, or override a Master Decision outcome | None — its `Decision` field is a verbatim echo, never a competing value |
| Execution Engine | 18 | Neither | Produce human-reviewable entry/stop/target/size plan with order-type, liquidity, and slippage rules | Place, transmit, or imply placement of an order | `DO NOT EXECUTE — REVERIFY` on stale critical input |

---

## 3A. Trigger Documents (No State Machine Position)

Per `STATE_MACHINE.md` §2B, these two documents never occupy a numbered
state — a fired trigger starts a **new** analysis run at State 01/02 rather
than acting within the run in progress:

| Document | State(s) | Kind | May do | May not do |
|---|---|---|---|---|
| `POSITION_MANAGEMENT_ENGINE.md` | None — trigger only | Neither | Determine when a gain/loss/earnings/IV trigger requires starting a new run | Publish Hold/Reduce/Exit itself, or skip Verification/Portfolio Review in the run it starts |
| `PORTFOLIO_REBALANCING_ENGINE.md` | None — trigger only | Neither | Determine when periodic sector/theme drift requires starting a new run | Bypass Scanner/Committee/Risk for any new capital deployment |

---

## 4. Playbook Layer

| Component | Primary responsibility | Authority | Cannot do |
|---|---|---|---|
| `playbooks/PLAYBOOK_LIBRARY.md` | Repeatable setup rules (context, trigger, required data, invalidation, risk, no-trade conditions) and each playbook's lifecycle status (Active/Under Review/Suspended/Deprecated, §5) | Defines the only setups Playbook Engine may assign; Suspended/Deprecated entries are unavailable for new assignment | Claim backtested or guaranteed performance |

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
Options, Decision, Committee, Master Decision, Portfolio Optimization) still
constrains eligibility or ranking — it simply cannot issue a binding
override the way Risk and Red Team can. See [Dependency Map](DEPENDENCY_MAP.md) for how these responsibilities
translate into document-level dependencies, and [Architecture](ARCHITECTURE.md)
for the full data-flow diagram.

If the engines above cannot converge within the State Machine's revision
budget, the run terminates in `SYSTEM REVIEW REQUIRED` rather than looping —
see `STATE_MACHINE.md` §6A and `FAILURE_TAXONOMY.md` category
`CONFLICT_FAILURE`. This is a bounded-disagreement outcome, not a veto by any
single engine.

---

End of Document

TRX Trading OS v1.0
