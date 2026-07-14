# TRX Trading OS
## memory/PORTFOLIO_PROFILE.md

```text
This is your copy of templates/PORTFOLIO_PROFILE_TEMPLATE.md, filled in.
Engines read THIS file at this fixed path — not the template, and not a
copy kept anywhere else. Edit the values below directly; there is no
separate "draft" step the way REPORT_TEMPLATE.md has one, because this
file is not a per-run report — it changes only when your actual
preferences change.

Classification   : Reference (non-authoritative — see §3 below)
Dependencies     : templates/PORTFOLIO_PROFILE_TEMPLATE.md
                   system/INVESTMENT_POLICY.md
                   engines/RISK_ENGINE.md
```

---

## 1. Purpose

Same purpose as `templates/PORTFOLIO_PROFILE_TEMPLATE.md` §1: an optional,
non-authoritative style/risk/holding-period worksheet. The difference is
purely mechanical — this is the actual, populated instance at a fixed
repository path (`memory/PORTFOLIO_PROFILE.md`) so an engine can reference
one predictable location, instead of an unfilled template a human would
otherwise have to copy somewhere ad hoc.

This is still not a runtime state file the way a future Decision History
implementation would be (`DECISION_SNAPSHOT_POLICY.md` §5) — nothing
accumulates here automatically, and no engine writes to it. A human edits
it directly, the same way a config file is hand-edited, and it never
overrides `INVESTMENT_POLICY.md`'s limits, `RISK_ENGINE.md`'s Account Risk
Profile, or any binding gate (§3).

---

## 2. Profile

```text
Style (e.g. Growth / Swing / Long Call / Value / Income) :
  [ fill in ]

Risk Tolerance (informational only — the binding profile is
  RISK_ENGINE.md §4 Account Risk Profile: Conservative / Balanced /
  Growth / Aggressive / Maximum Aggressive) :
  [ fill in ]

Preferred Holding Period (e.g. 3–9 months) :
  [ fill in ]

Preferred Sector (e.g. AI, Healthcare — informational weighting only,
  never a bypass of Scanner/Playbook criteria) :
  [ fill in ]

Avoid List (e.g. High Debt, specific sectors, specific instruments) :
  [ fill in ]

Other notes (free text) :
  [ fill in ]
```

---

## 3. How Engines Use This

Identical rules to `templates/PORTFOLIO_PROFILE_TEMPLATE.md` §3 — repeated
here since this is the file engines actually read:

- **Scanner / Playbook Engines** MAY use Style, Preferred Sector, and Avoid
  List to weight candidate relevance — never to bypass a Scanner/Playbook
  criterion or invent a setup absent evidence.
- **Portfolio Engine** MAY use Preferred Holding Period as context for its
  own Trading Horizon input (§3) — it does not change any concentration or
  cash limit, which remain `INVESTMENT_POLICY.md`'s alone.
- **Risk Engine** treats Risk Tolerance here as informational only; the
  binding Account Risk Profile (§4 of that document) is set independently
  and is never silently inferred from this file.

If this file is missing, empty, or incomplete, every engine SHALL proceed
using its own defaults — an incomplete Portfolio Profile is never a
Failure Condition.

---

End of Document

TRX Trading OS v1.0
