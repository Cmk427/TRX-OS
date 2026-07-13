# TRX Trading OS
## schemas/

```text
Document ID      : TRX-SCH-000
Document Name    : Machine Schema Layer (Index)
Owner            : TRX project owner
Last Updated     : 2026-07-13
Version          : 1.1.0
Status           : Active
Classification   : Reference
Dependencies     : system/ENGINE_INTERFACE_CONTRACT.md
Applies To       : Any AI agent framework validating engine input/output
```

---

## Purpose

`system/ENGINE_INTERFACE_CONTRACT.md` defines every engine's input/output
shape in prose plus an illustrative JSON example. That is sufficient for a
human reader, but different AI models reading the same paragraph can
reasonably disagree on edge cases — is `confidence` a string or a number,
is a field required, what happens on a validation failure. This folder is
the same content rendered as **actual [JSON Schema](https://json-schema.org/)
documents** (Draft 2020-12, written in YAML for readability), so any
standard JSON Schema validator — regardless of which AI model or framework
is orchestrating the pipeline — enforces the identical shape.

Like `system/PARAMETER_REGISTRY.md` and `docs/AI_AGENT_IMPLEMENTATION_GUIDE.md`,
this folder is a **derived, non-authoritative rendering**, not a new rule
source:

- Every field, type, enum, and required/optional flag here is transcribed
  from `system/ENGINE_INTERFACE_CONTRACT.md` §2–§12 — nothing here was
  invented independently of that document.
- If a `.schema.yaml` file here ever disagrees with
  `ENGINE_INTERFACE_CONTRACT.md`, **the Markdown document wins**, and the
  mismatch is a bug in this file, not a second valid schema to choose
  between.
- A change to an engine's output shape SHALL be made in
  `ENGINE_INTERFACE_CONTRACT.md` first; updating the matching
  `.schema.yaml` file is a mechanical sync in the same change, per
  `CONSTITUTION.md` §7 change control — never the reverse.

## Files

| File | Renders |
|---|---|
| `market.schema.yaml` | `ENGINE_INTERFACE_CONTRACT.md` §2 |
| `portfolio.schema.yaml` | §3 |
| `risk.schema.yaml` | §4 |
| `scanner.schema.yaml` | §5 |
| `playbook.schema.yaml` | §6 |
| `options.schema.yaml` | §7 |
| `decision.schema.yaml` | §8 |
| `committee.schema.yaml` | §9 |
| `red_team.schema.yaml` | §10 |
| `master_decision.schema.yaml` | §11 |
| `portfolio_optimization.schema.yaml` | §12 |
| `execution.schema.yaml` | §13 |

## Shared Conventions

Every schema file in this folder:

- validates against exactly one of two shapes via `oneOf`: the engine's
  normal success payload, or the shared failure shape (`category` one of
  `FAILURE_TAXONOMY.md`'s six values, plus a `reason` string) — matching
  `ENGINE_INTERFACE_CONTRACT.md` §1A's `failure_state` rule;
- sets `additionalProperties: false` on every object — an unrecognised
  field is a schema violation, not something a lenient reader should
  ignore, so an agent cannot silently invent a new field and have it pass
  validation;
- fixes `engine` to a `const` matching the engine's name, so a payload can
  never be validated against the wrong engine's schema by accident; and
- is self-contained (no cross-file `$ref`) so any single file can be
  loaded and validated independently, without a multi-file resolver.

---

End of Document

TRX Trading OS v1.0
