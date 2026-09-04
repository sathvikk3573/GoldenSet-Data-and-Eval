# The gold set

One folder per patent:

    <PATENT_ID>/
      input/    the source PDF and the translated line-numbered markdown
      output/   reactions.json, compounds.json, pathways.json  <- the GOLD
      audit/    AUDIT-LOG.md, RUN-NOTES.md, the independent read-from-patent lists,
                and GOLDEN-DATASET-FINDINGS.xlsx for that run

Every value traces to a line in the numbered markdown in `input/`, and every finding in
the audit was recorded against a line number.

The `output/` JSON follows the pipeline's shapes but **deliberately extends them**: where
a patent states something the pipeline schema cannot hold, the field was added to the gold
rather than the value dropped. The three patterns are range pairs (`_min`/`_max`, so ">95%"
is a lower bound rather than a value of 95), per-section arrays (a substance charged in
twenty examples has twenty quantities), and structure the schema flattened (multi-stage
conditions, alternative reagent groups, the analytical method in full). Anything validating
this data against the stock pipeline schema will read those additions as errors.

## CN109678767A

Zhejiang Zhongshan Chemical Industry Group, published 2019.04.26. A route to
tembotrione whose comparative examples are the point: the patent runs its own chemistry
against the prior art side by side and reports both.

- **reactions.json** — 45 records. Passed the Prompt 7 final re-derivation. The value
  layer came back clean; the fixes were all in the generated layer, the largest being 29
  split-stage records still carrying their pre-split parents' tag sets verbatim, all
  re-derived at stage level.
- **compounds.json** — 58 records. Prompts 3, 4, 6 and 8 all complete. Prompt 8 verified
  every one of the 114 per-batch entries and every base quantity with zero mismatches,
  and corrected 15 records in the generated layer: two section anchors, five stale
  section_types, the acetone cyanohydrin role (reagent to catalyst), a stray tag on the
  DMF solvent twin, and six identifier_type labels.
- Per-batch arrays (quantities_by_section, yields_by_section, appearances) follow the
  WO2022024094A1 convention: 114 entries across 20 records.
- Two pairs of twins are deliberately kept split, DMF and dichloroethane, because each
  genuinely plays two roles. Two misprinted TFE/DABCO pairs are kept exactly as printed
  with the contradiction written into the notes rather than silently corrected.

## CN112645853A

Jiangxi Tianyu Chemical, published 2021.04.13. A two-step route to
2-chloro-3-(alkoxymethyl)-4-(methanesulfonyl)benzoic acid, made two ways: the
tembotrione intermediate (Examples 1–10) and the tefuryltrione intermediate
(Examples 11–20).

- **reactions.json** — 55 records. 3 distinct transformations told 55 times: once per
  worked example, plus the claims, the summary and the prior art. Every mass, mole,
  temperature, time and yield traces to a line in the markdown. Where the patent's
  own arithmetic fails, all six cases are flagged rather than smoothed.
- **compounds.json** — 56 records. Every compound the patent names, including the
  class nouns it uses without naming a substance. Nothing invented; every quantity is
  a genuine pair from one real experiment; every analytics quotation is verbatim.

## WO2022024094A1

Rallis India, published 2022.02.03. A three-step route to mesotrione whose point is
deleting a step: the prior art goes acid to acid chloride to enol ester, this patent
couples the acid straight to 1,3-cyclohexanedione with DCC, and swaps a nitric-acid
oxidation for sodium hypochlorite over a ruthenium oxide catalyst.

- **reactions.json** — 20 records: 7 worked examples, 9 prior-art processes from five
  cited patents, the 3 invention route steps, and the in-situ preparation of the
  catalyst. Every number the patent prints in a chemistry line is in a field, and
  every quantity traces to a line. Ranges are stored as min and max, and open bounds
  keep the side they have: ">95%" is a lower bound of 95 with no upper bound, and
  "pH < 3" is an upper bound of 3.
- **compounds.json** — 52 records: 39 named substances, 12 generic class terms and one
  enzyme. The class nouns are the words the claims recite, "an oxidant", "a base", "a
  solvent", and the extraction had recorded none of them. Every charge the patent states
  is held per example, because most substances are charged three or four times and one
  quantity field cannot represent that.
- All 23 pages are scans with no text layer, so every character came from a vision
  pass, and five pages are marked medium confidence.
- This run differs from the others on both caveats and carries its own in
  `WO2022024094A1/audit/README.md`: the reviewer made every judgement call and
  overturned several model decisions, but did not read the patent line by line against
  the finished files.

## WO2024109718A1

Lansheng Biotechnology Group and Hebei University of Science and Technology, published
2024.05.30, priority CN 2022-11-22. A nine-step route to **tembotrione** that replaces the
prior art's Friedel–Crafts acetylation and sodium-hypochlorite haloform sequence with a
bromination and a palladium-catalysed carbonylation, cutting eleven steps to nine.

- **reactions.json** — 69 records: 22 prior-art steps from two cited documents, 10 claims,
  10 summary, 12 description, and 15 performed runs from Examples 1–11. Coverage was
  checked mechanically and confirmed by an independent reader: all 83 reaction-bearing
  schemes, all 166 drawn arrows and all 22 distinct transformations are represented.
  Ranges are stored as min and max with the point field left null, so CO at 1–4 MPa is
  1000–4000 kPa and Example 11's "below 10%" is an upper bound with no lower bound rather
  than a yield of 10. Fields were added where the patent states something the schema could
  not hold: per-stage conditions for the three two-stage runs, the HPLC method and NMR
  solvent, per-reagent concentrations, claim genus terms, and an alternative-group marker
  that keeps HBr+H2O2 as one combined brominating option instead of two reagents.
- **compounds.json** — 131 records covering 122 distinct compounds. The extraction's 137
  records covered only 103 substances: it wrote up to five records for one compound, once
  per naming form, and held none of the reagent classes the claims are written in terms of.
  21 class terms were added, each verified against the line it came from, and 27 duplicates
  merged losslessly. Every value was re-checked against the page with zero failures, and
  `concentration_pct` was added so that 1165 g of 5.1% hydrochloric acid cannot be read as
  1165 g of HCl. Molecular formula is never an identity key here: compound (VIII) and
  tembotrione are isomers sharing C17H16ClF3O6S, and a formula-based grouper merged them
  during the pass before the error was caught.
- No identifier in either file is Chinese; the Chinese the patent prints is kept as an alias.
- The patent is 45 pages of Chinese with **no text layer at all**, so every character came
  from a vision pass. That pass rendered the product name four different ways and one of
  them, sulcotrione, is a different registered herbicide; all twelve wrong renderings were
  corrected before annotation. The run notes carry the detail.
- Four of the last eight defects found were introduced by the fixing pass itself, not by
  the extraction, and were caught only by a final independent reader. They are logged as
  such.
- The fields added to stop range and multi-stage data being dropped are the largest set
  in the repo: per-stage conditions, molar-ratio and yield range pairs, the full HPLC
  method, genus and alternative-group markers on compounds. The principle the annotator
  set: extend the contract rather than lose the data.
- The annotator read and accepted this entry, so it does not carry caveat 1 in the root
  README.

## Conventions that look like defects but are not

These are deliberate. Do not "fix" them.

- **Aliases need not be patent-literal.** English synonyms, IUPAC names for drawn
  structures and `reactions.json` join keys are kept on purpose. Reactions join to
  compounds on identifier **or** alias.
- **Some records are deliberately split.** Where `reactions.json` references two
  different strings for the same substance, or a substance genuinely plays two roles,
  the compound is held as two records.
- **`commercially_available`** is true for an ordinary catalogue chemical even when the
  patent also makes it, false when made and not catalogue, null for class terms and
  placeholders.
- **`first_seen_line`** is the first line in the original-language description body;
  front-page and translation lines do not count.
- **Molecular formula is never an identity key.** Isomers share one.
- **Identifiers stay patent-literal.** Where the patent misprints a name, the printed
  form is kept and the correct chemistry goes in the notes and aliases.
- **Contradictions in the patent are recorded, not smoothed.** Failed arithmetic and
  misprinted pairs are kept as printed with the contradiction written into the notes.

## The structure layer

A structure pass on 2026-09-04 added four fields to every compound record, and to the
compound entries inside `reactions.json`:

    smiles              the structure, or null
    smiles_source       how it was arrived at
    molecular_formula
    molecular_weight

`smiles_source` is the point of the layer: it says whether the structure came from the
name (`opsin`, `pubchem`), from a drawing in the patent (`patent_scheme`,
`patent_drawing`), or was set by hand (`curated`), and where there is no structure it
says why rather than leaving a bare null — `none:class_name` for "a base",
`none:markush` for a generic formula, `none:reference`, `none:polymer`,
`none:ambiguous`, `none:unresolved`.

| patent | compounds | with a structure |
|---|---|---|
| CN109678767A | 58 | 50 |
| CN112645853A | 56 | 38 |
| WO2022024094A1 | 52 | 36 |
| WO2024109718A1 | 131 | 104 |

The records without one are overwhelmingly the class terms, which is correct: "an
oxidant" has no structure.

## What is NOT here, and why

**structures-resolved.json is deliberately excluded.** It carries a wrong structure:
`benzyl alcohol` is resolved to plain C7H8O, MW 108.14, where the patent means the
substrate's benzylic alcohol, C9H9ClO5S, MW 264.68. The patent's own drawn dimer
proves it — two substrate units minus water is 511.34, matching the drawn
C18H16Cl2O9S2 at 511.36, whereas two plain benzyl alcohols would give 198.26. Until
that is fixed, joining compounds to structures inherits a wrong molecule.

## Findings log

The full audit — what was flagged, what was fixed, what was withdrawn and why — is in
`GOLDEN-DATASET-FINDINGS.xlsx` in each patent's `audit/` folder, with a Reactions half
and a Compounds half. Ids are unprefixed and match that run's own `AUDIT-LOG.md` and
`RUN-NOTES.md`: F- for reactions, C- for compounds.

**This is the old audit layout.** These four runs predate the current prompt set;
[../prompts/README.md](../prompts/README.md) now mandates exactly four CSVs per patent
(`reactions_audit.csv`, `compounds_audit.csv`, `pathways_audit.csv`, `findings.csv`) and
no `.md` logs, no workbook. The existing folders are kept as they were rather than
retrofitted, because the log is the record of how the run actually went.
