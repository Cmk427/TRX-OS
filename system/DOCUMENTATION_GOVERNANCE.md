# TRX Trading OS
## DOCUMENTATION_GOVERNANCE.md

```text
Document ID      : TRX-DOC-001
Document Name    : Documentation Governance
Version          : 1.0.0
Status           : Active
Classification   : Reference
Dependencies     : CONSTITUTION.md
Applies To       : Documents created from this point forward
```

---

## 1. Purpose

More documents mean more drift. This is the standard for keeping new
documents traceable as the system grows, without retroactively touching the
~27 files that predate it — a mechanical retrofit of every existing header
was considered and explicitly rejected as low-value churn for a
single-owner project (see `CONSTITUTION.md` §7A change checklist, which
already covers what a *change* to an existing file requires).

---

## 2. Standard Header Fields (new documents only)

Every document created after this policy's introduction SHALL use the
existing header block pattern (Document ID, Document Name, Version, Status,
Classification, Dependencies, Applies To) plus two additional fields:

```text
Owner            : <who is responsible for this document's accuracy>
Last Updated     : <date of the most recent substantive edit>
```

For a single-owner project, `Owner` is simply the project owner's name or
role — this is not a claim that multiple people currently maintain TRX, only
a field that becomes immediately useful the moment a second contributor
joins, without requiring every prior document to be touched at that time.

---

## 3. Retrofit Policy

Existing documents (everything created before this policy) are **not**
retrofitted with `Owner` / `Last Updated` fields as a standalone task. The
next time an existing document receives a substantive edit for an unrelated
reason, adding these two fields to that document's header at the same time
is encouraged but not required. This keeps diffs focused on the actual
change being made, rather than mixing a real edit with mechanical
header churn across unrelated files.

---

## 4. Relationship to Change Control

This document governs header *format*; `CONSTITUTION.md` §7/§7A governs the
*process* for changing a Critical-classification document's content. They
are complementary, not overlapping — a document can satisfy this header
standard and still fail the change checklist if its content change lacks a
documented rationale.

---

End of Document

TRX Trading OS v1.0
