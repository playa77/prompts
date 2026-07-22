# Spec — Running Constraints/Decisions Document for Long-Form Prose

Defines format, admission rules, update procedure, and validation gates for the single document that guarantees continuity across a long-form prose work drafted in order. Covers narrative fiction and argument-driven nonfiction. Optimized for token density, not readability. English lexemes only, telegraphic syntax.

Unit of authorship is the scene (fiction) or the chapter/section (nonfiction); `S<n>` is its ordinal in either mode. Sections marked **fic** or **nf** are omitted in the other mode; unmarked sections apply to both.

Supersedes all prior versions of this spec.

---

## 1. Purpose and authority

- One file. It is the sole substitute for re-reading all prior units. If it is in context, no prior unit text needs to be.
- It records only what a later unit could **contradict** — nothing else.
- Authority order on conflict: `O` (outline) > `S<n>` (drafted+accepted unit, lower n wins) > `I` (inference). A drafted unit's facts become binding at `S<n>` tier on acceptance; they never demote.
- It does not duplicate the outline. Outline items enter only when drafting **narrowed, specified, contradicted, or resolved** them.
- Nonfiction: it is not a research file. External findings live in sources; only the **commitments the manuscript has made** enter here.

## 2. File and version rules

- Path: `<work>-constraints-v<N>.md`, `N` = number of units folded in, monotonic, no gaps.
- Full rewrite per update, no diffs. Prior versions retained, never overwritten.
- Header line 1: `CONSTRAINTS v<N> | mode: fic|nf | units folded: <list> | lines: <count>`.
- The `SCN` ledger is the changelog; no separate changelog.

## 3. Line grammar (mandatory)

```
<ID> <flag?> <fact> [<tier>] (∵<≤6-word rationale>)?
```

- `ID` = section prefix + integer, e.g. `M7`, `C3`, `W12`. Stable forever. Never reused after retirement.
- `tier` = `O` | `S1`…`S<max>` | `I`.
- `flag` = `x` retired/superseded (line stays, blocks reintroduction) · `?` open decision · `!` decided · `@S<n>` time-index (knowledge or claim state) · `+` planted/introduced · `=` paid/discharged · `#<k>` remaining use budget.
- One fact per line. Target ≤12 words, hard cap 18.
- Drop articles, copulas, hedges. Telegraphic. Symbols over words: `→` causes/leads to, `≠` must not / forbidden, `<`/`>` before/after, `~` approx, `∅` absent/empty, `//` contrast, `⊂` subordinate to.
- `∵` clause permitted **only** where the constraint reads arbitrary and a later drafter would "fix" it. Otherwise omit; rationale belongs in conversation, not the file.
- Each fact lives in exactly one section. Cross-reference by ID, never restate.

## 4. Section schema (fixed order, fixed prefixes)

| Prefix | Section | Mode | Admits | Excludes |
|---|---|---|---|---|
| `R` | RULE | both | person, tense, distance, thought/dialogue punctuation, paragraphing, per-unit register and pacing map; nf: stance toward subject, use of first person, handling of contested ground | anything unit-local |
| `N` | NEG | both | never-do / never-name / never-reveal-before-`S<n>`; forbidden narrator moves; fic: forbidden figure moves; nf: forbidden claim types, no-speculation rules | positives |
| `L` | LEX | both | banned words and crutch tics, reserved vocabulary + first-use gate, name spellings, forms of address, transliteration and translation conventions; fic: per-figure idiolect sets; nf: defined terms and their fixed definitions | style opinions |
| `M` | MECH/FRAME | both | fic: laws of the invented world and their failure modes, house style of in-world documents; nf: the book's analytical framework, criteria, taxonomies, comparison rules, and units of measure | plot events |
| `C` | FIGURE | both | function-name or first-mention form, stable attributes, relationship states, **time-indexed knowledge state** (fic) or **what the text has already asserted about them** (nf) | interiority, arc description |
| `W` | WORLD/ENTITY | both | places, institutions, programs, policies, objects, distances, procedures, artifacts and their formats | narration |
| `T` | TIME | both | chronology, intervals, ages, periods, season/time-of-day continuity; nf: as-of dates governing every figure | undated events |
| `E` | EVID | nf | each load-bearing claim: statement, strength (`fact`/`est`/`contested`/`author-view`), source pointer, and the hedge language locked to it | the evidence itself |
| `A` | ARG | nf | thesis, sub-claims, where introduced, where supported, where returned to, counterargument obligations outstanding | prose summary |
| `F` | MOTIF | both | planted images, phrases, objects, recurring cases or anecdotes, with payoff status and use budget | thematic analysis |
| `P` | OPEN | both | undecided items, each with `due≤S<n>` | resolved items (flip to `!` in home section, retire from `P`) |
| `SCN` | UNIT LEDGER | both | one row per drafted unit | prose summary |

- Legend block: fixed 3 lines at top, verbatim, never grows.
- `SCN` row, fic: `S<n> <title> | POV:<fig> | <place> | <story-time> | <words>w | est:<IDs> | used:<F IDs> | opens:<P IDs>`.
- `SCN` row, nf: `S<n> <title> | claims:<E IDs> | advances:<A IDs> | <words>w | est:<IDs> | opens:<P IDs>`.
- **Fic:** knowledge-state lines mandatory wherever reader and POV figure know different things: `C1 FIG-A knows FACT-3 @S6 [S6]`. A later unit may assert only knowledge whose `@S<n>` ≤ its own n.
- **Fic:** idiolect lines mandatory for any figure with two or more speaking scenes; without them, dialogue converges on one voice by mid-draft.
- **Nf:** every claim the argument later leans on gets an `E` line at first assertion, carrying its strength tier and its hedge wording. Restating a claim at a different strength in a later chapter is the characteristic nonfiction continuity failure, and it is invisible without this ledger.
- **Nf:** every promise the text makes to the reader ("this is taken up in part three", an objection acknowledged and deferred) gets an `A` line flagged `+`, discharged only by `=`.

## 5. Per-unit update procedure (exact, ordered)

1. **Pre-draft:** load doc + outline entry for `S<n>`. Extract applicable `N`, `L`, gated items whose gate = n, and any `A` obligation due here.
2. Draft unit.
3. **Post-acceptance extraction:** harvest every new hard fact from the text — numbers, dates, names, spellings, physical detail, distances, procedures, coined phrases, defined terms, figure idiom or attribution, world behaviors, and negative choices deliberately made (a thing left unnamed, a motive withheld, an objection not yet answered).
4. For each: test against existing lines. Match → skip. Contradiction → do **not** append; either amend the unit or retire the older line (`x` + successor ID) if authority order permits.
5. Append survivors at tier `S<n>`.
6. Update `C` knowledge, assertion, and relationship lines with `@S<n>`.
7. `F`: mark `=` on paid motifs, decrement `#<k>` budgets, retire exhausted (`x`).
8. Nf: append new `E` lines for load-bearing claims; update `A` threads (`+` introduced, `=` discharged); convert undischarged obligations into `P` items with due-by.
9. `P`: close items the unit decided (flip to `!` in home section, `x` in `P`); append new `?` items with `due≤S<n+k>`.
10. Append `SCN` row. Bump version. Rewrite file whole.

## 6. Validation gates

- **Pre-draft gate:** no `P` item with `due≤S<n>` may remain open. Resolve first.
- **Post-draft gate (run before acceptance):**
  - Both: unit violates no `N`; person, tense, and distance match `R`; no reserved `L` term used before its gate; defined terms used per `L`; no banned tic; every number, date, age, distance, and interval reconciles with `T`, `W`, and `M` units; no `x`-flagged fact reintroduced; every motif use recorded in `F` and within budget.
  - Fic: POV holder per `R`; each speaking figure matches its idiolect line; no `C` knowledge asserted ahead of its `@S<n>`.
  - Nf: no claim asserted at a strength above its `E` tier; hedge wording matches `E`; no `E` claim contradicted or silently upgraded; every figure carries the as-of date `T` requires; `A` obligations due in this unit discharged or re-scheduled in `P`; stance per `R` maintained.
- Failure at either gate → revise the unit, not the document. The document yields only to `O`, or to a deliberate, logged supersession.

## 7. Hard exclusions (never admitted)

Plot or chapter summary · rationale beyond `∵` · craft praise or critique · analysis of what a unit achieves · alternatives considered · restatement of unmodified outline text · raw research, quotations, or source content · anything re-derivable from another line · duplicate facts across sections.

## 8. Size budget

- Ceiling ~15 lines per scene folded (fic), ~25 per chapter (nf).
- On breach, condense in this order: merge sibling lines under one ID with `;` separators → compress `SCN` rows to `S<n>|<words>w|est:<IDs>` → drop `∵` clauses.
- Never condense by dropping `N`, `E`, or `x` lines. Negative constraints, claim strengths, and retirements are the highest-value, lowest-cost content.

## 9. Skeletons

`FIG-`, `PERSON-`, `ENTITY-`, `TERM-`, `FACT-`, `OBJ-`, `PLACE-`, `POLICY-`, `PERIOD-`, `SRC-` labels and angle-bracket tokens are slots, present to demonstrate grammar only.

**Fiction**

```
CONSTRAINTS v6 | mode: fic | units folded: 1-6 | lines: 78
LEG tier O>S<n>>I · flags x=retired ?=open !=decided @=known-by +=planted ==paid #=uses-left
LEG sym →cause ≠forbid <before >after ~approx ∅empty //contrast ⊂under ∵because

R1 close third, FIG-A only, past tense [O]
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
