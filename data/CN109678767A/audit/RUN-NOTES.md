# CN109678767A run notes

Status: BOTH HALVES VERIFIED GOLD (2026-09-01).
- reactions.json: prompt 7 final check complete (see below).
- compounds.json: prompts 3, 4, 6 AND 8 complete. Prompt 8 (final re-derivation pass)
  found the value layer clean (all 114 batch entries + every base quantity verified,
  0 mismatches) and fixed 15 records in the generated/derived layer: two wrong section
  anchors (bromine, sodium methoxide -> Background), five merge-stale section_types,
  the acetone cyanohydrin role (reagent -> catalyst, per its own claim-8 note and the
  reactions gold), a stray catalyst class tag on the DMF solvent twin, and six
  identifier_type labels (common names typed iupac while holding their true IUPAC
  names as aliases). C-010 synonym aliases stand by annotator ruling. GOLD.
  Details in AUDIT-LOG.md Prompt 8 section and scratchpad prompt8-record-log.md.
  Earlier prompts: independent blind read by a fresh agent
  (58 identities; borderline rulings by annotator), reconciliation 58/58 matched with
  0 missing and 0 extra, record-by-record verification with findings C-001..C-010 all
  dispositioned: duplicate section records merged for 6 substances (DMF and
  dichloroethane twins kept split by ruling: real dual role; deliberate isomer
  uncertainty), four uncaptured comparative charges filled (bromine, sodium methoxide,
  acetonitrile, ethanol), tembotrione's Example 4 batch completed (38.1 g / 85.4% /
  98.7%), DMF solvent-twin purity nulled, TFE/DABCO misprinted pairs kept as printed
  with explicit contradiction notes, pipeline synonyms kept by ruling, and per-batch
  arrays (quantities_by_section / yields_by_section / appearances, 114 entries across
  20 records) added per the WO2022024094A1 convention. Final gate: 58/58 coverage,
  zero unprinted numbers, all quotes verbatim, no phantom measurements, all reaction
  references resolvable. Details in audit/compounds-read-from-patent.md and
  GOLDEN-DATASET-FINDINGS.xlsx (Compounds tab).

Prompt 7 (final re-derivation pass) found the value layer clean and fixed defects in
the generated/derived layer only: one stretched named_reaction (Ex4 Step 1
Wohl-Ziegler -> null), one inherited confidence (Comp 3a high -> medium), one
reactant_names omission (Background 5 enol ester), two role wobbles (HBr reagent ->
reactant on Claims/Ex3 Step 1), and — the largest find — the 29 split-stage records
still carried their pre-split parents' tag sets verbatim (is_one_pot:true vs field
false, analytics/purification/workup/solvent/safety/step_role tags describing other
stages); all re-derived at stage level. byproduct_recovery shape unified to [].
The saponification naming question was RESOLVED at user direction (2026-09-01):
named_reaction = "saponification" now set on all six ester-hydrolysis (2b) records,
matching gold CN112645853A; the contradicting parent-note clause is superseded by
appended notes. No open items remain — reactions.json is GOLD, unconditionally.
Details in AUDIT-LOG.md Prompt 7 sections and the session scratchpad
prompt7-record-log.md.

## What the patent is
Synthesis process for the herbicide tembotrione. One 3-step route (bromination,
etherification + hydrolysis, acid chloride + condensation), run in 4 examples plus a
prior-art comparative example; 10 prior-art reactions described in the background.

## Shape of reactions.json
45 records at TRANSFORMATION level, an annotator decision that deviates from the
per-procedure one-pot convention of the reference run CN104292137A:
- 29 performed transformations (Examples 1-4: 6 each; Comparative: 5; every telescoped
  one-pot step split into its chemical stages, intermediates as structured entities
  with no yields because the patent isolates nothing between stages)
- 6 described route transformations (Claims records, carrying the claim 2-10 ladders;
  the Summary prose twins were verified field-identical and removed; the 5 drawn scheme
  arrows were merged into the Claims records as SCHEME EVIDENCE notes and removed)
- 10 prior-art reactions from the background, one per document x reagent system
Fields added by annotator direction during verification: isolated_product_appearance
(16 records), quantity.equivalents_min/equivalents_max (claim ratio ranges, 15 values),
conditions.time_text (the literal "overnight" on Comparative_Step 2a).

## Verification done
- Prompt 1 independent read (before any extraction output was opened), full line-by-line;
  counts grounded in line numbers.
- Prompt 2 reconciliation by identity: matched 45/45, missing 0, extra 0.
- Prompt 5 record-by-record verification: zero value errors; every printed charge,
  assay, volume, temperature, time, yield, purity and pH matches its record; every
  procedure_text and quote verbatim (two genuine page-break joins at lines 188+193 and
  267+272). Findings F-001..F-006 in audit/GOLDEN-DATASET-FINDINGS.xlsx: 4 fixed,
  F-003 declined by annotator (flask volumes stay in notes), F-006 no change needed.

## Patent defects carried as printed (never repaired)
- Example 4 step 2: 1.13 g / 0.2 mol trifluoroethanol pair is impossible (~20 g would
  be 0.2 mol); flagged molar_mass_inconsistent + mass_balance_implausible.
- Triethylenediamine mass/mole pairs fail in Examples 2 (1 g / 0.008 mol),
  3 (0.226 g / 0.001 mol) and 4 (2.26 g / 0.01 mol); flagged as printed.
- Line 313 misprint 整出 for 蒸出, noted in the source enrichment.

## Known staleness, deliberate
reactions-provenance.json, pathways.json and chemistry-rollup.json in the repo run
directory still reference the pre-split record ids and the old count of 36; the
annotator directed that only reactions.json be edited. They are not copied here.

## Provenance of model-written artifacts
- Enrichment/vision/extraction passes: produced by the repo pipeline (see repo run).
- Independent reads (prompts 1 and 3 pattern), reconciliation, record verification,
  the transformation-level split, the dedup and all hand-edits to reactions.json:
  Claude (model claude-fable-5), directed and reviewed by the annotator, 2026-09-01.
  The reads and the extraction are not independent in the usual sense: where both
  missed the same thing, nothing here can see it.
