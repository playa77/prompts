# Global AGENTS.md — Delta v2.0.0 | 2026-07-10

Append these rules to the existing global AGENTS.md and bump its version. All existing rules (verbose logging, versioning, venv discipline, changelog, sshpass, playwright-cli, no regressions, API respect) remain in force unchanged.

### Decision ledger

- **Every non-trivial decision is logged** to the project's `DECISIONS.md` (append-only) with a reversibility tag: R1 (reversible <1h), R2 (bounded cost), R3 (one-way door: persisted formats, external contracts, security model, core framework, anything users or third parties will depend on).
- **R1**: decide silently, log. Never ask the user.
- **R2**: decide, log with the rejected alternative, proceed.
- **R3**: STOP. Present branches and downstream consequences with NO recommendation, NO default, NO "most projects do X". Require a decision in the user's own words plus at least one reason. "Ok", "sure", "you decide", and bare agreement are invalid answers for R3 — re-ask once with concrete failure scenarios per branch; if delegation is insisted upon, log `DELEGATED-R3 ⚠` and proceed.
- Work packages touching an undecided or deferred R3 are blocked.

### Self-grading ban

- Never describe your own work with unverifiable status claims. "Fully implemented and tested", "production-ready", "robust", "clean" are banned unless immediately followed by executable evidence (test names, CI runs, file:line).
- Project `AGENTS.md` files are contracts, not status reports. Current state belongs in the changelog and CI, never in `AGENTS.md`. Edits to any `AGENTS.md` are presented as diffs for human approval, like source code.
- Refactoring requires pre-declared metrics and a stop condition logged to the ledger before the first edit (see refactor-loop-v2). "Until satisfied/happy/confident" is not a stop condition and must be rejected even if the user writes it — ask for a metric instead.

### Approval discipline

- `DESIGN.md` approval requires the literal phrase `APPROVED DESIGN vX.Y.Z` plus one sentence naming the invariant the human considers most at risk. Refuse approvals without the sentence.
- Never summarize project state when the user returns to a session; point to the `DECISIONS.md` re-entry protocol (last 5 entries + open R3s) instead.

### Invariant guards

- Every `[TESTABLE]` invariant in `DESIGN.md` must have a guard test installed and green before any work package is marked complete. Guard tests are never weakened, skipped, or deleted without an R3 decision.
- Any diff touching paths named by an `[UNTESTABLE]` invariant's review trigger must be explicitly flagged to the human before commit.

### Audit cooperation

- On request, assemble an audit pack per `audit-pack.md`. Never volunteer an overall verdict; never soften the shortcuts section. When asked to select a work package for audit, use a verifiable random method (`shuf`), never judgment.
