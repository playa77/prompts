# The Attention-Budget Workflow

**Version: 1.0.0 | 2026-07-10**

A development process for one architect operating 3–5 concurrent AI-agent projects (50k–250k LOC each). This guide teaches the whole process to someone who has never seen it. Read it once top to bottom; setup takes under an hour per project.

---

## 1. Why this exists

The bottleneck in agent-driven development is no longer model capability. It is **human attention at the review gate**.

Older workflows assumed a human reads architecture documents end-to-end and catches errors. That worked when generation was slow and you ran one project. Today an agent produces fifty pages in minutes, across several parallel projects, and every modern agent phrases its questions as suggestions ("I'd propose X — sound good?"). Accepting a suggestion costs zero effort. The result is a process where every formal gate exists on paper and none exist in practice: the human clicks "ok" and the agent's confidence score measures its confidence in a spec the human never actually engaged with.

This workflow accepts the constraint instead of fighting it. Human attention is a fixed, small budget. The design goal is to **spend that budget only where models are weak and errors are irreversible**, and to make everything else either machine-verified or randomly audited.

Three principles govern everything below:

1. **Reversibility determines attention.** Decisions are tagged R1 (cheap to reverse), R2 (costly to reverse), or R3 (one-way door). Humans review R3 always, R2 by random sample, R1 never.
2. **Typing forces thinking.** For R3 decisions, the human must state the decision and one reason in their own words. "Ok", "sure", and "you decide" are invalid answers by contract. This is the single most important rule in the system.
3. **Agents never grade their own homework.** Every claim of completion must map to machine-verifiable evidence (a test name, a CI run, a file:line). "Refactor until happy" and "fully implemented and tested" (self-reported) are banned phrases.

## 2. The artifacts

Each project contains exactly these documents. Everything is versioned (SemVer) and lives in the repo.

| File | Written by | Read by | Length |
|---|---|---|---|
| `DESIGN.md` | Agent drafts, human approves | **Human, fully, every version bump** | 1–2 pages, hard limit |
| `DECISIONS.md` | Agent appends, human reads R3 entries | Human (R3 only) + agents | Append-only ledger |
| `ROADMAP.md` | Agent generates from DESIGN.md | Agents; human spot-checks | As needed |
| `CHANGELOG.md` | Agent appends | Human skims | Append-only |
| `AGENTS.md` (project) | Agent drafts, **human approves like code** | Agents | As needed |

What disappeared compared to the classic design-doc / tech-spec / roadmap trio: the standalone **technical specification is demoted**. Its contents get split — interfaces and invariants move into the compressed `DESIGN.md` (the human-must-read core), and per-feature detail moves into the roadmap's work packages as machine-verifiable acceptance criteria, where agents consume it. Nobody was reading the 50-page spec anyway; this makes that honest.

### 2.1 DESIGN.md — the only document you must fully read

Hard limit of two pages. It contains only what a human is uniquely qualified to judge:

- **Intent** — three sentences on what this is and for whom.
- **Non-goals** — what this deliberately does not do.
- **Invariants** — numbered statements that must never become false. Each tagged either `[TESTABLE → test name]` or `[UNTESTABLE → review trigger]`. Untestable invariants automatically flag any touching change for human review. Examples: "The API key is never written to any file on disk `[TESTABLE → test_no_key_in_fs]`", "The UI never blocks longer than 200ms on network I/O `[TESTABLE → perf suite]`", "Error messages never expose internal paths to the end user `[UNTESTABLE → review any change to error.rs]`".
- **Interfaces** — the external surface only: IPC commands, HTTP endpoints, file formats, CLI. Signatures, not implementations.
- **Risks** — top 3–5, one line each.

The invariants section is the heart of the system. It is the compressed form of your architectural judgment, and it is what the machine gates enforce forever after. Time spent making invariants sharp pays back on every subsequent work package.

Approval ritual: the human may not approve a `DESIGN.md` version by saying "ok". Approval requires typing `APPROVED DESIGN vX.Y.Z` **plus one sentence naming the invariant you consider most at risk**. If you can't name one, you haven't read the document, and the agent is instructed to refuse the approval.

### 2.2 DECISIONS.md — the decision ledger

Append-only. Every non-trivial choice the agent makes during interview, planning, or implementation gets a three-line entry:

```
D-041 | 2026-07-10 | R2
Decision: SQLite via rusqlite for run history, not JSON files.
Rejected: flat JSON per run (chosen against: query cost at >1k runs).
Status: auto-accepted (R2). Audit: none yet.
```

Reversibility tiers:

- **R1** — reversible in under an hour (naming, file layout, internal helpers). Agent decides silently, logs, never asks.
- **R2** — reversible at a real but bounded cost (library choice, schema shape, internal module boundaries). Agent decides, logs with rejected alternative, proceeds. Subject to random audit.
- **R3** — one-way doors (external API contracts, data formats persisted to user machines, security model, framework choice, anything users will depend on). Agent **stops**, presents the branch consequences **without a recommendation**, and requires a typed decision plus one reason from the human. See the interview prompt for the exact protocol.

The ledger doubles as your **re-entry point for table-switching**: coming back to a project after days, read the last five entries and the open R3s. That is the table state. You no longer depend on the agent's self-summary.

### 2.3 Project AGENTS.md is reviewed like code

Agent-written project docs drift toward describing (and flattering) the current state. Rule: the project `AGENTS.md` is a **contract, not a description**. Status claims ("fully tested") are banned from it; state lives in the changelog and CI. Any agent edit to `AGENTS.md` goes through the same diff review as source code.

## 3. The lifecycle

### Phase 0 — Interview (prompt: `prompts/interview-v2.md`)

The revised interview replaces open-ended "interview me until 95% sure" with **forced-choice elicitation**. Key mechanics, explained fully in the prompt file:

- The agent classifies every open question by reversibility before asking.
- R1 questions are never asked — assumed and logged.
- R2 questions are asked as neutral multiple choice with symmetric tradeoffs and **no recommendation**. Answering with a letter is fine.
- R3 questions (max 5 per project — if more emerge, the agent must batch and prioritize) are asked open-ended, with no default offered, and the agent contractually rejects "ok / sure / you decide" as answers.
- The interview ends by producing the draft `DESIGN.md` and a seeded `DECISIONS.md`, then requesting the approval ritual from §2.1.

Why this fixes the rubber-stamping: the old prompt let the model do the thinking and you do the clicking. This one makes low-stakes thinking the model's job entirely (no clicks to give), and makes high-stakes thinking impossible to delegate (no defaults to click).

### Phase 1 — Roadmap generation

After `DESIGN.md` approval, the agent generates `ROADMAP.md`: atomic work packages, each with machine-verifiable acceptance criteria (you already do this — unchanged). New rule: every acceptance criterion must be **executable or observable** ("`cargo test wp_07` passes", "endpoint returns 429 after 10 req/s") — no criterion of the form "code is clean" or "works correctly". The agent must also map each DESIGN.md invariant to at least one work package that installs its guard test, or mark it `[UNTESTABLE]`.

The human does **not** read the whole roadmap. Read the work-package titles and the invariant-coverage table at the top. That's it.

### Phase 2 — Execution loop

Per work package: agent implements → gates run → changelog entry → ledger entries for any R2/R3 encountered → commit. The gates are mechanical:

1. Build + typecheck + lint clean.
2. Full test suite green, including all invariant guard tests.
3. New/changed acceptance criteria demonstrably met (agent cites evidence: test names, file:line).
4. Changelog appended.
5. Any R3 encountered mid-implementation halts the package until the human decides.

Nothing here requires human attention unless an R3 fires or a gate fails twice.

### Phase 3 — Random audit (prompt: `prompts/audit-pack.md`)

This is the ritual that replaces "read everything" and keeps your calibration alive. **Once per working session per active project** (or per 5 completed work packages, whichever comes first):

1. Pick one completed work package *at random* — actual dice or `shuf`, not "whichever seems interesting". Randomness is the point: it samples the true quality distribution instead of the packages that happened to catch your eye, and it prevents your mental model of the codebase from decaying.
2. The agent assembles an **audit pack** (≤2 pages): the acceptance criteria with evidence mapping, a diff summary, the three biggest shortcuts it took, and the single place a hostile reviewer would object first.
3. You review the pack and, if anything smells wrong, the actual diff. Budget: 15 minutes.
4. Verdict goes in the ledger: `AUDIT D-xxx: pass / pass-with-notes / fail`. A fail triggers a repair work package and — important — an audit of one *additional* random package (fails cluster).

If you have a second model family available (you do — GLM as oracle), have the **auditor role run on a different family than the implementer**. Correlated blind spots between an implementer and a reviewer sharing weights are real; cross-family review is the cheapest diversity you can buy.

### Phase 4 — Refactoring (prompt: `prompts/refactor-loop-v2.md`)

"Refactor until you are happy" is replaced by: **declare the target metric before touching anything**. The agent must state, up front: the metric(s) (dependency cycles, module fan-in/out, max file length, test runtime, duplication score — whatever fits), the current value, the target value, and the stop condition. Then the usual loop: step → live-test → gates → commit → log. The loop ends when the metric target is met or the declared budget is exhausted — never when anyone is "happy".

## 4. The session ritual (the whole human job, ~15 min/project)

When you sit down at a table:

1. Read the ledger tail — last 5 entries + all open R3s. (2 min)
2. Decide open R3s: typed decision + one reason each. (variable — this is the real work)
3. Run one random audit if due. (15 min)
4. Skim the changelog since last session. Diffstat, not diffs. (2 min)
5. Approve any pending `DESIGN.md` or `AGENTS.md` version bumps — full read, these are the only full reads in the system. (rare)

Everything not on this list is explicitly *not your job*. The discipline is symmetric: no skipping the ritual, and no ad-hoc deep-diving outside it (that's how attention budgets die).

## 5. Setup — implementing this from zero

1. Create the project repo. Copy `templates/DESIGN-template.md` → `DESIGN.md` (v0.1.0) and `templates/DECISIONS-template.md` → `DECISIONS.md`.
2. Install the prompts where your agent harness picks them up (for opencode: as commands or pasted at session start). `interview-v2.md` runs once per project; `audit-pack.md` and `refactor-loop-v2.md` run on demand.
3. Append `AGENTS-global-delta.md` to your existing global `AGENTS.md` and bump its version. It adds the ledger rules, reversibility tiers, banned self-grading phrases, and the R3 stop-protocol, so every agent role enforces the contract even outside the interview.
4. Wire the gates: CI (or a local `make gate` script) that runs build, lint, tests, and greps the changelog for an entry newer than HEAD~1. No green, no commit.
5. Run the interview. Approve the design with the ritual phrase. Let the agent generate the roadmap. Start the execution loop.
6. Put a recurring reminder in your calendar or shell MOTD: "audit due?" — the random audit is the piece humans silently drop first.

## 6. Anti-patterns (each of these is how the old process died)

- **Accepting a default on an R3.** The agent is instructed to refuse, but if it slips: any "sure" on a one-way door is a bug in the process, treat it as one.
- **Strictening prompts to compensate for not reading.** More contractual text enlarges the review surface; your attention is constant; net effect negative. Shrink what must be read instead.
- **Letting the agent summarize project state at re-entry.** That's the ledger's job. An agent summary is one more default to accept.
- **Auditing the package you're curious about.** Curiosity-sampling is biased sampling; roll the die.
- **Self-graded loops.** Any instruction containing "until you are satisfied/happy/confident" without an external metric.
- **Status claims in AGENTS.md.** "Fully implemented and tested" is CI's sentence to say, not the agent's.

## 7. Versioning this workflow

The workflow itself is an artifact. Keep this directory in a repo, bump versions when you change prompts or rules, and log workflow changes in a changelog like everything else. When something fails — an audit misses a defect, an R3 slips through — the fix is a versioned change to a prompt or a rule here, not a resolution to "pay more attention". Resolutions don't survive contact with five tables; process changes do.
