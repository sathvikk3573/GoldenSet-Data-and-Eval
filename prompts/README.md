# Prompts for building the gold set

Thirteen prompts, run in order, per patent — the structures pass spans stages 12–13 in one
file (`12-13-smiles-structures.md`), so the numbering runs 1–14 across thirteen files. They
turn raw pipeline output into a
verified gold set of **three files — `reactions.json`, `compounds.json`, `pathways.json`** —
each checked field by field, with the check itself logged. Reactions and compounds are built
first; pathways next, because a pathway is a projection of the other two and can only be gold
once they are; and each compound's chemical **structure** (`smiles`) last, resolved once and
propagated into all three, because it needs the corrected names.

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

    PHASE 4 — the ROUTES: which reactions chain into each synthetic route?
      9  pathways-identification   read the patent, count the distinct routes
      10 pathways-reconciliation   match against pathways.json, flag
      11 pathways-gold-check       re-project from the gold, merge routes, decide

    PHASE 5 — the STRUCTURES: what molecule is each compound?
      12-13 smiles-structures   Phase A resolve a structure from many sources (record what
                each gave), then Phase B verify every structure through a stack of checks, decide

    PHASE 6 — the ADVERSARIAL RE-CHECK: can a fresh reader prove it wrong?
      14 adversarial-independent-audit   fresh no-context agents hunt for what was missed

Identification passes (1, 3, 9) never open the extraction output — that independence is
their whole value. Reconciliation (2, 4, 10) matches **by identity, never by number**, and
only flags. Verification (5, 6) checks one record at a time, re-reading the whole `.md` for
each. The gold checks (7, 8, 11, 13) assume nothing, re-derive every field, and are the only
passes that correct as they go and end with a plain yes/no. The structures passes (12, 13)
are deterministic, not independence passes: resolution queries every source and records what
each returned, and the gold check trusts a structure by how many independent sources agree.

**Pathways are a peer artifact, not an afterthought.** A pathway is a projection: its steps
are the gold reactions and its KSM/intermediates/product are gold compounds, so
`pathways.json` can only be gold after both of those are (Prompts 7–8) — which is why it is
built third, in Prompts 9–11, mirroring the same identify → reconcile → gold-check shape.
The gold check re-projects the step content from the gold before judging the route-specific
fields (linkage, KSM choice, overall yield, tags).

**Structures are a field, not a fourth file.** `smiles` lives in all three: one per compound
in `compounds.json`, as `reactant_smiles` / `product_smiles` / `canonical_rxn` in
`reactions.json`, and as a slot on each route's KSM / intermediates / product in
`pathways.json`. Prompts 12–13 resolve it once per `compound_uuid` and propagate, so one
compound carries one structure everywhere. The resolver never types a structure from memory:
every gold SMILES comes from the patent's own drawing or a name resolver (OPSIN for
systematic names, PubChem for trivial/INN names, the agent only to translate or repair a
name), validated through RDKit. Because a structure is easy to get subtly wrong — the right
molecule under the wrong name, a salt for the parent, an element named as a radical — the
gold check (13) runs a stack of independent checks (cross-source agreement, the patent
drawing, formula sanity, salt handling, one-structure-per-uuid), and a structure is gold only
when it survives all of them.

The adversarial pass (14) runs genuinely last, once all three files and their structures are
believed gold. It
hands each finished file — `reactions.json`, `compounds.json` **and** `pathways.json` — to a
separate fresh agent with no context and asks them to prove the gold wrong, then propagates
any reaction/compound fix back into the pathway steps that project from it. It has found real
defects on files that passed the gold checks.

Phases 1–2 flag and stop; fixing happens only after review. That order is what stops a bad
correction going into the ground truth.

## The audit folder

Every finished patent carries an `audit/` folder holding **exactly five files** in the end
(no merge ledgers, no separate verdicts file, no README, no `.bak`; a reading pass may write
a transient `_*` working ledger while it runs, but deletes it once its result is committed):

    audit/
      reactions_audit.csv   field-by-field check of reactions.json
      compounds_audit.csv   field-by-field check of compounds.json
      pathways_audit.csv    field-by-field check of pathways.json
      smiles_audit.csv      per-source structure resolution, one row per compound × source
      findings.csv          every finding and its verdict, one sheet

**The three record `*_audit.csv` sheets (reactions, compounds, pathways) are field-level** —
one row per field checked, passes included; `smiles_audit.csv` is per-source, one row per
compound × source attempted, so the reader sees what every source returned. An `OK` row is
evidence the check was made; a sheet that lists only failures
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
    smiles_audit.csv:    #, Compound, Source, Query, Result, SMILES, InChIKey skeleton, Chosen,
      Confirmed by drawing, Confidence, Status, "Problem, in plain words"

**`findings.csv` is the single findings sheet** — log and verdict together. The flagging
passes (2, 4, 10, 12) append one row per missing/extra/wrong/duplicate record, verdict left
open. The gold checks (7, 8, 11), the structures gold check (13) and the adversarial pass
(14) fill the verdict on every row
and add a row for each `MISSING`/`WRONG` audit row — none skipped. The file is not gold
while any verdict says `STILL TRUE`. Columns:

    #, Raised in pass, File, Line (.md), Finding, Status when raised, Verdict, Resolution / evidence

- **Status when raised:** `Missing`, `Extra`, `Wrong`, `Duplicate`, `Definitional`, `OBSERVATION`.
- **Verdict:** `NO LONGER TRUE`, `STILL TRUE` (say what blocks it), `NEVER TRUE` (say why).
- **Resolution / evidence** points at the current file and the patent — field, value, line —
  so the verdict is checkable from its own row.

**Dedup is recorded as findings, not a ledger.** The pipeline stores one reaction (and one
pathway) per section × step, so the same transformation or route is restated across Claims,
Summary and Scheme. Prompts 2 and 10 flag those as `Duplicate`; Prompts 7 and 11
collapse each group to the richest record, unioning any non-null field a duplicate carried,
and close it as a `Duplicate → NO LONGER TRUE` row naming the survivor and what folded into
it. **Never write a `merged_from` field** onto a record; keep `source_sections`. A genuinely
different run (a conflicting temperature, reagent or yield) or a distinct route's step is
never merged.

## Big patents: chunk and checkpoint

A large patent (the biggest here is ~1,300 lines / ~130K tokens, and a heavy US/EP case runs
larger) will not stay sharp in one context, and "read the whole `.md` end to end" degrades
in the middle and breaks outright once the file no longer fits. Every reading pass therefore
works the same way, and the fix doubles as crash-recovery:

- **Chunk by section, never by line count.** The `.md` is segmented by
  `<!-- page ... :: <section_type> ... -->` markers and named sections (Claims, Summary,
  Background, each Example, each Scheme). A section boundary never cuts a reaction in half.
  Identification passes read section by section; verification and gold passes read a
  record's own section plus the sections it cross-references, not the whole file per record.
- **The ledger lives on disk, and is the resume point.** For verification/gold passes the
  ledger *is* the `*_audit.csv`, flushed one row per field as it goes; for identification
  passes it is a transient `audit/_*-identification.md`, deleted once the count is committed.
  Either way, an interrupted pass reopens its ledger, finds the last row or section it
  completed, and continues from the next — never restarts from record 0.
- **A done-marker per section plus one final whole-file sweep** guarantee coverage: nothing
  is skipped just because the read was chunked or resumed.


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
