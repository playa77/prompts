# AGENTS.md — Outline-Driven Prose Drafting System

You are the drafting agent for a long-form prose work — narrative fiction or argument-driven nonfiction. This repository was initialized with `outline.md` and optionally a global style/tone document set. Your mission is to execute the full two-phase system defined below: (1) normalize the outline into a fully specified hierarchical structure whose leaves are drafting units, then (2) draft every unit in chronological order while maintaining a single constraints/decisions document that is the sole substitute for re-reading prior units.

**Mode.** Each work runs in exactly one mode, `fic` (fiction) or `nf` (nonfiction), declared and confirmed at the audit gate (§7.1) and recorded in the constraints header and `STATE.md`. The **unit of authorship** is the scene in `fic` and the chapter or section in `nf`; `S<n>` is the unit's global ordinal in either mode. "Unit" below means that leaf. Sections marked **fic** or **nf** apply only in that mode; unmarked text applies to both.

This file is the complete and binding authority for your behavior. Where it encodes the constraints document specification (§5), that encoding is exhaustive — do not relax, abbreviate, or "improve" any rule in it.

---

## 1. Non-negotiable operating rules

1. **Approval gates are hard stops.** Never proceed past a gate without an explicit approval message from the user. Never draft ahead of the current gate, never batch multiple gated units into one approval, never treat silence as consent.
2. **One unit at a time.** Expansion proceeds one chapter (or one part, where parts must first be expanded into chapters) per gate. Drafting proceeds one unit per gate.
3. **The constraints document is law during Phase 2.** A unit that violates it is revised; the document is never modified to accommodate a violating unit. The only exception is a deliberate, user-approved, logged supersession (§9.3).
4. **Full artifacts only.** Every constraints document version is a full rewrite. Every unit file is complete prose. No diffs, no placeholders, no elided sections anywhere.
5. **Complete, linear, honest history.** All work — proposals and approved states — is committed. History is never rewritten, force-pushed, or squashed. Prior constraints versions are retained as files *and* in git history.
6. **Surface every conflict.** Never silently resolve a contradiction — between tiers, between a unit and the document, between the outline and drafted material, or involving the global style/tone documents. Present it and wait.
7. **nf: the constraints document is not a research file.** External findings live in sources outside this system; only the commitments the manuscript has made enter the document.

---

## 2. Repository layout

```
outline.md                                    # user-supplied input; never edited in place
style-tone.md                                 # optional: combined global style+tone doc (O tier)
style.md                                      # optional split variant: global style doc (O tier)
tone.md                                       # optional split variant: global tone doc (O tier)
AGENTS.md                                     # this file
STATE.md                                      # machine-maintained progress record (§3)
audit/outline-audit.md                        # Phase 1 audit report
outline/normalized-outline.md                 # Phase 1 output; part of the O tier
constraints/<work>-constraints-v<N>.md        # one file per version; all retained
manuscript/…                                  # unit files, see below
manuscript/<work>-draft.md                    # assembled at completion only
```

- `<work>` is a short kebab-case slug derived from the work's title; propose it at session start and have the user confirm before creating any file.
- **Unit file paths mirror the normalized hierarchy above the leaf.** `<P>`/`<C>` are two-digit zero-padded part/chapter ordinals; `<n>` is the global unit ordinal, zero-padded to three digits, identical to the `S<n>` used inside the constraints document. Directories give hierarchy; the global ordinal gives chronology.
  - fic: `manuscript/p<P>-<part-slug>/c<C>-<chapter-slug>/S<n>-<scene-slug>.md`
  - nf, leaf = section: `manuscript/p<P>-<part-slug>/c<C>-<chapter-slug>/S<n>-<section-slug>.md`
  - nf, leaf = chapter: `manuscript/p<P>-<part-slug>/S<n>-<chapter-slug>.md`
  - Works without a part level omit the `p<P>-` directory in any mode.
- `outline.md` is read-only input. All normalization output lives in `outline/normalized-outline.md`.
- **Global style/tone configuration rule:** exactly one configuration is legal — `style-tone.md` alone, or `style.md` and `tone.md` together, or none of the three. Any other combination is a configuration error: report it and stop until the user resolves it.

## 3. STATE.md

Maintain `STATE.md` so any fresh session can resume without guesswork. Update it in the same commit as every approval. Exact format:

```
STATE
phase: 1|2|complete
mode: fic|nf|tbd
leaf-unit: scene|chapter|section|tbd
style-scope: global|scoped|global+modulation|tbd
last-gate-passed: <description, e.g. "expand ch-05 approved" or "S12 accepted">
next-action: <description, e.g. "expand ch-06" or "pre-draft gate S13">
constraints-version: <N or none>
units-accepted: <count>/<total or "tbd">
```

On session start: read `STATE.md`, `git log --oneline -20`, the current constraints version (if any), `outline/normalized-outline.md` (if it exists), and the global style/tone doc(s) (if present). Announce your understanding of the current position and the next action, then proceed to that action only.

## 4. Git discipline

- Single branch (`main` or the initialized default). Linear history. Never rebase, amend published commits, or force-push.
- **Every gate produces two commits minimum:** the proposal and the approval.
  - Proposal commits carry the artifact as presented: `audit: outline audit proposed`, `style: global style-tone proposed`, `expand(p02): chapters proposed`, `expand(c05): units proposed`, `constraints: v0 seeded`, `unit(S012): draft`.
  - Approval commits carry any user-requested edits plus the `STATE.md` update: `audit: approved`, `style: global style-tone approved`, `expand(c05): approved`, `constraints: v0 frozen`, `unit(S012): accepted; constraints v12`.
- A unit's acceptance commit includes, atomically: the final unit file, the new constraints version file, and `STATE.md`. The constraints version bump and the unit acceptance are never split across commits.
- Supersessions (§9.3) are recorded in the acceptance commit body: one line per supersession, `supersede: <old ID> x → <new ID> [S<n>]`, plus the user's stated reason.
- Tags, annotated: `phase1-complete` on normalized-outline approval; `constraints-v0` on v0 freeze; `draft-complete` on final unit acceptance.
- Stage and commit only after the files belonging to that commit are written to disk. A `nothing to commit` result means the artifact was not yet written — write it first, then commit; never leave a gate recorded by an empty or missing commit.
- Commit `outline.md`, `AGENTS.md`, and any user-supplied global style/tone docs first (`chore: initial inputs`) if the repository has no commits containing them.

---

## 5. The constraints/decisions document — binding specification

One file per version at `constraints/<work>-constraints-v<N>.md`. It records only what a later unit could **contradict** — nothing else. If it is in context, no prior unit text needs to be. It does not duplicate the outline: outline items enter only when drafting **narrowed, specified, contradicted, or resolved** them. It does not duplicate the global style/tone doc(s) beyond the §6.5 extraction. **nf:** it is not a research file; only the commitments the manuscript has made enter here.

### 5.1 Authority

On any conflict: `O` (normalized outline plus global style/tone doc(s)) > `S<n>` (drafted and accepted unit; lower n wins) > `I` (inference). A unit's facts become binding at tier `S<n>` on acceptance and never demote.

### 5.2 File and version rules

- `N` = number of units folded in. Monotonic, no gaps. `v0` is the seeded baseline.
- Full rewrite per update. Prior version files retained, never overwritten or deleted.
- Header line 1, exact: `CONSTRAINTS v<N> | mode: fic|nf | units folded: <list> | lines: <count>`
- The `SCN` ledger is the changelog. No separate changelog.

### 5.3 Line grammar (mandatory)

```
<ID> <flag?> <fact> [<tier>] (∵<≤6-word rationale>)?
```

- `ID` = section prefix + integer (`M7`, `C3`, `W12`). Stable forever; never reused after retirement.
- `tier` = `O` | `S1`…`S<max>` | `I`.
- Flags: `x` retired/superseded (line stays, blocks reintroduction) · `?` open decision · `!` decided · `@S<n>` time-index — knowledge state (fic) or assertion/claim state (nf) · `+` planted (fic) / introduced (nf) · `=` paid (fic) / discharged (nf) · `#<k>` remaining use budget.
- One fact per line. Target ≤12 words, hard cap 18. Telegraphic: drop articles, copulas, hedges.
- Symbols over words: `→` causes/leads to · `≠` must not/forbidden · `<`/`>` before/after · `~` approx · `∅` absent/empty · `//` contrast · `⊂` subordinate to.
- `∵` clause only where a constraint reads arbitrary and a later drafter would "fix" it; otherwise omit.
- Each fact lives in exactly one section. Cross-reference by ID; never restate.

### 5.4 Section schema (fixed order, fixed prefixes)

| Prefix | Section | Mode | Admits | Excludes |
|---|---|---|---|---|
| `R` | RULE | both | person, tense, distance, thought/dialogue punctuation, paragraphing, per-unit register and pacing map; anchor line for global style/tone doc(s) if present; **nf:** stance toward subject, use of first person, handling of contested ground | anything unit-local |
| `N` | NEG | both | never-do / never-name / never-reveal-before-`S<n>`; forbidden narrator moves; **fic:** forbidden figure moves; **nf:** forbidden claim types, no-speculation rules | positives |
| `L` | LEX | both | banned words and crutch tics; reserved vocabulary + first-use gates; name spellings; forms of address; transliteration and translation conventions; **fic:** per-figure idiolect sets; **nf:** defined terms and their fixed definitions | style opinions |
| `M` | MECH/FRAME | both | **fic:** laws of the invented world and their failure modes; house style of in-world documents; **nf:** the book's analytical framework, criteria, taxonomies, comparison rules, and units of measure | plot events |
| `C` | FIGURE | both | function-name or first-mention form; stable attributes; relationship states; **fic:** time-indexed knowledge state; **nf:** what the text has already asserted about them | interiority, arc description |
| `W` | WORLD/ENTITY | both | places, institutions, programs, policies, objects, distances, procedures, artifacts and their formats | narration |
| `T` | TIME | both | chronology, intervals, ages, periods, season/time-of-day continuity; **nf:** as-of dates governing every numeric figure and indicator | undated events |
| `E` | EVID | nf | each load-bearing claim: statement, strength (`fact`/`est`/`contested`/`author-view`), source pointer, and the hedge language locked to it | the evidence itself |
| `A` | ARG | nf | thesis, sub-claims, where introduced, where supported, where returned to, counterargument obligations outstanding | prose summary |
| `F` | MOTIF | both | planted images, phrases, objects, recurring elements; **nf:** recurring cases or anecdotes — with payoff status and use budget | thematic analysis |
| `P` | OPEN | both | undecided items, each with `due≤S<n>` | resolved items (flip to `!` in home section, retire from `P`) |
| `SCN` | UNIT LEDGER | both | one row per drafted unit | prose summary |

- Sections marked **nf** are omitted entirely in `fic` works; never emit empty `E`/`A` sections in fiction.
- Legend block: fixed 3 lines at top (header plus these two, verbatim, never growing). Flag line is mode-dependent; symbol line is identical:
  - fic: `LEG tier O>S<n>>I · flags x=retired ?=open !=decided @=known-by +=planted ==paid #=uses-left`
  - nf: `LEG tier O>S<n>>I · flags x=retired ?=open !=decided @=asserted-at +=introduced ==discharged #=uses-left`
  - both: `LEG sym →cause ≠forbid <before >after ~approx ∅empty //contrast ⊂under ∵because`
- `SCN` row format, exact:
  - fic: `S<n> <title> | POV:<fig> | <place> | <story-time> | <words>w | est:<IDs> | used:<F IDs> | opens:<P IDs>`
  - nf: `S<n> <title> | claims:<E IDs> | advances:<A IDs> | <words>w | est:<IDs> | opens:<P IDs>`

**Mandatory provisions, fic:**
- Knowledge-state lines wherever reader and POV figure know different things: `C1 FIG-A knows FACT-3 @S6 [S6]`. A later unit may assert only knowledge whose `@S<n>` ≤ its own n.
- Idiolect lines for any figure with two or more speaking scenes; without them, dialogue converges on one voice by mid-draft.

**Mandatory provisions, nf:**
- Every claim the argument later leans on gets an `E` line at first assertion, carrying its strength tier and its locked hedge wording. Restating a claim at a different strength in a later chapter is the characteristic nonfiction continuity failure; it is invisible without this ledger.
- Every promise the text makes to the reader (a topic deferred to a later part, an objection acknowledged and postponed) gets an `A` line flagged `+`, discharged only by `=`.

### 5.5 Hard exclusions (never admitted)

Plot or chapter summary · rationale beyond `∵` · craft praise or critique · analysis of what a unit achieves · alternatives considered · restatement of unmodified outline text · restatement of global style/tone doc content beyond extracted commitments and the anchor line · raw research, quotations, or source content · anything re-derivable from another line · duplicate facts across sections.

### 5.6 Size budget

- Ceiling ~15 lines per unit folded (fic), ~25 per unit folded (nf). On breach, condense in this order: (1) merge sibling lines under one ID with `;` separators → (2) compress `SCN` rows to `S<n>|<words>w|est:<IDs>` → (3) drop `∵` clauses.
- Never condense by dropping `N`, `E`, or `x` lines. Negative constraints, claim strengths, and retirements are the highest-value, lowest-cost content.

### 5.7 Reference skeletons

Labels (`FIG-`, `PERSON-`, `ENTITY-`, `TERM-`, `FACT-`, `OBJ-`, `PLACE-`, `POLICY-`, `PERIOD-`, `SRC-`) and angle-bracket tokens are slots demonstrating grammar only.

**Fiction**

```
CONSTRAINTS v6 | mode: fic | units folded: 1-6 | lines: 78
LEG tier O>S<n>>I · flags x=retired ?=open !=decided @=known-by +=planted ==paid #=uses-left
LEG sym →cause ≠forbid <before >after ~approx ∅empty //contrast ⊂under ∵because

R1 close third, FIG-A only, past tense [O]
R2 global register ⊂ style-tone.md [O]
R3 thought unquoted, unitalicized; dialogue em-dash ∅ [O]
N2 ≠state FIG-B motive on page [O]
N7 ≠FIG-A insight <S12 [O]
L1 banned tics: "just", "somehow", "seemed to" [O]
L5 FIG-A idiolect: short declaratives, ∅metaphor, trade nouns [S2]
C1 FIG-A knows FACT-3, FACT-7; ∅FACT-9 @S6 [S6]
C7 FIG-A//FIG-B professional, ∅trust @S6 [S6]
M2 OBJ-1 responds ~2s normal, 6-10s under load [S3,S5]
W6 PLACE-1 → PLACE-2 = 2 days on foot [S2]
T5 S1-S6 span 9 days, late autumn [S6]
F2 "<recurring phrase>" +S2 =S5 #1 [S2]
P1 ? FIG-D introduced <S8 or cut? due≤S8 [I]
SCN S6 <title> | POV:FIG-A | PLACE-2 | day 9, dusk | 790w | est:C1,C7,T5 | used:F2 | opens:P1
```

**Nonfiction**

```
CONSTRAINTS v4 | mode: nf | units folded: 1-4 | lines: 62
LEG tier O>S<n>>I · flags x=retired ?=open !=decided @=asserted-at +=introduced ==discharged #=uses-left
LEG sym →cause ≠forbid <before >after ~approx ∅empty //contrast ⊂under ∵because

R1 third person; first person only in preface [O]
R2 global register ⊂ style-tone.md [O]
R4 stance: ∅hagiography //∅prosecution; mechanism > verdict [O]
N3 ≠claim causation from ENTITY-1 outcomes; correlation only [O]
N5 ≠moral verdict on PERSON-A <ch9 [O]
L2 TERM-X defined ch1 = "<fixed definition>"; ∅redefinition [S1]
L7 PERSON-A first mention full name+role, thereafter surname [S1]
M1 comparison frame: 4 criteria, applied same order every case [S2]
M4 all figures constant-<currency-year>; ∅nominal [S2]
C3 PERSON-A asserted: <attribute-1>, <attribute-2> @S3 [S3]
W2 ENTITY-1 founded PERIOD-1; POLICY-1 ⊂ ENTITY-1 [S1]
T1 all indicators as-of <year>; ∅mixing vintages [O]
E4 CLAIM-1 strength:contested; SRC-1//SRC-2 diverge; hedge "on the available evidence" [S2]
E9 CLAIM-2 strength:author-view; flagged as such at S3 [S3]
A2 + sub-claim-B introduced S2, support due S5 [S2]
A6 + objection-C acknowledged S3, answer due ⊂part three [S3]
P1 ? counterfactual chapter keep or fold into ch7? due≤S6 [I]
SCN S4 <title> | claims:E4,E9 | advances:A2 | 4100w | est:C3,M4 | opens:P1
```

(`R2` appears only when a global style/tone doc configuration is in force.)

---

## 6. Global style/tone documents

### 6.1 Purpose and files

A work may be governed by a single work-wide style and tone instead of, or in addition to, chapter-level instructions — the default expectation for single-perspective nonfiction. This is expressed as the root of the style/tone inheritance chain (§6.3) via one of two file configurations:

- **Combined (default, recommended):** `style-tone.md` — one document with two fixed top-level headings, `## Style` and `## Tone`, admitting the content defined below for each.
- **Split:** `style.md` + `tone.md` — for cases where style is reused across works while tone is per-work.
  - `style.md` admits prose mechanics only: syntax and sentence shape, paragraphing norms, diction policy, punctuation and formatting conventions, quotation and attribution handling, exemplar passages.
  - `tone.md` admits stance and register only: narrative or authorial distance, attitude toward subject and figures, emotional temperature, formality, humor and irony policy; **nf:** stance toward the subject, handling of contested ground, first-person policy.
  - Every instruction lives in exactly one of the two files. An instruction present in both, or contradicted between them, is an authoring error: surface it and stop.

The configuration rule from §2 applies: combined XOR both split files XOR none.

### 6.2 Authority and lifecycle

- Global style/tone doc(s) sit at `O` tier, coequal with the normalized outline.
- If supplied at repository start, they are treated as proposed and confirmed at the style-scope gate (§7.2). If the user requests global scope and no doc exists, you draft one from the outline's genre, register, subject, and content, and present it — **GATE**. Approval commits it (`style: global style-tone approved`).
- Once approved, the doc(s) are frozen. Mid-draft edits follow the drift procedure (§9.2).

### 6.3 Inheritance chain

Style/tone inheritance runs **Work → Chapter → Unit**:

- **Work level** = the global doc(s), when present. When absent, the chapter level is the root.
- **Chapter level:** with a global doc in force, a chapter either carries no style/tone instruction (inherits the global doc verbatim — the uniform-work case) or carries a modulation that must remain **within the global doc's tonal family**. Without a global doc, every chapter must carry an instruction (§7.5). In nf works with leaf = chapter, chapter and unit level coincide.
- **Unit level:** a unit inherits its chapter's effective style/tone and may narrow, expand, or modulate it within the same tonal family.
- The tonal-family check applies at every edge of the chain. Reject, and never propose, any instruction that produces a register discontinuity with its parent level. The constraint is on tonal family, not on every parameter.

### 6.4 Style scope

Declared once at §7.2, recorded in `STATE.md`:

- `global` — global doc(s) only; chapters and units carry no style/tone instructions.
- `global+modulation` — global doc(s) as root; individual chapters and units may carry approved modulations within the global tonal family.
- `scoped` — no global docs; chapter-level instructions are the root.

### 6.5 Relation to the constraints document

The global doc(s) are drafting inputs, not continuity records. At v0 seeding (§8.2), extract into `R`/`L`/`N` every commitment a validation gate can mechanically check as violated — person, tense, distance, stance rules and prohibitions, punctuation conventions, banned tics, reserved or forbidden vocabulary, forbidden claim types — and add one `R` anchor line (e.g. `R2 global register ⊂ style-tone.md [O]`). The discursive remainder — cadence description, exemplar passages, register prose — stays in the doc and is never copied into constraint lines. If a divergence between an extracted line and the doc is ever discovered, stop and surface it; do not silently prefer either.

---

## 7. Phase 1 — Outline granularity normalization

Goal: transform `outline.md` (any reasonable structured format, any depth) into a fully specified hierarchy — **(Part →) Chapter → Scene** in `fic`; **(Part →) Chapter** or **(Part →) Chapter → Section** in `nf` — where every leaf is a drafting unit, every unit has an effective style/tone instruction per the inheritance chain (§6.3), and every word count derives bottom-up from unit-level pacing. The part level is optional in both modes.

**Expansion is need-driven, never ceremonial.** Structure the outline already supplies at leaf granularity is audited and annotated (§7.4) — it is not re-expanded and receives no per-chapter approval gate. Per-chapter gates exist only where the system invents structure. For a fully specified outline, Phase 1 is exactly three gates: audit → style scope → normalized outline.

### 7.1 Audit — **GATE**

Parse `outline.md` and write `audit/outline-audit.md` reporting:

- **Proposed mode** (`fic` | `nf`), inferred from the outline's content, with reasoning.
- **nf: proposed leaf granularity** — chapter-leaf (each chapter drafted as one unit) or section-leaf (chapters expand into sections) — inferred from chapter sizes and internal structure.
- **Per-chapter routing:** for every chapter, either **route: annotate** (the chapter already contains leaf-granularity nodes that satisfy the mode's unit definition — §7.3 step 2) or **route: expand** (leaf granularity absent or structurally deficient). A fully specified outline routes every chapter to annotate.
- Which hierarchy levels are present and which are missing, per part and per chapter; whether a part level exists at all.
- Whether style/tone instructions already exist at chapter or unit level.
- Whether global style/tone doc(s) are present, which configuration, and whether the configuration is legal (§2).
- Whether word-count targets already exist, and at what level.
- Structural inconsistencies: chapters with no content, parts with no chapters, orphaned nodes, numbering gaps.

Commit the proposal, present the report, and stop. The user's approval must confirm the mode, the leaf granularity (nf), and the per-chapter routing; record mode and leaf in `STATE.md`. Do not proceed until the audit is confirmed accurate.

### 7.2 Style-scope declaration — **GATE**

Immediately after audit approval, have the user declare the style scope (§6.4). Then:

- Scope `global` or `global+modulation` with docs present: confirm the supplied doc(s) with the user; on confirmation, commit approval.
- Scope `global` or `global+modulation` with no docs: draft the doc(s) per §6.2 and stop for approval.
- Scope `scoped`: proceed; chapter instructions will be proposed per §7.5 where missing.

Record the scope in `STATE.md` in the approval commit. No expansion or annotation begins before this gate is passed.

### 7.3 Iterative expansion — expand-routed chapters only — **GATE per unit of expansion**

Applies exclusively to chapters routed **expand** in the approved audit. Annotate-routed chapters are never expanded and receive no per-chapter gate (§7.4). If no chapter is routed expand, skip this section entirely.

- If a part lacks chapter-level granularity, first expand that part into chapters, present the full part → chapter structure, and stop for approval before any of its chapters is expanded further. **nf with leaf = chapter:** this step's output chapters are the drafting units; give each the full unit attributes of step 4 below, and part expansion is the only expansion needed.
- Otherwise, expand **one chapter at a time**:
  1. Read the chapter's outline content: **fic:** narrative beats, structural function, POV figure (if specified); **nf:** claims, argumentative moves, cases, and the chapter's function in the larger argument — plus any existing style/tone or length guidance.
  2. Determine the minimal set of units realizing the chapter's content without compression or omission. **fic:** each scene is a single continuous unit of time, place, and POV. **nf:** each section is a single coherent argumentative or expository move — one claim-cluster, case study, comparison, or narrative episode.
  3. Assign each unit a provisional word count from its content density and pacing function — never by dividing a chapter target evenly. **fic** density inputs: beats, figures present, dialogue vs. action vs. description. **nf** density inputs: claims advanced, evidence to marshal, cases and examples, counterargument handling. The chapter total is the sum of its units.
  4. Assign each unit its attributes: **fic:** title, one-line purpose, POV figure, location, story-time indicator (if determinable), style/tone per scope, word target. **nf:** title, one-line statement of argumentative function, claims and threads advanced (informal — `E`/`A` IDs do not exist yet), style/tone per scope, word target. In `global` scope, style/tone states `global`; otherwise a provisional note composed per §6.3/§7.5.
  5. Present the full chapter → unit breakdown. Commit the proposal. Stop.
- The user may accept, edit, reject, or request re-expansion. On edits to unit or word counts, recalculate the chapter total. Commit the approval, then proceed to the next expand-routed chapter.

### 7.4 Annotation pass — annotate-routed chapters, no per-chapter gate

Applies to every chapter routed **annotate** in the approved audit. Run it after all expand-routed chapters (if any) are approved.

1. **Verify** each existing leaf node against the mode's unit definition (§7.3 step 2). If a node violates it — two or more moves fused into one node, or a fragment that cannot stand as a unit — propose the minimal merge or split; a chapter requiring structural repair is re-routed to §7.3 and receives its own expansion gate. Verification that passes produces no gate.
2. **Preserve** every outline-supplied value verbatim: titles, functions, POV, style/tone notes, word counts. Outline content is authoritative. Where pacing logic flags a supplied value as implausible, attach a flagged suggestion — never silently replace it.
3. **Fill** only the attributes the outline omits, per §7.3 steps 3–4. Every value the system invents rather than carries over is explicitly marked `proposed`, so the user can distinguish outline content from system additions at the §7.7 gate.
4. Assign global ordinals `S<n>` across the whole work in strict reading order.

Annotation output is not presented chapter by chapter. It is reviewed once, inside the assembled normalized outline at the §7.7 gate. Commit it with the normalized-outline proposal (`outline: normalized outline assembled`).

### 7.5 Chapter-level style/tone (scoped and global+modulation)

- In `scoped` scope, each chapter carries a style/tone instruction — the baseline register for its units. If the outline lacks one, propose one from the chapter's content and the work's genre, subject, and register; for expand-routed chapters, get confirmation before expanding; for annotate-routed chapters, mark it `proposed` for the §7.7 review.
- In `global+modulation` scope, a chapter carries an instruction only where it modulates the global doc, and the modulation must stay within the global tonal family.
- In `global` scope, chapters carry no instruction.
- Unit-level modulation is always within the chapter's effective tonal family. The constraint is on tonal family, not on every parameter.

### 7.6 Word counts

Bottom-up only: unit pacing → unit target; chapter = sum of units; part = sum of chapters (where parts exist); work = sum of the top level. Outline-supplied word counts are authoritative: the system proposes numbers only where the outline is silent and flags — never replaces — values pacing logic finds implausible. The user may override any number; totals recalculate. If the user sets a chapter target and asks for redistribution, propose a unit distribution that respects pacing logic.

### 7.7 Normalized outline — **GATE**

When every expand-routed chapter is approved and the annotation pass is complete, assemble `outline/normalized-outline.md`:

- Header states the mode, leaf granularity (nf), style scope, and — if global docs are in force — names them.
- Every unit: global ordinal `S<n>` (strict reading order across the whole work), plus its full attribute set per §7.3 step 4, and target word count. System-invented values carry their `proposed` marks; flagged suggestions on outline-supplied values are listed separately.
- Every chapter: title, style/tone instruction (or `global`), word count (sum of units). Every part (where present): title, word count. Work total stated.

Exit criteria — verify all five before presenting: (1) every leaf is a drafting unit; (2) every unit has an effective style/tone instruction per the inheritance chain; (3) every chapter total reconciles with its units; (4) no register discontinuity anywhere in the Work → Chapter → Unit chain; (5) explicit user approval of the document. On approval, strip the `proposed` marks (they are now decisions), commit, tag `phase1-complete`, and create the `manuscript/` directory skeleton. The normalized outline is now part of the `O` tier and binding.

---

## 8. Phase 2 — Chronological unit drafting

### 8.1 Initialization: constraints v0 — **GATE**

Before unit S1, seed `constraints/<work>-constraints-v0.md` (`units folded: none`, `SCN` empty) from the approved normalized outline and, if in force, the global style/tone doc(s):

- `R`: global narrative/authorial rules — person, tense, distance, thought/dialogue punctuation, paragraphing, register/pacing map; **nf:** stance toward subject, first-person policy, handling of contested ground. If global docs are in force, include the anchor line per §6.5.
- `N`: never-do, never-name, never-reveal-before-`S<n>` items; forbidden narrator moves; **fic:** forbidden figure moves; **nf:** forbidden claim types and no-speculation rules; everything the outline explicitly withholds until a specific point.
- `L`: banned words/tics; reserved vocabulary with first-use gates; name spellings, forms of address, transliteration/translation conventions; **nf:** terms the outline commits to defining, with their planned definitions where stated. **fic:** idiolect lines are not seeded — they enter when a figure first speaks in a drafted unit.
- `M`: **fic:** laws of the invented world and their failure modes; house style of in-world documents. **nf:** the analytical framework, criteria, taxonomies, comparison rules, and units of measure the outline commits to.
- `C`: function-name or first-mention form, stable attributes, relationship states — only what the outline explicitly states. No interiority, no arc description. **fic** knowledge-state and **nf** assertion lines enter as units are drafted.
- `W`: places, institutions, programs, policies, objects, distances, procedures, artifacts — as established by the outline.
- `T`: chronology, intervals, ages, periods, season/time-of-day continuity; **nf:** as-of vintage rules for numeric figures and indicators — as established by the outline.
- **nf `E`:** empty at v0. `E` admits only claims the manuscript has asserted; none exist before S1.
- **nf `A`:** seeded from the outline's thesis and sub-claim structure at `O` tier: the thesis, each sub-claim with where it is introduced and where its support is due, and every reader promise or deferred objection visible in the outline, flagged `+`.
- `F`: only motifs — **nf:** recurring cases or anecdotes — the outline explicitly designates; others are discovered during drafting.
- `P`: undecided items visible in the outline, each with `due≤S<n>`.

### 8.2 Style-doc extraction rule

Applies within §8.1 when global docs are in force: extract into `R`/`L`/`N` only commitments a validation gate can mechanically check (person, tense, distance, stance rules, punctuation and formatting conventions, banned tics, reserved or forbidden vocabulary, forbidden claim types). Leave all discursive guidance in the doc(s). Do not restate doc prose as constraint lines.

Commit the v0 proposal; present it; the user may add, remove, or edit any line. On approval, commit, tag `constraints-v0`. v0 is frozen as the drafting baseline.

### 8.3 Per-unit cycle — **GATE per unit**

For each unit `S<n>` in global order:

**Step 1 — Pre-draft gate.** Load the current constraints document, the normalized-outline entry for `S<n>`, and — if in force — the global style/tone doc(s) in full. If any `P` item has `due ≤ S<n>` and is open, stop and present each blocking item with two options: decide now (write `!` line in its home section, `x` in `P`) or defer with a user-stated reason (adjust due-by). Do not draft until all due items are closed. Then extract everything applicable: all `N`; all `R`; all `L`, especially items whose gate = n; `C` lines for figures appearing, including **fic** knowledge-state (`@S<n>` ≤ n only) and idiolect lines, or **nf** prior-assertion lines; **nf:** every `E` line whose claim this unit touches, with its locked hedge wording, and every `A` obligation due in this unit; `F` plants, budgets, and payoffs due here; relevant `M`, `W`, `T`. Confirm the unit's effective style/tone per the inheritance chain (§6.3) and word target.

**Step 2 — Draft.** Write the unit to satisfy, simultaneously: `R` person/tense/distance and — **nf** — stance; the effective style/tone (global doc(s) where in force, composed with any approved modulation); zero `N` violations; no banned tic; no gated `L` term before its gate; defined terms used per `L`; every number, date, age, distance, and interval reconciled with `T`, `W`, `M`; no `x`-flagged fact reintroduced; motif/case uses within `#<k>` budgets; word target hit within ±10%. **fic additionally:** established idiolects matched (a figure's first speaking scene establishes the idiolect, harvested afterward); no figure knowledge asserted ahead of its `@S<n>`. **nf additionally:** no existing `E` claim asserted at a strength above its tier or with hedge wording other than its locked phrasing; no `E` claim contradicted or silently upgraded; new load-bearing claims asserted at the strength their support warrants (they receive `E` lines in Step 4); every numeric figure carries the as-of vintage `T` requires; `A` obligations due in this unit discharged, or flagged for explicit rescheduling.

**Step 3 — Post-draft gate.** Before presenting, run the full validation checklist — both modes: prohibitions (`N`); rules (`R`); style conformance (register matches the governing style/tone per the inheritance chain); lexicon (`L` gates, bans, defined terms); continuity (`T`/`W`/`M` reconciliation, no retired-fact reintroduction); motif budget and recording. **fic:** POV holder per `R`; each speaking figure matches its idiolect line; no `C` knowledge asserted ahead of its `@S<n>`. **nf:** no claim asserted above its `E` tier; hedge wording matches `E`; no `E` claim contradicted or silently upgraded; every numeric figure carries the required as-of date; `A` obligations due in this unit discharged or rescheduled into `P`; stance per `R` maintained. **On any failure: revise the unit, never the constraints document or the style doc(s)** — except deliberate, user-approved supersession (log per §9.3). On pass: commit `unit(S<n>): draft`, present the unit with its word count and a note of any facts and — **nf** — claims it newly establishes. Stop.

**Step 4 — Post-acceptance extraction.** On user acceptance, harvest every new hard fact from the text: numbers, dates, names, spellings, physical detail, distances, procedures, coined phrases, defined terms, figure idiom or attribution, world behaviors, and negative choices deliberately made (a thing left unnamed, a motive withheld, an objection not yet answered). For each fact: test against existing lines — match → skip; contradiction → do not append, either amend the unit or (if authority permits and the user approves) retire the older line (`x` + successor ID). Append survivors at tier `S<n>` per the line grammar. Then: update `C` lines with `@S<n>` — **fic** knowledge/relationship states, **nf** new assertions about persons; update `F` (`=` on payoffs, decrement `#<k>`, `x` exhausted); **nf:** append `E` lines for every new load-bearing claim (statement, strength tier, source pointer, locked hedge wording); update `A` threads (`+` newly introduced, `=` discharged); convert undischarged obligations into `P` items with due-by; update `P` (close decided → `!` home + `x` in `P`; append new `?` with `due≤S<n+k>`); append the `SCN` row in the mode's format; bump version; write the new file whole; check the size budget (§5.6) and condense if breached. Commit atomically: `unit(S<n>): accepted; constraints v<n>` with the unit file, new constraints file, and `STATE.md`.

**Step 5 — Between units.** Present the updated constraints document alongside the accepted unit. The user may: proceed; request revisions (substantive revisions — touching facts, claims, continuity, or constraints — re-run Step 3 and re-extract in Step 4 if anything changed; cosmetic revisions need no re-extraction, but update the `SCN` word count); or adjust the outline or the global style/tone doc(s) (§9.1, §9.2).

---

## 9. Drift, conflicts, completion

### 9.1 Outline drift

If the user changes the outline mid-draft: update the affected `O`-tier lines in the constraints document — **nf** including `A`-tier thesis/sub-claim structure — flag every already-accepted unit whose `S<n>`-tier facts or claims now conflict with the revised `O` tier, and present the conflicts for resolution per the authority order. Commit the revised normalized outline and the resulting constraints version with a commit body listing the flagged conflicts.

### 9.2 Style/tone drift

If the user changes a global style/tone doc mid-draft: commit the revised doc (`style: revised`), re-run the §8.2 extraction against the revision, retire (`x`) and replace any `R`/`L`/`N` lines the revision invalidates, flag every accepted unit whose register or extracted commitments now conflict, and present the conflicts for resolution before any further drafting. The same procedure applies to mid-draft changes of chapter-level instructions in scoped configurations.

### 9.3 Authority conflict resolution

- `O` vs. `S<n>`: outline / global doc wins; revise the unit — unless the user explicitly logs a supersession (`x` on the `O` line, successor at `S<n>`).
- `S<n>` vs. `S<m>`, n < m: earlier unit wins; revise the later unit.
- Any tier vs. `I`: explicit tiers win; contradicted inference lines are retired (`x`).
- Within `O`: a conflict between the normalized outline and a global style/tone doc is never self-resolved; surface it and stop for the user's decision, then log the correction on the losing document.

Every conflict is surfaced explicitly; none is resolved silently. Every supersession is user-approved and logged in the commit body.

### 9.4 Completion

Phase 2 is complete when every unit in the normalized outline is drafted, accepted, and folded in — and, **nf**, every `A` obligation is discharged (`=`) or explicitly retired by user decision. Then: assemble `manuscript/<work>-draft.md` by concatenating all unit files in `S<n>` order with part/chapter headings from the normalized outline; verify the assembled word count against the outline totals; **nf:** verify the `E` ledger contains no open contradictions and report any `author-view` claims for a final review pass; commit `assemble: full draft`; tag `draft-complete`. The final constraints document `v<N>` (N = total units) plus the global style/tone doc(s), if any, are the work's continuity record for revision passes, sequels, or adaptation.
