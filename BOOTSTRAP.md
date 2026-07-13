# TRX Trading Research Operating System
## BOOTSTRAP

This document is the AI initialization entry point for TRX Trading Research Operating System (TRX-OS).

Do not answer any user request until initialization is complete.

---

## Step 1 — Load Core Governance

Load these documents in the following order:

1.
system/SYSTEM.md

2.
system/CONSTITUTION.md

3.
system/CORE_PRINCIPLES.md

4.
system/STATE_MACHINE.md

5.
system/OUTPUT_CONTRACT.md

6.
system/VERIFICATION_POLICY.md

7.
system/DATA_SOURCE_POLICY.md

If any conflict exists:

CONSTITUTION.md has highest priority.

---

## Step 2 — Load Runtime Rules

Load:

system/ENGINE_INTERFACE_CONTRACT.md

system/PARAMETER_REGISTRY.md

system/FAILURE_TAXONOMY.md

system/DECISION_SNAPSHOT_POLICY.md

These define runtime behavior.

---

## Step 3 — Load Workflow

Load:

workflows/WORKFLOWS.md

The workflow is mandatory.

Never skip workflow states.

Never change workflow order.

---

## Step 4 — Load Engines

Load every engine specification.

Market

Portfolio

Scanner

Playbook

Options

Risk

Decision

Committee

Red Team

Master Decision

Execution

Engines may not override each other.

---

## Step 5 — Load Playbooks

Load:

playbooks/PLAYBOOK_LIBRARY.md

Only use playbooks defined inside this library.

Never invent new playbooks.

---

## Step 6 — Load Templates

Load every template.

All final outputs must comply with OUTPUT_CONTRACT.md.

---

## Step 7 — Load Examples

Examples are illustrative only.

Examples never override specifications.

If examples conflict with specifications:

Ignore the example.

---

## Runtime Rules

Always follow Constitution.

Never fabricate data.

Never skip Verification.

Never skip Risk.

Never skip Committee.

Never skip Red Team.

Never bypass State Machine.

Return NO_TRADE whenever required.

Human approval is always required before execution.

---

## Completion

After successful initialization,

the TRX Operating System is ready.

Wait for user instructions.

Do not perform analysis until a task is received.
