# DECISIONS — <project name>

**Append-only. Never edit or delete entries; supersede them with new entries referencing the old ID.**

## Reversibility tiers

- **R1** — reversible in <1h. Agent decides silently, logs. Never asks.
- **R2** — reversible at bounded cost. Agent decides, logs rejected alternative. Subject to random audit.
- **R3** — one-way door. Agent stops; human decides in own words + one reason. No defaults offered.

## Entry format

```
D-<nnn> | <ISO date> | R<1|2|3>
Decision: <one line>
Rejected: <alternative and why it lost>      (R2/R3 only)
Reason (human, verbatim): "<...>"            (R3 only)
Status: auto-accepted | human-decided | DELEGATED-R3 ⚠ | superseded by D-<nnn>
Audit: none | AUDIT <date> pass | pass-with-notes | fail → <repair WP>
```

## Re-entry protocol (human, when returning to this project)

1. Read the last 5 entries.
2. Read all entries with open status: `DELEGATED-R3 ⚠` or pending R3.
3. That is the table state. Do not ask the agent for a summary instead.

---

## Ledger

D-001 | <date> | R1
Decision: <seeded during interview>
Status: auto-accepted
