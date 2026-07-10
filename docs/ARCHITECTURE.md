# TRX Trading OS v1.0 Architecture

## Purpose

TRX Trading OS is a document-driven AI Trading Research Operating System. It
does not place orders or provide automated trading. This architecture makes
research outputs verifiable, auditable, risk-constrained, and extensible while
keeping human authority over all capital decisions.

## Repository Structure

```text
README.md                         Product overview and entry point
system/                           Governance, principles, policies, workflow, output
  SYSTEM.md                       System scope and contract
  CONSTITUTION.md                 Responsibility and veto hierarchy
  CORE_PRINCIPLES.md              Immutable trading-safety principles
  OUTPUT_CONTRACT.md              Final report requirements
  STATE_MACHINE.md                Sole workflow source of truth
  VERIFICATION_POLICY.md          V1–V5 evidence classification
  DATA_SOURCE_POLICY.md           Source quality, freshness, critical-data rules
engines/                          Specialist research and decision components
playbooks/                        Repeatable, rule-based setup definitions
docs/                             Architecture, roadmap, and cross-reference maps
  ARCHITECTURE.md                 This document
  ROADMAP.md                      Version plan and promotion criteria
  RESPONSIBILITY_MATRIX.md        Quick-reference authority/veto index (derived from CONSTITUTION.md)
  DEPENDENCY_MAP.md                Document dependency graph and forward-dependency checks
templates/                        Fill-in worksheets aligned to State Machine stages
  ANALYSIS_TEMPLATE.md            States 02–11 (input through options review)
  DECISION_TEMPLATE.md            States 12–18 (ranking through self audit)
  REPORT_TEMPLATE.md              State 19 — direct fill-in version of OUTPUT_CONTRACT.md
workflows/                        Reserved for future tool-level orchestration definitions
examples/                         Fictional worked examples of a completed report
```

## Workflow and Data Flow

`STATE_MACHINE.md` is the only document that defines the processing order.

```text
Validate Input → Verify Data → Market & Macro → Portfolio
  → Preliminary Risk → Positions → Scanner → Playbook → Options (conditional)
  → Ranking → Committee → Red Team → Final Risk → Master Decision
  → Execution Plan (conditional) → Self Audit → Final Report
```

The workflow deliberately evaluates existing holdings and risk before searching
for new opportunities. `NO TRADE` is a completed outcome. A critical data
failure results in `INSUFFICIENT VERIFIED INFORMATION`; it never receives an
invented substitute.

## Authority Model

| Layer | Authority |
|---|---|
| Human trader | Sole capital and order-execution authority |
| Core Principles / data controls | Non-negotiable safety and evidence constraints |
| Risk Engine | Binding preliminary and final risk veto |
| Red Team Engine | Binding veto for a documented critical risk |
| State Machine | Valid order of work and recovery transitions |
| Master Decision Engine | Only publisher of the final integrated outcome |
| Committee Engine | Independent multi-role assessment; preserves dissent |
| Specialist engines | Evidence, analysis, classification, and proposed plans |

The Master Decision Engine integrates outputs but cannot override an upstream
critical-data failure, Risk Engine hard veto, or Red Team critical-risk veto.

## Engine Boundaries

| Engine | Owns | Does not own |
|---|---|---|
| Market | Regime, breadth, volatility, macro and sector context | Trade selection |
| Portfolio | Current holdings, capacity, concentration, correlation | Risk override |
| Scanner | Candidate universe, eligibility, preliminary ranking | Final opportunity decision |
| Playbook | One repeatable setup classification | New trade authorisation |
| Options | Long Call versus underlying analysis | Independent underlying thesis |
| Risk | Position / portfolio risk and vetoes | Data verification or order placement |
| Decision | Candidate Opportunity Score and decision package | Final output |
| Committee | Independent expert-style votes and dissent | Final output / risk override |
| Red Team | Counter-evidence and critical-risk challenge | Suppressing evidence or dissent |
| Master Decision | Final integration and report publication | Veto override |
| Execution | Human review plan and re-verification checklist | Broker access or order placement |

## Evidence and Auditability

Every material factual claim is classified V1–V5 and governed by source tier,
timestamp, freshness, and conflict rules. Each completed analysis retains an
evidence ledger, engine outcomes, gate results, assumptions, dissent, outcome,
invalidation, review triggers, and document versions. Historical analyses are
append-only; future learning may alter future rules but not past records.

## Consistency Checklist

Any material document change must confirm:

- all referenced files exist and reference the correct responsibility;
- State Machine ordering is not redefined elsewhere;
- the authority hierarchy and vetoes remain intact;
- output fields have an evidence and freshness policy;
- no duplicate engine becomes a competing final-decision authority;
- no new dependency creates a cycle or points to a later State Machine state
  — see `docs/DEPENDENCY_MAP.md` §1 and its checklist; and
- README architecture matches the repository structure.

## Extension Rules

New instruments, option strategies, data integrations, scoring models, or
automation proposals require a documented scope, source policy, risk controls,
failure modes, output requirements, and constitutional review. They must not
imply autonomous execution without a separately approved, explicitly scoped
system change.

---

TRX Trading OS v1.0
