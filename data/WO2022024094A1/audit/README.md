# Audit trail: WO2022024094A1

Everything behind the gold file in `../output/`. Read this to see what was checked,
what was found, and what was decided.

| file | what it is |
|---|---|
| `GOLDEN-DATASET-FINDINGS.xlsx` | the findings log, five tabs |
| `RUN-NOTES.md` | how the extraction was produced, every fix, and by which model |
| `reactions-read-from-patent.md` | the 19 reactions read from the patent, independently, before reactions.json was opened |
| `reactions-provenance.json` | per-record source lines, quoted evidence, arithmetic and drawing checks |

## The findings log

    Reactions   30 findings   all closed
    Compounds    1 finding    open, waiting for the compounds pass

## The headline numbers

    reactions   the patent describes 19 transformations; the extraction found 18
                the miss was the in-situ RuO2 catalyst preparation at lines 187 and 192
                a 20th, the implied NMSBA to NMSBC step of CN106565561A at line 88,
                was missed by the extraction AND by the independent read, and added later
    coverage    118 of 118 numeric tokens in the patent's chemistry lines are in a field
                68 of 68 quantity values verified verbatim against their cited span
                92 of 92 reaction-bearing lines fall inside some record's cited span
                37 all-null fields tested against the patent: 36 correctly silent, 1 real gap

Nothing was invented by the extraction.

## The one real chemistry error, and what it cost

Three route records listed the patent's ALTERNATIVE reagents inside `compounds[]`:
three oxidants on one record, seven bases on another. That asserts they are all
charged to the same flask. Alternatives now sit in `reagent_alternatives` with the
sentence they come from, plus a plain-language `alternatives_note`, and only the
reagent actually used appears in `compounds[]`.

An intermediate attempt split each alternative into its own record, taking the file
to 28. That was reverted: the alternatives come from one sentence each with no
condition, yield or purity attached, so they do not earn records. A separate record
is earned only where the patent gives different conditions, yields or purities.

## Fields added beyond the shipped schema

`reactions.json` deliberately carries fields `pipeline/schemas/reactions.schema.json`
does not define, because the patent states things the schema cannot hold:

    time_h_min / time_h_max          "6-8 hours" against a scalar time_h
    time_h_tiers                     three nested tiers at line 194
    ph_target_min / ph_target_max    "pH < 3" was stored as ph_target 3.0
    product_purity_pct_min / _max    ">95%" was stored as an assayed 95.0
    temperature_stages               one object cannot hold reflux, then 25-30, then 5-10
    stirring_stated                  every Example stirs; the enum has no "type unstated"
    solvent_recovery_pct_min / _max  ">85%" stated five times, held nowhere
    catalyst_recovery                filtration, reuse, and the ">50 conversion" bound
    catalyst_alternatives            RuO2 / RuO2 hydrate / RuCl3 are forms of supply
    catalytic_cycle_species          ruthenate, perruthenate, RuO4 are not charged
    reagent_alternatives             the alternatives, kept out of compounds[]
    alternatives_note                the same, in plain language
    product_observed                 melting point and appearance of each batch
    workup.pressure_operations       seven statements of reduced pressure or vacuum

The naming of the min/max pairs follows CN112645853A, which already carries
`time_h_min`, `time_h_max`, `ph_target_min` and `ph_target_max`.

## Three caveats that travel with this data

1. **A human directed it, and did not read every line.** Every judgement call in this
   run was made by the reviewer, not the model: what counts as one reaction against
   several, whether alternatives earn their own records, whether the repo schema or the
   gold is the authority, whether generic class terms belong in the compounds, how
   multiple charges are stored, and which readings to keep where the patent says two
   things. Several model decisions were overturned on those calls, including an
   eight-record split that was reverted. What was NOT done is a line-by-line human read
   of the patent against the finished files.
2. **The extraction, the independent read and the audit were all Claude.** Where the
   model misread something and the audit read it the same way, nothing in this process
   can see it. This is not hypothetical here: the implied step at line 88 was missed by
   the extraction AND by the independent read, and only surfaced on a later sweep.
3. **The final gold checks shared context with the fixes.** Prompts 6 and 7 ran in the
   same session that made every edit, so they could see their own reasoning. They did
   catch real defects, including seven wrong provenance lines and one false flag, but a
   clean result from them is weaker evidence than a fresh-context audit would be.


## What the record count depends on

    7   worked examples          beyond dispute; the patent performs 7 experiments
    9   prior-art background     a convention: many pipelines exclude background
    3   invention route          the patent describes 3 transformations
    1   catalyst preparation     borderline: prophetic, "can be formed", never performed

Only the 7 Example records are uncontestable. The other 13 depend on conventions
chosen during this run, and those conventions must ship with the data if it is ever
used to score a pipeline, or a scorer measures convention-agreement instead of
extraction quality.
