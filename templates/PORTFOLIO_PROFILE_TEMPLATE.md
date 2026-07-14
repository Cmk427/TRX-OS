# TRX Trading OS
## PORTFOLIO_PROFILE_TEMPLATE.md

```text
Document ID      : TRX-TPL-004
Document Name    : Portfolio Profile Template
Owner            : TRX project owner
Last Updated     : 2026-07-14
Version          : 1.1.0
Status           : Active
Classification   : Template
Dependencies     : INVESTMENT_POLICY.md
                   RISK_ENGINE.md
Applies To       : Optional human-supplied context, read by engines
```

---

## 1. Purpose

A one-time, human-completed worksheet capturing the trader's own style,
risk tolerance, preferred holding period, and things to avoid. Engines MAY
read it as optional context; it is **not** a new authority — it never
overrides `INVESTMENT_POLICY.md`'s limits, `RISK_ENGINE.md`'s Account Risk
Profile, or any binding gate. It is a preference input, the same tier as
the Watchlist or Trading Horizon fields `PORTFOLIO_ENGINE.md` §3 already
accepts, not a policy document.

This is a fill-in worksheet, not an automatically-accumulating runtime
state file the way a future Decision History implementation would be
(`DECISION_SNAPSHOT_POLICY.md` §5, `docs/ROADMAP.md` v2.1) — nothing here
is written by an engine. **Do not edit this template directly.** Your
actual, populated copy lives at the fixed path `memory/PORTFOLIO_PROFILE.md`
— engines reference that path, not this template. Complete it once; update
it only when your actual preferences change, not per analysis run.

---

## 2. Profile

```text
Style (e.g. Growth / Swing / Long Call / Value / Income) :
Risk Tolerance (informational only — the binding profile is
  RISK_ENGINE.md §4 Account Risk Profile: Conservative / Balanced /
  Growth / Aggressive / Maximum Aggressive) :
Preferred Holding Period (e.g. 3–9 months) :
Preferred Sector (e.g. AI, Healthcare — informational weighting only,
  never a bypass of Scanner/Playbook criteria) :
Avoid List (e.g. High Debt, specific sectors, specific instruments) :
Other notes (free text) :
```

---

## 3. How Engines Use This

- **Scanner / Playbook Engines** MAY use Style, Preferred Sector, and Avoid
  List to weight candidate relevance — never to bypass a Scanner/Playbook
  criterion or invent a setup absent evidence.
- **Portfolio Engine** MAY use Preferred Holding Period as context for its
  own Trading Horizon input (§3) — it does not change any concentration or
  cash limit, which remain `INVESTMENT_POLICY.md`'s alone.
- **Risk Engine** treats Risk Tolerance here as informational only; the
  binding Account Risk Profile (§4 of that document) is set independently
  and is never silently inferred from this worksheet.

If this file is missing or incomplete, every engine SHALL proceed using its
own defaults — an incomplete Portfolio Profile is never a Failure Condition.

---

End of Document

TRX Trading OS v1.0
