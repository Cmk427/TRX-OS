# Atlas Trading OS
## PLAYBOOK_ENGINE.md

```text
Document ID      : ATO-PB-001
Document Name    : Playbook Engine
Version          : 1.0.0
Status           : Stable
Classification   : Core
Dependencies     : MARKET_ENGINE
                   SCANNER_ENGINE
                   MASTER_DECISION_ENGINE
Applies To       : Opportunity Classification
```

-------------------------------------------------------------------------------
1. PURPOSE
-------------------------------------------------------------------------------

The Playbook Engine classifies every trading opportunity into a predefined,
repeatable trading strategy.

Atlas Trading OS never recommends a trade without assigning it to a
validated Playbook.

If no suitable Playbook exists,

Recommendation Status

NO TRADE

-------------------------------------------------------------------------------
2. PHILOSOPHY
-------------------------------------------------------------------------------

Trade the setup.

Not the ticker.

Every stock is temporary.

Every setup is repeatable.

The system learns Playbooks,

not symbols.

-------------------------------------------------------------------------------
3. PLAYBOOK CLASSIFICATION
-------------------------------------------------------------------------------

Every recommendation SHALL belong to exactly ONE Playbook.

Multiple Playbooks are prohibited.

-------------------------------------------------------------------------------
4. PLAYBOOK LIBRARY
-------------------------------------------------------------------------------

PB-001

Momentum Breakout

PB-002

Gap & Go

PB-003

Post Earnings Continuation

PB-004

Trend Pullback

PB-005

Relative Strength Leader

PB-006

Sector Rotation

PB-007

High Volume Reversal

PB-008

Short Squeeze

PB-009

AI Momentum

PB-010

Defense Rotation

PB-011

Large Cap Leadership

PB-012

ETF Swing

PB-013

Buy Call Momentum

PB-014

Institutional Accumulation

PB-015

Event Driven

-------------------------------------------------------------------------------
5. PLAYBOOK DETECTION
-------------------------------------------------------------------------------

Every candidate SHALL compare itself against

All available Playbooks.

The engine SHALL calculate

Similarity Score

0-100

Highest score wins.

-------------------------------------------------------------------------------
6. PLAYBOOK REQUIREMENTS
-------------------------------------------------------------------------------

Every Playbook defines

Market Regime

Trend

Volume

Catalyst

Risk

Holding Period

Exit Logic

Invalidation

-------------------------------------------------------------------------------
7. PLAYBOOK SCORE
-------------------------------------------------------------------------------

Each candidate receives

Playbook Match Score

100

Perfect Match

90

Excellent

80

Qualified

70

Weak

Below 70

Reject

-------------------------------------------------------------------------------
8. EXECUTION ELIGIBILITY
-------------------------------------------------------------------------------

Execute only if

Playbook Match

≥ 85

Otherwise

Watchlist

-------------------------------------------------------------------------------
9. REQUIRED OUTPUT
-------------------------------------------------------------------------------

Every recommendation SHALL include

Playbook Name

Playbook ID

Match Score

Reason for Match

Expected Holding Period

Expected Behaviour

Historical Characteristics

-------------------------------------------------------------------------------
10. SUCCESS CRITERIA
-------------------------------------------------------------------------------

A successful Playbook

Is repeatable

Has measurable rules

Can be backtested

Produces consistent decision making

-------------------------------------------------------------------------------
End of Document

Atlas Trading OS

PLAYBOOK_ENGINE.md

Version 1.0.0
```
