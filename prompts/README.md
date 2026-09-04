# Prompts for building the gold set

Twelve numbered prompts, run in order, per patent. They turn raw pipeline output into a
verified gold set of **three files — `reactions.json`, `compounds.json`, `pathways.json`** —
each checked field by field, with the check itself logged. Reactions and compounds are
built first; pathways last, because a pathway is a projection of the other two and can
only be gold once they are.

## How they are run

One Claude session per patent, started inside that patent's folder, which holds only:

    <PATENT_ID>/
      input/    one *-enriched-numbered.md   (the only source; all line numbers refer to it)
      output/   reactions.json, compounds.json, pathways.json
      audit/    created by the prompts

The extraction code and its prompts are deliberately kept out of the folder: they would
bloat context and contaminate the judgement of the thing being measured. Each prompt is
self-contained — all it needs is the `.md`, the json files, and chemical knowledge. **No
prompt takes a patent id**; each reads the single `*-enriched-numbered.md` in `input/`. If
a field's meaning is genuinely ambiguous, the prompt says to ask, not to hunt for pipeline
code.

## The order

    PHASE 1 — the COUNT: does the file hold everything the patent describes?
      1  reactions-identification     read the patent, count the reactions
      2  reactions-reconciliation     match against reactions.json, flag
      3  compounds-identification     read the patent, list the compounds
      4  compounds-reconciliation     match against compounds.json, flag

    PHASE 2 — the CONTENT: is every field of every record right?
      5  reactions-record-verification   every field of every reaction
      6  compounds-record-verification   every field of every compound

    PHASE 3 — the FINAL CHECK: is it actually gold?
      7  reactions-final-gold-check   re-derive every field, correct as you go
      8  compounds-final-gold-check   re-derive every field, correct as you go

    PHASE 4 — the ADVERSARIAL RE-CHECK: can a fresh reader prove it wrong?
      9  adversarial-independent-audit   fresh no-context agents hunt for what was missed

    PHASE 5 — the ROUTES: which reactions chain into each synthetic route?
      10 pathways-identification   read the patent, count the distinct routes
      11 pathways-reconciliation   match against pathways.json, flag
      12 pathways-gold-check       re-project from the gold, merge routes, decide

Identification passes (1, 3, 10) never open the extraction output — that independence is
their whole value. Reconciliation (2, 4, 11) matches **by identity, never by number**, and
only flags. Verification (5, 6) checks one record at a time, re-reading the whole `.md` for
each. The gold checks (7, 8, 12) assume nothing, re-derive every field, and are the only
passes that correct as they go and end with a plain yes/no. The adversarial pass (9) hands
the finished reactions and compounds to fresh agents with no context and asks them to prove
the gold wrong — it has found real defects on files that passed Phase 3.

**Pathways are a peer artifact, not an afterthought — they simply run last.** A pathway is
a projection: its steps are the gold reactions and its KSM/intermediates/product are gold
compounds, so `pathways.json` can only be gold after both of those are. The three pathways
prompts mirror the same shape as reactions and compounds (identify, reconcile, gold-check),
carry their own adversarial round inside identification, and the gold check re-projects the
step content from the gold before judging the route-specific fields (linkage, KSM choice,
overall yield, tags).

Phases 1–2 flag and stop; fixing happens only after review. That order is what stops a bad
correction going into the ground truth.

## The audit folder

Every finished patent carries an `audit/` folder holding **exactly four files — nothing
else** (no merge ledgers, no separate verdicts file, no README, no `.bak`):

    audit/
      reactions_audit.csv   field-by-field check of reactions.json
      compounds_audit.csv   field-by-field check of compounds.json
      pathways_audit.csv    field-by-field check of pathways.json
      findings.csv          every finding and its verdict, one sheet

**The three `*_audit.csv` sheets are field-level** — one row per field checked, passes
included. An `OK` row is evidence the field was looked at; a sheet that lists only failures
cannot be told from one where most fields were never checked. Every row carries the `.md`
line number, what the source says, and what the json holds, side by side. Status values:
`OK`, `MISSING`, `WRONG`, `NOTE`, `EXCLUDED`, `FIXED`. Headers:

    reactions_audit.csv: #, Reaction, Patent step, Line (English), Field, Compound or item,
      Role in the source, What the source says (English), What reactions.json has, Status,
      "Problem, in plain words"
    compounds_audit.csv: #, Compound, Section, Line (English), Field, Role in the source,
      What the source says (English), What compounds.json has, Status, "Problem, in plain words"
    pathways_audit.csv:  #, Pathway, Section, Line (English), Field, Item, Role in the source,
      What the source says (English), What pathways.json has, Status, "Problem, in plain words"

**`findings.csv` is the single findings sheet** — log and verdict together. The flagging
passes (2, 4, 11) append one row per missing/extra/wrong/duplicate record, verdict left
open. The gold checks (7, 8, 12) and the adversarial pass (9) fill the verdict on every row
and add a row for each `MISSING`/`WRONG` audit row — none skipped. The file is not gold
while any verdict says `STILL TRUE`. Columns:

    #, Raised in pass, File, Line (.md), Finding, Status when raised, Verdict, Resolution / evidence

- **Status when raised:** `Missing`, `Extra`, `Wrong`, `Duplicate`, `Definitional`, `OBSERVATION`.
- **Verdict:** `NO LONGER TRUE`, `STILL TRUE` (say what blocks it), `NEVER TRUE` (say why).
- **Resolution / evidence** points at the current file and the patent — field, value, line —
  so the verdict is checkable from its own row.

**Dedup is recorded as findings, not a ledger.** The pipeline stores one reaction (and one
pathway) per section × step, so the same transformation or route is restated across Claims,
Summary, Example and Scheme. Prompts 2 and 11 flag those as `Duplicate`; Prompts 7 and 12
collapse each group to the richest record, unioning any non-null field a duplicate carried,
and close it as a `Duplicate → NO LONGER TRUE` row naming the survivor and what folded into
it. **Never write a `merged_from` field** onto a record; keep `source_sections`. A genuinely
different run (a conflicting temperature, reagent or yield) or a distinct route's step is
never merged.

## The rules, learned the hard way

- **Never open the extraction output during an identification pass.** Once you have seen its
  answer you reproduce it, and your count is worthless.
- **Reconcile by identity, never by number.** Two files can hold the same count and disagree
  on half of it. `DMF` and `N,N-dimethylformamide` are one compound.
- **Never treat the pipeline as correct.** It is the thing being measured. Look up what a
  field is DEFINED to hold; never assume a value is absent for a good reason.
- **A step told twice covers itself.** Where claims and summary both record a step, a value
  on one is not missing from the other. Read them together.
- **Prose is not capture.** A value in `notes` or `molar_ratio_text` is invisible downstream.
  "Recoverable in the prose" is not "not lost".
- **Never withdraw a finding because the schema has no field.** Flag it; add the field if it
  matters. Check first whether a general field like `analytics` already fits.
- **A `null` is not evidence the patent is silent.** Checking is the only way to tell an
  absent value from an uncaptured one.
- **List every item individually.** 43 matches means 43 rows; ranges hide what the reader
  came to check.
