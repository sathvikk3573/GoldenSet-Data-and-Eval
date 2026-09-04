# Prompt 2: reconcile your reaction list against reactions.json

Only after Prompt 1 is finished and the count is written down.

---

You now have your own list of the reactions this patent describes, read from the
`.md` alone. Open `output/reactions.json` and reconcile the two.

**Do not trust `reactions.json`.** It is the thing being tested. Your job is to prove
whether it is missing something, has invented something, or has recorded something
wrongly.

## How to reconcile

**By identity, never by number.** Two files can both hold 54 records and disagree
about half of them. For every record, ask which of your transformations it is. Match
on what reacts, what forms, and which section it came from, not on the count and not
on the label.

Work **one record at a time**. For each one:

1. Read what the record says it is.
2. Go to the source it cites and read that passage in the `.md`.
3. Then read the whole `.md` again with that record in mind. Do not rely only on the
   cited lines. A record's evidence is often spread across sections.
4. Decide: is this a real transformation the patent describes, and is it the one the
   record claims to be?

## What you are looking for

- **In the patent but not in the file.** A missing reaction. Say where it is, quote
  the line, and say why you are confident it is genuine.
- **In the file but not in the patent.** A reaction the extraction invented, or one it
  built from a passage that does not describe a real transformation. Flag it as a
  false record rather than assuming you missed it.
- **Present in both but wrong.** Wrong product, wrong reactants, wrong section, two
  transformations collapsed into one, or one split into two.
- **A difference you cannot resolve.** Record both readings. Do not average them away.

## Before you flag anything

- **Check the twin.** Where one step is recorded twice, once from the claims and once
  from the summary, the two records cover each other. A value present on one is not
  missing from the other. Read the pair together, never alone. This holds everywhere in
  the gold, for reactions and compounds alike.

## Duplicate restatements: flag them to merge

The twin rule above stops you calling a restated step "missing". Its counterpart is
this: when the SAME transformation is recorded as several records across Claims,
Summary, Example and Scheme sections, that is not one finding per section — it is one
real reaction carried by several records, and the file should hold **one** record for
it, not many. Flag the extras as duplicates to be merged (the merge itself happens in
Prompt 7, never here).

Decide duplicate-vs-distinct by the same rule the gold uses everywhere:

1. **Same transformation** = same product and same reactant set after normalising names
   (lowercase; `methanesulfonyl` == `methylsulfonyl`; ignore spaces/hyphens/parens;
   trust `product_smiles` / `canonical_rxn` when present). A different product, a
   genuinely different mechanism to the same product, or a different route's step (e.g. a
   thioether variant) is a DISTINCT transformation — keep it separate.
2. **Same conditions signature, or one a strict generic subset of the other** (a Claims
   or Summary restatement that only adds a molar-ratio range and omits the yields, masses
   and workup an Example gives) → SAME reaction → mark the generic ones as duplicates of
   the richest record. **Any conflicting specified value** (a different temperature,
   reagent or yield = a genuinely different run) → KEEP both.
3. **Survivor = the richest record** (most non-null conditions/quantities/yield, usually
   the worked Example). Name it as the merge target.

For every duplicate, record a finding with Status `Duplicate`, the survivor's
`reaction_id`, and one line saying why it is a subset (no yield/mass, generic
conditions). These are what Prompt 7 collapses, keeping the survivor and unioning any
non-null field the duplicate has that the survivor lacks. The merge writes no
`merged_from` field and no separate ledger — it is closed as a `Duplicate` row in
`findings.csv`. (`source_sections`, the sections a record was drawn from, is kept.)

## What a field MEANS, versus what the pipeline did

Work out what a field is meant to hold before you judge it. Several field names do
not mean what they sound like. The pipeline's code and prompts are deliberately not
in this folder, so infer a field's meaning from its name, from how the rest of the
file uses it, and from the chemistry, and if it stays genuinely ambiguous, ask me
rather than guess.

**Never treat the pipeline's behaviour as correct.** It is the thing being measured.
If the patent states something and the extraction left it out, that is a finding,
whatever the pipeline was told to do. The gold has to be more complete than the
pipeline, not equally lossy.

## What to give me at the end

**Three tables. List every reaction individually. Do not group them, do not collapse a
range of rows into "records 10-17, all the same", and do not summarise a bucket as one
line. If there are 54 matches, show 54 rows.**

**Table 1: the matches, one row per reaction**

| # | your transformation | reactions.json reaction_id | match |
|---|---|---|---|
| 1 | ester + NaOH -> sodium salt, Example 1 | Example 1_Step 1 | exact |
| 2 | alkoxide prep, Example 2 | Example 2_Alkoxide Preparation | identity |

Mark each row `exact` where it is unmistakably the same record, or `identity` where you
had to match on the chemistry because the labelling differs, and say in the row what
differed.

**Table 2: in the patent but not in reactions.json, one row each, with a one-line
reason**

| # | transformation | line | why it was probably missed |
|---|---|---|---|
| 1 | methanol + benzylic bromide -> methoxymethyl impurity | 147 | described as an impurity mechanism, not as a numbered step |

**Table 3: in reactions.json but not in the patent, one row each**, with a one-line
verdict saying whether it is your miss or a false record, and why.

Then the four counts: matched, missing, extra, duplicate.

**Record every finding from Tables 2 and 3 in `audit/findings.csv`** (create the
`audit/` folder and the file if they do not exist), one row each, in the one findings
schema the whole suite shares:

    #, Raised in pass, File, Line (.md), Finding, Status when raised, Verdict,
      Resolution / evidence

where `Raised in pass` is `Prompt 2`, `File` is reactions.json, `Line (.md)` is the
line number in the `.md`, `Status when raised` is `Missing`, `Extra`, `Wrong` or
`Duplicate` (a section restatement to be merged — name the survivor's reaction_id in
the Finding), `Verdict` is left `STILL TRUE` for now, and `Resolution / evidence` is
left blank. The final gold check (Prompt 7) fills the verdict and resolution on every
one of these rows, so a finding that is not written down with a traceable row is a
finding that never gets closed.

Flag only. Do not change `reactions.json` until I have reviewed the flags and told you
to.
