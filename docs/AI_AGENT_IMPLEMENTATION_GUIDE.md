# TRX Trading OS
## AI_AGENT_IMPLEMENTATION_GUIDE.md

```text
Document ID      : TRX-IMPL-001
Document Name    : AI Agent Implementation Guide
Owner            : TRX project owner
Last Updated     : 2026-07-13
Version          : 1.1.0
Status           : Active
Classification   : Reference
Dependencies     : STATE_MACHINE.md
                   CONSTITUTION.md
                   CORE_PRINCIPLES.md
                   PLAYBOOK_LIBRARY.md
                   DATA_SOURCE_POLICY.md
                   VERIFICATION_POLICY.md
                   ENGINE_INTERFACE_CONTRACT.md
                   FAILURE_TAXONOMY.md
                   DECISION_SNAPSHOT_POLICY.md
Applies To       : Any AI agent framework implementing this system
```

---

## 1. Purpose

Every document under `system/` and `engines/` was written for a human
reader first. This guide is the opposite direction: a consolidated
checklist for whatever AI agent framework (Claude Code, Codex, GPT, Gemini,
or any future one) is asked to actually run this document set as a
pipeline.

Like `system/PARAMETER_REGISTRY.md`, `docs/RESPONSIBILITY_MATRIX.md`, and
`docs/DEPENDENCY_MAP.md`, this document is a **derived index, not a new
source of authority**. It restates constraints that already exist in the
documents listed above. If this guide and an owning document ever disagree,
**the owning document wins**, and the mismatch is a bug in this guide.
This guide SHALL NOT be read as granting any capability, authority, or
autonomy beyond what `CONSTITUTION.md` already defines.

---

## 2. Every Engine Is Stateless

An engine call is a pure function of (a) the inputs its own document
declares as required, and (b) the rules in its own document — nothing else.
It retains no memory of a prior run, prior ticker, or prior session unless
that memory is an explicit, recorded input (e.g. a Decision Snapshot
retrieved by ticker, once `docs/ROADMAP.md` v2.0's Research Memory layer
exists). An implementation SHALL NOT let one engine's context window,
conversation history, or prior tool calls silently influence a later,
supposedly independent engine's output — this is exactly what
`COMMITTEE_ENGINE.md` §3's "members SHALL NOT inherit conclusions from
previous members" already requires at the Committee layer, generalised to
every engine.

---

## 3. Never Skip the State Machine

`STATE_MACHINE.md` is the sole source of workflow order (`docs/ARCHITECTURE.md`
"Workflow and Data Flow"). An implementation SHALL:

- call engines in the declared state order, never reordered;
- never run a later state's engine using a guessed or "probably fine"
  placeholder for an earlier state's output that hasn't completed yet —
  this is the exact class of bug `STATE_MACHINE.md` and `OPTIONS_ENGINE.md`
  §4 fixed when Options Review was found gating on a score not yet
  computed;
- never parallelise two states that have a declared dependency
  (`docs/DEPENDENCY_MAP.md` §1's no-forward-dependency rule exists
  precisely so this is checkable); and
- respect the §6A revision-cycle limit — a state may be revisited on
  legitimate new information, but not looped indefinitely; the third
  full-cycle revision triggers `SYSTEM REVIEW REQUIRED`, not another retry.

---

## 4. Never Override the Constitution

`CONSTITUTION.md` §4A's Constraint Authority is binding on every engine
and on the orchestration layer itself. Concretely:

- a Risk Engine hard veto (`RISK_ENGINE.md` §28) or a Red Team critical-risk
  finding (`RED_TEAM_ENGINE.md` §18) SHALL NOT be reinterpreted, retried
  with different inputs to get a different answer, or bypassed by an
  orchestration script that calls the Master Decision Engine directly;
- a critical-data failure (`INSUFFICIENT VERIFIED INFORMATION`) is a
  terminal state for that run, not a prompt to fabricate the missing fact
  and continue (`CORE_PRINCIPLES.md`);
- the Master Decision Engine's Publication Authority (§21) never becomes
  Constraint Authority no matter how the orchestration code is structured
  — an agent framework SHALL NOT add a "force publish" or "override gate"
  parameter; none is defined anywhere in this system and adding one is an
  unapproved constitutional change (`CONSTITUTION.md` §7); and
- the human retains sole order-execution authority (`CONSTITUTION.md` Rule
  1) regardless of which agent framework runs the analysis — no engine or
  orchestration layer may place, modify, or cancel an order.

---

## 5. Never Invent a Playbook

`PLAYBOOK_ENGINE.md` matches a candidate against `playbooks/PLAYBOOK_LIBRARY.md`
exactly as currently versioned — it does not create or modify a playbook at
runtime. An implementation SHALL NOT let an agent synthesize a new setup
description, blend two playbooks into an ad hoc hybrid, or relax a
playbook's stated conditions to make a candidate fit. Adding, retiring, or
changing a playbook is an out-of-band content change subject to
`PLAYBOOK_LIBRARY.md` §4/§5's own governance and lifecycle rules, never a
runtime decision made mid-analysis.

---

## 6. Never Generate Missing Data

If a required input is unavailable, the correct behavior across every
engine is the same: surface it as the literal value `UNKNOWN`
(`system/ENGINE_INTERFACE_CONTRACT.md` §1) and apply whatever
missing-value or failure rule the owning document specifies —
`RISK_ENGINE.md` §26A, `DATA_SOURCE_POLICY.md`, and
`VERIFICATION_POLICY.md` each define this for their domain. An
implementation SHALL NOT have an agent infer a plausible number, reuse a
stale cached value past its freshness window, average a historical range,
or otherwise produce a number "so the pipeline can keep moving." An
unmeasurable input is a `Reject` or an `UNKNOWN`-flagged output, never a
silently estimated one.

---

## 7. How to Use the Engine Interface Contract

`system/ENGINE_INTERFACE_CONTRACT.md` §2–§12 define the JSON shape each
engine hands downstream; §1A defines the conventions that make those
shapes actually checkable:

- every payload SHALL carry both `engine_version` (which rule-set produced
  it) and `schema_version` (which shape version it conforms to);
- a field is required unless its type ends in `| null` or is marked
  `(optional)` — a required field missing from a real payload is a
  validation failure, not something to infer;
- an enumerated field (`"A | B | C"`) SHALL be exactly one listed literal —
  never a close synonym, a different casing, or an invented additional
  category; and
- every numeric score is 0–100 on one shared scale unless the field's own
  definition states otherwise — never a 0–1 fraction, never a percentage
  string, per §1A's Scale rule.

---

## 8. How to Return Failure

When an engine cannot produce its normal output — a required upstream
value is `UNKNOWN`, contradictory, or a veto applies — it SHALL return the
`failure_state` shape defined in `ENGINE_INTERFACE_CONTRACT.md` §1A:
`category` (one of `FAILURE_TAXONOMY.md` §2's six categories) plus a
`reason` string, in place of its normal payload, not alongside placeholder
values for the normal fields. A consumer SHALL check for `failure_state`
before reading any engine-specific field — its presence means the rest of
that call's normal payload does not exist, not that it exists with
defaults.

---

## 9. Determinism and Auditability

Every completed run SHOULD be traceable to one Decision Snapshot
(`DECISION_SNAPSHOT_POLICY.md` §2), recording exactly which document
version of each engine produced the result — this is why `docs/ARCHITECTURE.md`'s
Release Version Matrix and every engine's own `Version` header must stay in
sync (`docs/ARCHITECTURE.md` "Keeping this table honest"). An
implementation SHALL NOT run a mix of document versions in one pipeline
pass without recording exactly which version of each was used.

---

## 10. Non-Goals

This guide grants no new capability. It does not authorise autonomous
execution, broker connectivity, or any bypass of the rules it summarises.
Any of those would require the separately-scoped constitutional review
`docs/ARCHITECTURE.md`'s Extension Rules already require, not an
implementation detail decided while wiring up an agent framework.

---

## 11. Common Failure Patterns — Bad vs. Good Agent Behaviour

Each row names one way an implementation could plausibly go wrong despite
every document above being followed "in spirit," and the correct behaviour
this guide requires instead.

| # | Bad agent behaviour | Why it violates this system | Good agent behaviour |
|---|---|---|---|
| 1 | Invent a plausible catalyst, price, or option quote when a real one is unavailable | Violates §6 and `CORE_PRINCIPLES.md` — a guessed fact is indistinguishable from a real one downstream, defeating the entire evidence model | Return `UNKNOWN` for that field and let the owning engine's own failure rule apply (e.g. `DATA_FAILURE` → `INSUFFICIENT VERIFIED INFORMATION`) |
| 2 | Skip the Preliminary Risk Gate because the candidate "looks obviously fine" | Violates §3 — no state may be skipped regardless of how confident the agent is | Run State 07 exactly as `STATE_MACHINE.md` requires, every time, with no confidence-based shortcut |
| 3 | Let the Master Decision Engine publish `EXECUTE` after a Risk Engine hard veto because the Confidence Model score is high | Violates §4 — a high WCS never overrides a binding veto, per `MASTER_DECISION_ENGINE.md` §7/§13 | Publish the vetoed outcome (`NO TRADE` / `REDUCE` / `EXIT`) regardless of WCS, and report the WCS only as supporting context |
| 4 | Blend two playbooks, or loosen PB-001's trigger slightly because the candidate "almost" qualifies | Violates §5 and `PLAYBOOK_ENGINE.md`'s `NO INVENTION — MATCH ONLY` rule | Report `NO TRADE`/exclusion with the specific unmet criterion named, per `PLAYBOOK_LIBRARY.md` §2 field 12 |
| 5 | Retry a failed state with slightly different framing until it produces a passing result | Violates §3's revision-cycle limit (`STATE_MACHINE.md` §6A) | Let the 3rd full-cycle revision terminate the run as `SYSTEM REVIEW REQUIRED`, and report which states were in conflict |
| 6 | Add a "confidence override" or "force publish" flag to the orchestration code for edge cases | Violates §4 — no such parameter exists in this system and adding one is an unapproved constitutional change | Return `NO TRADE` or the applicable Failure Taxonomy category; escalate the edge case as a documented gap, not a code-level bypass |
| 7 | Carry state or "learnings" from one ticker's analysis into another ticker's engine calls within the same session | Violates §2 — every engine call is stateless per its own declared inputs | Re-supply every required input explicitly for each candidate; treat each engine call as independent |

## 12. New Agent Checklist

Before any new AI agent framework is considered ready to run this system,
it SHALL be able to answer "yes" to every item below:

- [ ] Follows `STATE_MACHINE.md`'s state order exactly, with no skipped,
      reordered, or merged states.
- [ ] Treats `CONSTITUTION.md` §4A's Constraint Authority (Risk Engine
      veto, Red Team critical-risk veto, Core Principles) as binding and
      non-bypassable by any orchestration-layer code.
- [ ] Validates every engine call against its schema in `schemas/` (or,
      at minimum, `system/ENGINE_INTERFACE_CONTRACT.md` §2–§12) before
      passing it downstream.
- [ ] Produces a final report that satisfies `system/OUTPUT_CONTRACT.md`
      in full, including a `Not Applicable` section where a state was
      conditionally skipped.
- [ ] Never invents data, a playbook, or a score for a field it cannot
      verify — it returns `UNKNOWN` or the applicable `failure_state`
      instead.
- [ ] Never lets the human-execution boundary blur — the framework itself
      never places, modifies, or cancels an order, regardless of what the
      Execution Engine's plan says.

---

End of Document

TRX Trading OS v1.0
