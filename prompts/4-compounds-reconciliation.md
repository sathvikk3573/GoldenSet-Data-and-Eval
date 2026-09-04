# Prompt 4: reconcile your compound list against compounds.json

Only after Prompt 3 is finished and the list is written down.

---

You now have your own list of every compound the patent names. Open
`output/compounds.json` and reconcile the two, going through it **one record at a
time**.

## Step 1: what did they get that you got

Of the compounds you listed, how many are in `compounds.json`?

**Check identity by identity, not by exact name.** The same substance appears under
different names: `DMF` and `N,N-dimethylformamide`, `苄醇` and `benzyl alcohol`, an
original-language name and its English translation. Look at the `aliases` on each record, not
only the `identifier`. Two lists can hold the same chemistry and share almost no
strings.

Go through `compounds.json` manually, record by record. Do not match by string
comparison alone and call it done.

## Step 2: what is in the file that is not on your list

For each one, reason it out and say plainly which it is:

- **your miss** - the patent does name it and you did not catch it. Say how you missed
  it.
- **their error** - the file holds something the patent never names, or a name that is
  not a compound at all, such as a section title, a method name or a heading.

## Step 3: what is on your list that is not in the file

For each one, say whether it is a genuine extraction miss and how much it matters. A
generic class noun from the claims matters more than a herbicide named once in the
background.

## Before you flag anything

- **Check the twin.** Where the same thing is recorded twice from two sections, the two
  records cover each other. A value on one is not missing from the other.
- **One substance, one record.** The counterpart to the twin rule: `compounds.json`
  holds one record per substance, not one per section. If it carries two records for the
  same compound (the same substance under a Claims name and an Example name, an
  original-language name and its English gloss), that is a duplicate to merge, not two
  compounds. Flag it with Status `Duplicate`, name the survivor's `identifier`, and note
  that the merge (keep the richest record, union the other's aliases and any non-null
  field it carries) happens in Prompt 8, never here. Two genuinely different substances
  that merely share a name are NOT merged.

## What a field MEANS, versus what the pipeline did

Work out what a field is meant to hold before you judge it. Several field names do
not mean what they sound like. The pipeline's code and prompts are deliberately not
in this folder, so infer a field's meaning from its name, from how the rest of the
file uses it, and from the chemistry, and if it stays genuinely ambiguous, ask me
rather than guess.

**Never treat the pipeline's behaviour as correct.** It is the thing being measured.
If the patent names a compound and the extraction does not have it, that is a finding,
whatever the pipeline was told to do. The gold has to be more complete than the
pipeline, not equally lossy.

## What to give me at the end

**Three tables. List every compound individually. Do not group them, do not collapse a
range of rows into "10-17 all same", and do not summarise a bucket as one line. If
there are 43 matches, show 43 rows.**

**Table 1: the matches, one row per compound**

| # | your compound | compounds.json identifier | match |
|---|---|---|---|
| 1 | tert-butanol | tert-butanol | exact |
| 2 | DMF | N,N-dimethylformamide | identity |
| 3 | 苄醇 the benzylic alcohol | benzyl alcohol | identity |

Mark each row `exact` where the strings are the same, or `identity` where they are the
same substance under a different name, such as an abbreviation against its full name,
an original-language name against its English one, or a short form against a
systematic name.

**Table 2: missing from compounds.json, one row per compound, with a one-line reason**

| # | compound | line | why it was probably missed |
|---|---|---|---|
| 1 | alkali metal alkoxide | 47 | a generic class noun, though it is a reagent route in claim 1 |
| 2 | HPPD | 125 | an enzyme, not a chemical, named only in the background |

**Table 3: in compounds.json but not on your list, one row each**, with a one-line
verdict saying whether it is your miss or their error, and why.

Then the four counts: matched, missing, extra, duplicate.

**Record every finding from Tables 2 and 3 in `audit/findings.csv`** (create the
`audit/` folder and the file if they do not exist; append if Prompt 2 already started
it), one row each, in the one findings schema the whole suite shares:

    #, Raised in pass, File, Line (.md), Finding, Status when raised, Verdict,
      Resolution / evidence

where `Raised in pass` is `Prompt 4`, `File` is compounds.json, `Line (.md)` is the
line number in the `.md`, `Status when raised` is `Missing`, `Extra`, `Wrong` or
`Duplicate` (a same-substance record to be merged — name the survivor's identifier in
the Finding), `Verdict` is left `STILL TRUE` for now, and `Resolution / evidence` is
left blank. The final gold check (Prompt 8) fills the verdict and resolution on every
one of these rows, so a finding that is not written down with a traceable row is a
finding that never gets closed.

Flag only. Do not change `compounds.json` until I have reviewed the flags and told you
to.
