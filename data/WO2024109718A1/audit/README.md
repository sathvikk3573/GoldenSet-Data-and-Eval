# WO2024109718A1 - what is here and what is not

## Reactions and compounds

Both files are gold. The annotator read and accepted the reactions pass on 2026-09-02
and the compounds pass on 2026-09-03.

    reactions.json   69 records
    compounds.json  131 records, covering 122 distinct compounds

No identifier in either file is Chinese. Every Chinese string the patent prints is kept
as the first alias on its record, so the authoritative text survives and a reviewer is
never shown a name they cannot read. `identifier` is the only key joining the two files:
all 139 identifiers reactions.json references resolve to a compound record.

## What the gold holds

69 records, and the count itself is the first finding. The extraction produced 79; ten
were within-section retellings of a transformation already recorded, each verified as a
strict subset (no conditions, no workup, compound roster contained in its twin) before
being merged. What was NOT merged, and why, is on the Reactions sheet: twins across
sections stay separate because each rung of the claim/description/summary ladder holds
substance the others lack, the two prior-art routes stay separate because they come from
different cited documents, and the three route-convergence twins stay separate because
`precursor_step` is single-valued and route membership is data.

    22  background prior-art steps   two 11-step schemes, DE19846792A1 and CN200510030163.5
    10  claims
    10  summary of the invention
    12  detailed description
    15  performed runs               Examples 1-11; Ex 4 and Ex 9 have two methods, Ex 8 has three steps

## Coverage, checked mechanically

All 83 reaction-bearing schemes, all 166 drawn arrows, all 22 distinct transformations
and all 11 examples are represented. No reaction the patent describes is missing and no
record is unsupported by the page. A final independent reader confirmed this
separately.

## Ranges reach the JSON as two numbers

This was the largest loss class and it is fixed. CO at 1-4 MPa is
`pressure.value_kpa_min/max` = 1000/4000 on seven records; pH 2-3 is
`ph_value_min/max`; the 10-20 h at line 320 is `time_h_min/max`, where the reaction time
had been absent altogether; Example 11's "below 10%" is `product_yield_pct_max` with a
null minimum. The point-valued fields `value_kpa`, `ph_value`, `time_h` and
`product_yield_pct` are **deliberately null wherever the patent gives a range** - a
bound is not a measurement, and storing one as though it were would be fabrication.

Other fields added because the patent states something the schema could not hold:
`conditions.stages` (per-stage temperature and time for the three two-stage runs),
`process_control.analytical_method` and `nmr_solvent`, `compounds[].concentration_pct`,
`compounds[].alternative_group` (which marks HBr+H2O2 as ONE combined option, not two
reagents), `compounds[].is_genus` (claim genus terms the records had narrowed to
species), `one_pot_optional`, `molar_ratio_min/max`, and
`temperature.preferred_min_c/max_c`.

## Empty fields are empty on purpose

Every remaining null was justified by searching the Chinese, not assumed. 纯度 (purity)
appears zero times in the document, so `product_purity_pct` is null on all 69. 搅拌
(stirred) appears at exactly four lines, 氮气置换 at six, 四口/高压釜 at twelve, cooling
terms at two - and in each case exactly those records carry the field.

## Caveats, both of which matter

1. **The annotator has read this.** Every automated check is listed above, and the
   annotator read the finished file and the findings, directed every judgement call - what
   to merge, what to keep, which findings were in scope, whether preference belongs in
   prose or in a field - and accepted the result on 2026-09-02. What has NOT happened is a
   line-by-line human re-derivation of all 69 records from the page independently of the
   model that produced them.

2. **The extraction and the audit were both Claude.** The extraction under test was
   Claude Opus 4.5. This gold pass, its six section readers and the final independent
   auditor were all Claude Fable 5, and all of them read the same enriched markdown.
   Where the markdown itself is wrong, or where a fact is absent from it, nothing in this
   process can see it. The one structural mitigation: the reaction list in
   `reactions-read-from-patent.md` was produced from the markdown alone, before
   `reactions.json` was opened, so the count was not derived from the thing it measures.

**Four of the last eight defects were introduced by this annotation, not by the
extraction** - two over-broad "about" markers, an analytical method assigned by section
adjacency rather than by what the section prints, and a skipped concentration. They were
caught by the final independent reader and are logged as mine on the Reactions sheet.
A fixing pass introduces defects too, which is the argument for the independent gate.

## Files

    input/   the source PDF and the line-numbered translated markdown
    output/  reactions.json  - the gold
             compounds.json  - the gold
    audit/   GOLDEN-DATASET-FINDINGS.xlsx   76 findings: 46 on reactions (43 fixed, 3
                                            closed by decision) and 30 on compounds
                                            (25 fixed, 5 closed by decision)
             reactions-read-from-patent.md  the independent reaction list, written before
                                            reactions.json was opened
             AUDIT-LOG.md                   the per-record verification log
             RUN-NOTES.md                   the full run notes, including the vision pass
                                            that rendered the product name four ways, one
                                            of them a different herbicide
             reactions-provenance.json      source lines, quotes and drawing evidence

Provenance note: `reactions-provenance.json` carries two known prose errors that were
NOT corrected, by instruction - a `drawing_evidence` entry claiming an arrow is absent
from the consolidated scheme when it is step 6 of the seven-arrow schemes at lines
407/416, and an `arithmetic_check` calling Example 5's charges an excess when they are
1.004 and 1.048 equivalents. Both are corrected in the `notes` of the affected records
in `reactions.json`, which is the file that matters.

## The schema was extended, on purpose

The original schema sets `additionalProperties: false` and declares none of the fields the
fixes added, so the file would have failed validation against it. Rather than drop the
data to fit the contract, the contract was extended: `reactions.schema.json`, kept in the source repo,
declares all of it and `reactions.json` validates against it with zero errors. Each new
field carries a description saying what it holds and why the original field could not.

The added fields are the paired range bounds (`*_min` / `*_max` for time, pH, pressure,
yield and molar ratio, plus `temperature.preferred_min_c/max_c` for a nested preferred
tier), `conditions.stages` for multi-stage one-pot runs, `workup.ph_intermediate_*` and
`post_precipitation_hold_h`, `process_control.nmr_solvent` and `analytical_method`,
`one_pot_optional`, and on each compound `concentration_pct`, `alternative_group` and
`is_genus`.

`stirring.type` gained the value `stirred`, meaning the patent says 搅拌 without naming a
mechanism. This is not a new convention: the CN109678767A gold run in this same folder
made the identical call and uses the same value.

## One thing deliberately not pursued

The `约` (about) markers on times, temperatures and pressures are recorded where they were
already found and verified against the page, but no further sweep was made for them. The
annotator judged the return too low. So treat `*_approximate` as present-where-noticed
rather than exhaustive; the underlying values are exact as printed either way.

## The compounds pass

The extraction delivered 137 records covering 103 distinct substances. The gold holds 131
records covering 122. The record count fell and the substance count rose because the
extraction wrote up to five records for one substance, once per naming form, and held none
of the reagent classes the claims are written in terms of.

    137  as extracted
    +21  generic class terms the patent states and the extraction did not record
    158
    -27  duplicate records merged losslessly
    131  gold

Nine substances stay split across records because the patent gives them a different role
or a different charge in different sections: PdCl2 is charged three ways, methanol is a
reagent in the Background and a solvent in the Examples. Merging those would destroy a
fact. The consequence is that 15 identifiers resolve to more than one record; disambiguate
on `role` plus `section_label`.

**Molecular formula is never an identity key in this run.** Compound (VIII) is the enol
ester and tembotrione is the C-acyl ketone. They are isomers, both C17H16ClF3O6S, and a
formula-based grouper merged them during this pass before the error was caught. The patent
has 18 distinct structures and only 17 distinct formulas. Both records carry distinct
InChIKeys and a warning.

### Content verification

Every value in compounds.json was re-checked against the page: masses, volumes, yields,
concentrations, all five melting points, all eight NMR records and every quotation field.
Zero failures. Fields left null were checked to be genuine absences: the patent states no
boiling point, no mass spectrum, no purity, no density, no equivalents and no molar
amounts anywhere.

`concentration_pct` was added because the danger it guards against lives in this file.
`mass_g` 1165 on hydrochloric acid is 1165 g of a 5.1% solution, about 59 g of HCl, and a
reader of compounds.json alone would otherwise have no way to know. Six reagents are
charged as solutions: HCl 5.1%, NaNO2 20%, CH3SNa 20%, NaOH 32%, H2O2 23%, NaH 60%.

Per-Example charges and yields are NOT duplicated here. reactions.json already records all
68 charges and all 7 yields, per reaction step, with role and concentration. compounds.json
holds the first value the patent states and points at reactions.json for the rest.

### Two things to carry with the compounds gold

1. **C-015 is a judgement call.** The patent prints the identical string 式(A)所示的化合物
   in both the Summary and the Claims, so once Chinese identifiers were dropped there was
   no second English name to tell two records apart. They were merged, keeping the Claims
   reading `product` over the Summary reading `other`, with the disagreement written into
   the surviving record's notes.
2. **`commercially_available` is general knowledge, not a reading of the patent.** The
   patent never states availability for anything. True for ordinary catalogue chemicals,
   false where the patent or its cited prior art makes the compound, null for class terms
   and placeholders. The one case the page does support is tembotrione: L61 says its market
   has expanded since launch.
