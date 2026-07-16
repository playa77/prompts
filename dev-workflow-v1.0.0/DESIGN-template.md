# DESIGN — <project name>

**Version: 0.1.0 | <date>**
**Status: DRAFT — not approved**
**Hard limit: 2 pages. If this document exceeds 2 pages, it contains material that belongs in the roadmap or the ledger.**

## Intent

<Three sentences. What this is, for whom, and what changes when it exists.>

## Non-goals

<What this deliberately does not do. 3–6 bullets, one line each.>

## Invariants

Statements that must never become false, at any version. Each is tagged with its enforcement mechanism. These are the compressed form of the architect's judgment — the machine gates enforce them forever.

- **INV-1**: <statement> `[TESTABLE → <test name>]`
- **INV-2**: <statement> `[TESTABLE → <test name>]`
- **INV-3**: <statement> `[UNTESTABLE → review trigger: any change to <path>]`

Rules: every `[TESTABLE]` invariant must have its guard test installed by a roadmap work package. Every `[UNTESTABLE]` invariant names the exact paths whose modification flags a change for human review.

## Interfaces

External surface only — the contracts others (users, files on disk, other systems) depend on. Signatures, not implementations.

<IPC commands / HTTP endpoints / persisted file formats / CLI surface>

## Risks

<Top 3–5, one line each: risk → consequence.>

---

## Approval record

| Version | Date | Approval phrase | Named at-risk invariant |
|---|---|---|---|
| | | | |

Approval requires the literal phrase `APPROVED DESIGN vX.Y.Z` plus one sentence naming the invariant considered most at risk. Agents must refuse approvals lacking the sentence.
