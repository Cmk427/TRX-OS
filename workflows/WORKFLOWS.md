# TRX Trading OS
## workflows/

```text
Document ID      : TRX-WF-000
Document Name    : Workflow Definitions (Placeholder)
Version          : 1.0.0
Status           : Reserved — no workflow defined yet
Classification   : Reference
Dependencies     : STATE_MACHINE.md
Applies To       : Future automated orchestration of the analysis pipeline
```

---

## 1. Purpose

`system/STATE_MACHINE.md` remains the sole source of truth for the analysis
order. This folder is reserved for concrete, tool-level workflow definitions
that *execute* that order — for example, an orchestration script or agent
pipeline that runs States 02–19 against a real data feed and produces a
`templates/REPORT_TEMPLATE.md`-shaped report automatically.

No workflow file exists yet. This placeholder exists only so the repository
layout matches `docs/ARCHITECTURE.md` and `README.md`.

---

## 2. Rules for Any Future Workflow File Added Here

A workflow definition added to this folder SHALL:

- implement the state order in `STATE_MACHINE.md` exactly — it may not skip,
  reorder, or merge states;
- treat every gate and veto in `CONSTITUTION.md` §5 as binding and
  non-bypassable by automation;
- stop at State 04 with `INSUFFICIENT VERIFIED INFORMATION` under the same
  conditions a human-run analysis would, per `VERIFICATION_POLICY.md`; and
- terminate at `EXECUTION_ENGINE.md`'s output — it SHALL NOT place, transmit,
  or simulate placing an order. Automated execution is out of scope for the
  entire v1.x line per `docs/ROADMAP.md`.

A workflow file that would grant broker access, autonomous order submission,
or any bypass of Risk / Red Team vetoes requires a Constitution amendment
first, not just a file added here.

---

End of Document

TRX Trading OS v1.0
