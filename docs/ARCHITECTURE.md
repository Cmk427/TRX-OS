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
  DECISION_SNAPSHOT_POLICY.md     What is locked/recorded per published decision
  ENGINE_INTERFACE_CONTRACT.md    Structured engine-to-engine schema (naming/shape only)
  FAILURE_TAXONOMY.md             Classification of every non-EXECUTE outcome
  PARAMETER_REGISTRY.md           Numeric-threshold index (derived view; owning engine wins on conflict)
  DOCUMENTATION_GOVERNANCE.md     Header standard (Owner/Last Updated) for new documents only
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

Per `CONSTITUTION.md` §4A, "authority" splits into two non-competing kinds:
**Constraint Authority** (the binding power to forbid an outcome) and
**Publication Authority** (the sole right to integrate and emit the one
final report). A component never holds both meaningfully — Risk Engine's
Constraint Authority does not make it a publisher, and Master Decision
Engine's Publication Authority does not make it a constraint.

| Layer | Kind | Authority |
|---|---|---|
| Human trader | — (outside the system) | Sole capital and order-execution authority |
| Core Principles / data controls | Constraint | Non-negotiable safety and evidence constraints |
| Risk Engine | Constraint | Binding preliminary and final risk veto |
| Red Team Engine | Constraint | Binding veto for a documented critical risk |
| State Machine | Constraint (procedural) | Valid order of work, recovery transitions, and the §6A revision-cycle limit |
| Master Decision Engine | Publication | Only publisher of the final integrated outcome; holds no veto |
| Committee Engine | Neither | Independent multi-role assessment; preserves dissent |
| Specialist engines | Neither | Evidence, analysis, classification, and proposed plans |

The Master Decision Engine integrates outputs but cannot override an upstream
critical-data failure, Risk Engine hard veto, or Red Team critical-risk veto.
`CONSTITUTION.md` §4B Conflict Resolution Protocol states this as one fixed
priority order (Risk veto > Red Team veto > Committee majority > Master
Decision integration) for the case where these disagree.

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
invalidation, review triggers, and document versions — collected into one
Decision Snapshot per `DECISION_SNAPSHOT_POLICY.md`. Historical analyses are
append-only; future learning may alter future rules but not past records. Any
non-`EXECUTE` outcome is additionally classified under
`FAILURE_TAXONOMY.md` for audit purposes.

## Release Version Matrix

"TRX Trading OS v1.0" (the product-level version stamped at the foot of
every document) is a composite of many independently-versioned documents,
not one number — a Decision Snapshot's "engine document versions used"
(`DECISION_SNAPSHOT_POLICY.md` §2) only makes sense if there is one place
that says what the current, coherent set of versions actually is. This
table is that place:

```text
system/     CORE_PRINCIPLES 1.0.0 · CONSTITUTION 1.1.0 · SYSTEM 1.1.0
            OUTPUT_CONTRACT 1.1.0 · STATE_MACHINE 1.2.0
            VERIFICATION_POLICY 1.2.0 · DATA_SOURCE_POLICY 1.1.0
            DECISION_SNAPSHOT_POLICY 1.1.0 · ENGINE_INTERFACE_CONTRACT 1.2.0
            FAILURE_TAXONOMY 1.1.0 · PARAMETER_REGISTRY 1.3.0
            DOCUMENTATION_GOVERNANCE 1.0.0
engines/    MARKET 1.3.0 · PORTFOLIO 1.2.0 · SCANNER 1.0.0 · PLAYBOOK 1.1.0
            OPTIONS 1.2.0 · RISK 1.2.0 · DECISION 1.2.0
            MASTER_DECISION 1.3.0 · COMMITTEE 1.3.0 · RED_TEAM 1.2.0
            EXECUTION 1.1.0
playbooks/  PLAYBOOK_LIBRARY 1.2.0
templates/  ANALYSIS 1.2.0 · DECISION 1.2.0 · REPORT 1.3.0
examples/   NO_TRADE 1.4.0 · EXECUTE_LONG_CALL 1.4.0
docs/       RESPONSIBILITY_MATRIX 1.1.0 · DEPENDENCY_MAP 1.2.0
workflows/  WORKFLOWS 1.0.0 (unchanged — still a placeholder)
```

A document at 1.0.0 above genuinely has not changed since the original
architecture pass (`SCANNER_ENGINE.md`, `CORE_PRINCIPLES.md`,
`DOCUMENTATION_GOVERNANCE.md`, `WORKFLOWS.md`) — that is a fact about their
history, not a sign they were skipped. Each whole revision level above 1.0
reflects one round of substantive fixes. `examples/` are at 1.4.0 (four
revision rounds) and `MASTER_DECISION_ENGINE.md`/`COMMITTEE_ENGINE.md`/
`MARKET_ENGINE.md` are at 1.3.0 partly because a fifth audit round found
that an earlier fix (removing an unscoped "WCS &lt; 70 caps Confidence at
Low" rule from `MASTER_DECISION_ENGINE.md` §7) had not fully propagated —
the same disavowed rule was still quoted in that document's own §17,
`PARAMETER_REGISTRY.md` §8, and `templates/DECISION_TEMPLATE.md` §6. This is
recorded here deliberately: propagation gaps are exactly the failure mode
this matrix and the Decision Snapshot policy exist to catch, and pretending
a fix round found nothing would defeat that purpose.

**Keeping this table honest**: any change to a document's `Version` header
SHALL update its entry here in the same edit — this table is exactly as
prone to drifting out of sync as `PARAMETER_REGISTRY.md` is, and is subject
to the same rule: if this matrix and a document's own header ever disagree,
**the document's own header wins**, and the mismatch is a bug in this table.

## Consistency Checklist

Any material document change must confirm:

- all referenced files exist and reference the correct responsibility;
- State Machine ordering is not redefined elsewhere;
- the authority hierarchy and vetoes remain intact;
- output fields have an evidence and freshness policy;
- no duplicate engine becomes a competing final-decision authority;
- no new dependency creates a cycle or points to a later State Machine state
  — see `docs/DEPENDENCY_MAP.md` §1 and its checklist;
- any use of the word "authority" names Constraint or Publication Authority
  per `CONSTITUTION.md` §4A — never an unqualified "final authority";
- a change to `STATE_MACHINE.md` §6A's revision-cycle limit, or to any
  terminal status, stays consistent across `OUTPUT_CONTRACT.md`,
  `FAILURE_TAXONOMY.md`, and `docs/RESPONSIBILITY_MATRIX.md`;
- the changed document's `Version` header is bumped and the Release
  Version Matrix above is updated in the same edit; and
- README architecture matches the repository structure.

## Extension Rules

New instruments, option strategies, data integrations, scoring models, or
automation proposals require a documented scope, source policy, risk controls,
failure modes, output requirements, and constitutional review. They must not
imply autonomous execution without a separately approved, explicitly scoped
system change.

---

TRX Trading OS v1.0
