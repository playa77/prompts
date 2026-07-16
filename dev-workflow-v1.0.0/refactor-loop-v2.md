# REFACTOR LOOP — Metric-Gated

**Version: 2.0.0 | 2026-07-10** (supersedes architecture_satisfaction_loop.md)
**Recommended temperature: 0.2**

"Happy with the architecture" is not a stop condition — it is self-graded homework. This loop replaces satisfaction with declared, measurable targets.

## Phase 0 — Declare before touching anything

Produce and log to DECISIONS.md (as one R2 entry) BEFORE the first edit:

1. **Metrics** (1–3, no more): pick from — dependency cycle count, module fan-in/fan-out, max/median file length, duplication percentage, test suite runtime, public API surface count, or another concrete measure. For each: the measurement command, the CURRENT value (measure it now), and the TARGET value.
2. **Stop condition**: target met, OR budget exhausted (declare the budget: N commits or N hours), whichever comes first.
3. **Invariant guard**: confirm all DESIGN.md guard tests are green before starting. They must stay green after every step.

If you cannot express why the refactor is wanted as a metric, STOP and report that to the human — that refactor is an R3 discussion, not a loop.

## Loop

Per step:

1. One coherent refactoring step (single concern per commit).
2. Live-test the system (actually run it, not just the test suite).
3. Run all gates: build, lint, full tests, invariant guards.
4. Re-measure the declared metrics. Log the values.
5. Commit with the metric delta in the commit message.
6. Track progress in `/tmp/refactor-<projectname>.md`: step, metric values, next step.

## Termination

- Target met → final entry in DECISIONS.md with before/after metric values. Done.
- Budget exhausted with target unmet → STOP regardless of how close. Report metric trajectory to the human; continuing is the human's R2 decision, not yours.
- Any invariant guard goes red and cannot be fixed within the current step → revert the step, log, report.

Banned: continuing because "one more step would make it cleaner". The budget is the budget.
