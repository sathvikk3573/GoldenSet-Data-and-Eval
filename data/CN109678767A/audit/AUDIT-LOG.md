# CN109678767A - Pass 1 running audit (reactions identification)

Source read: input/CN109678767A-enriched-numbered.md, all 314 lines, in order.
Extraction output NOT opened (reactions.json, compounds.json untouched in this pass).
This audit produced by: Claude (model claude-fable-5), 2026-09-01.

## Document map
- p01 front page: lines 4-43 (abstract line 40-41)
- p02 claims 1-10: lines 45-78
- p03-p05 description: background [0002]-[0009] lines 90-108; summary [0010]-[0027] lines 111-149; scheme [0028] line 150-152
- p06-p11 examples: Ex1 172-201, Ex2 202-229, Ex3 230-257, Ex4 258-287, Comparative 288-313, closing remark [0090] 314

## Compound letters used below
- A = methyl 2-chloro-3-methyl-4-methylsulfonylbenzoate (SM)
- B = methyl 2-chloro-3-bromomethyl-4-methylsulfonylbenzoate (bromide)
- C = methyl 2-chloro-3-(2,2,2-trifluoroethoxy)methyl-4-methylsulfonylbenzoate (ether ester; appears ONLY in scheme line 152, step_id 2 product; never named in text, never isolated as a weighed product)
- D = 2-chloro-3-(2,2,2-trifluoroethoxy)methyl-4-methylsulfonylbenzoic acid (acid)
- E = corresponding acid chloride (appears ONLY in scheme line 152, step_ids 4/5)
- T = tembotrione
- CHD = 1,3-cyclohexanedione

## Mechanical checks done
- Step headings "…的合成": 21 total = 3 (claim 1) + 3 (summary [0013]/[0015]/[0017]) + 15 (5 example blocks x 3). Matches.
- Yields (收率 with a number): 15 in performed procedures (181,193,200,211,221,228,239,249,256,272,279,286,298,305,312), 4 in background prior art (67% L96, 75.2% L98, 70% and 69% both on L100). 收率 total occurrences 23 (line 100 carries two).
- IMAGE_EXTRACT: 16. Line 152 = 5-equation reaction scheme. Lines 178,185,197,208,215,225,236,247,254,265,276,283,296,303,310 = single product structure per example step.
- Example markers: 实施例1 L172, 2 L202, 3 L230, 4 L258, 对比实施例 L288. Five blocks.

## Scheme (line 152) step order anomaly
step_id order in the drawing JSON: 1 (A->B), 2 (B+TFE->C), 3 (C->D, "碱/酸"), 4 (E+CHD->T, arrow "三乙胺/丙酮氰醇"), 5 (D+SOCl2->E).
Chemical order is 1,2,3,5,4. The drawn/extracted order lists condensation before acid-chloride formation. Recorded, not resolved.

## Telescoping map (text step -> chemical transformations)
- Patent "step 2" = two transformations in one procedure: etherification (B+TFE->C) then alkaline hydrolysis + HCl acidification (C->D). Only D is isolated/weighed.
- Patent "step 3" = two transformations in one procedure: acid chloride formation (D+SOCl2->E, DMF cat) then CHD/Et3N O-acylation + acetone-cyanohydrin rearrangement to T. Only T is isolated/weighed. The enol-ester intermediate is never named in this patent's own route; it is named only in background L100-101 as CN104292137A's isolated intermediate.

## Performed procedures (15), with per-run data
Ex1: step1 [0040] L181 (AIBN 0.006mol, HBr 0.273mol, H2O2 0.182mol, DCE, 75-80C, iPrOH recryst; 46.67g, 99.0%, 89%)
     step2 [0043] L188 + continuation L193 (K2CO3, DABCO 0.0079mol, THF, TFE 0.1039mol, 75-80C; then NaOH 0.1485mol/water 70-75C, HCl; 27.6g, 99.7%, 91.3%)
     step3 [0046] L200 (DMF cat, SOCl2 0.102mol, DCE reflux; CHD 0.084mol, DCE, Et3N 0.18mol at 25-30C; acetone cyanohydrin 0.011mol; MeOH recryst; 30.3g, 98.6%, 86%)
Ex2: step1 [0051] L211 (AIBN 0.003, HBr 0.202, H2O2 0.22, DCM, 45-50C, EtOH recryst; 46g, 99.8%, 88.5%)
     step2 [0054] L221 (K2CO3 0.153, DABCO 0.008, DMF solvent, TFE 0.153, 70-75C; NaOH 0.112; 31.82g, 99.8%, 89.6%)
     step3 [0057] L228 (DMF 0.004, SOCl2 0.103, DCE reflux; CHD 0.129, DCM, Et3N 0.215 at 20-25C; ACH 0.011; EtOH recryst; 32.63g, 98.1%, 84.1%)
Ex3: step1 [0062] L239 (mCPBA 0.001 as catalyst, HBr 0.1, H2O2 0.1, DCE, 50-55C, iPrOH; 31.33g, 98.0%, 90%)
     step2 [0065] L249 (K2CO3 0.1, DABCO 0.001, acetone solvent, TFE 0.1, 0-5C; NaOH 0.1; 31.58g, 99.3%, 90.5%)
     step3 [0068] L256 (DMF 0.001, SOCl2 0.1, DCE reflux; CHD 0.1, DCE, Et3N 0.2 at 0-5C; ACH 0.001; MeOH; 37.94g, 98.7%, 85%)
Ex4: step1 [0073] L267+L272 (CHCl3 solvent, AIBN 0.01, HBr 0.2, H2O2 0.2, 60-65C, EtOH; 30.66g, 98.8%, 88.7%)
     step2 [0076] L279 (K2CO3 0.2, DABCO 0.01, DMF, TFE "1.13g, 0.2mol" <- mass/mol conflict, 20-25C; NaOH 0.2; 31.1g, 99.5%, 89.3%)
     step3 [0079] L286 (DMF 0.01, SOCl2 0.2, DCE reflux; CHD 0.2, DCM, Et3N 0.3 at 5-10C; ACH 0.01; EtOH; 38.1g, 98.7%, 85.4%)
Comp: step1 [0083] L298 (Br2 0.055mol, CCl4, reflux 4h; 15g pale yellow solid, 88%; no purity)
      step2 [0086] L305 (NaOMe 0.1mol in DMF, TFE dropwise NO AMOUNT, bromide B NO AMOUNT, RT overnight; then EtOH + 10% NaOH 40ml, reflux 1h; HCl; yellow solid, 83.2%, NO MASS)
      step3 [0089] L312 (SOCl2 0.5mol, DCM reflux 5h; MeCN, CHD 0.12mol, ACH 4 drops, RT 16h; HCl to pH 2; 35g yellow solid, 80%; note "整出" misprint for 蒸出)

## Mass-balance spot checks (all pass except flagged)
- Ex1 step1: 0.152mol x 341.6 = 51.9g theor; 46.67g x 0.99 / 51.9 = 89.0% -> matches stated 89%.
- Comp step1: 0.05mol -> 17.08g theor; 15/17.08 = 87.8% ~ 88% ok.
- Comp step3: 0.1mol T -> 44.08g theor; 35/44.08 = 79.4% ~ 80% ok.
- Ex3 step3: 37.94 x 0.987 / 44.08 = 85.0% ok.
- FLAG Ex4 step2 L279: "1.13g TFE (99%, 0.2mol)" impossible; 0.2mol TFE = ~20g. Mass and moles disagree; record both, do not resolve. (Compare Ex2's 15.46g = 0.153mol, consistent.)
- FLAG Ex1/Ex2 step3: acetone cyanohydrin 0.011mol vs substrate 0.0788/0.086 mol = 0.14/0.13 eq, above claim-8 range 0.01-0.1. Ex3 (0.01 eq) and Ex4 (0.1 eq) inside range. Noted only; claims compliance is not this pass's job.

## Described-only entries
Invention route (abstract L40-41 = claim 1 L47-60 = summary [0012]-[0018] L115-128 = ONE description per transformation; condition ladders from claims 2-10 L61-78 and [0019]-[0027] L129-149; scheme L152):
 D1 A -> B (HBr/H2O2, radical cat)
 D2 B + TFE -> C (K2CO3, DABCO)
 D3 C -> D (base 2 then HCl)
 D4 D + SOCl2 -> E (DMF cat)
 D5 E + CHD -> T (Et3N, then ACH)
Prior art (each cited document x reagent system = its own entry, rule 2):
 P1 L96-97 CN1323292A: A -> B with NBS, 67% (route from 2,6-dichlorotoluene: route mention only, no step data)
 P2 L98-99 CN105601548A: A -> B with NBS, 75.2% (route from 3-chloro-2-methylaniline: mention only)
 P3 L100-101 CN104292137A: A -> B with Br2, 70%
 P4 L100-101 CN104292137A: etherification using sodium 2,2,2-trifluoroethoxide
 P5 L100-101 CN104292137A: T synthesis using cyanoacetone, via isolated enol ester intermediate, 69%
 P6 L102-103 CN106008290A: etherification using sodium trifluoroethoxide
 P7 L102-103 CN106008290A: T synthesis using expensive condensing agent
 P8 L107-108 Nongyao 56(5): A -> B with Br2
 P9 L107-108 Nongyao 56(5): etherification using sodium methoxide
 P10 L107-108 Nongyao 56(5): T synthesis in acetonitrile

## Excluded (explicit, with reason)
- X1 L107-108: NaOMe + H2O -> MeOH + NaOH. Hazard property statement; a reaction under conditions nobody used. Out by scope rule.
- X2 L90-91: HPPD inhibition / blockade of prenylquinone biosynthesis. Biology/mode of action, not a preparative transformation.
- X3 Route-level mentions "from 2,6-dichlorotoluene" (L96), "from 3-chloro-2-methylaniline" (L98), "from 2-chlorotoluene" (L100): whole prior routes named with no step data beyond the steps counted in P1-P5; not countable as transformations themselves.
- X4 Recrystallisations, washes, acidification workups as standalone entries: treated as workup of their parent transformation, except D3/step-2 hydrolysis+acidification which forms a new compound (D) and is counted.

## Count summary
Performed: 15 procedures (12 example + 3 comparative); 25 chemical transformations if telescoped steps split (each step2 = 2, each step3 = 2; comparative same).
Described-only: 5 invention-route transformations (scheme granularity; 3 at patent-step granularity) + 10 prior-art entries.

## Decisions locked with the user (2026-09-01)
1. Unit of record = the individual chemical transformation, not the numbered
   procedure. Performed count for this patent is 25. Matches the patent's own
   scheme granularity (5 equations, L152). Telescoped pairs stay linked to their
   parent procedure: each step-2 run carries 2a (etherification) + 2b (hydrolysis/
   acidification), each step-3 run carries 3a (acid chloride) + 3b (condensation).
   The 2a/3a intermediates (C = ether ester, E = acyl chloride) are never isolated;
   their entries carry no yield/purity of their own.
2. NaOMe + H2O -> MeOH + NaOH (L107-108) stays EXCLUDED (scope rule 8: hazard
   property, conditions nobody used). Kept on the excluded list with reason,
   not silently dropped.

## Pass 2 reconciliation vs output/reactions.json (2026-09-01)
File holds 36 records: 10 Background + 3 Claims + 3 Summary + 5 Scheme + 15 performed.
Schema (pipeline/prompts/A2-reactions.md rule at L63): telescoped sequence = ONE record,
is_one_pot true, transformations enumerated in one_pot_steps, class from FINAL transformation.
So 15 performed records is the format's intended shape, not a collapse defect.
Mapping: my P1-P25 -> 15 performed records (one_pot halves inside one_pot_steps);
my D1-D5 -> Claims 1-3 + Summary 1-3 (twins) + Scheme 1-5; my D6-D15 -> Background 1-10 in order.
Matched 40/40, missing 0, extra 0.
Verified mechanically: all yields/purities match pass-1 table; all procedure_text and
provenance quote_zh verbatim (page-break joins at L188+193 and L267+272 are genuine);
provenance source_lines all agree with my line map.
File found 3 arithmetic defects I missed, all verified true by me: DABCO mass/mole pairs
Ex2 (1g vs 0.008mol), Ex3 (0.226g vs 0.001mol), Ex4 (2.26g vs 0.01mol). TFE 1.13g/0.2mol
caught by both. All flagged as printed, none repaired - correct behaviour.
Open items for the user:
 (a) step-3 sub-granularity: file enumerates 3 transformations (chlorination, O-acylation,
     ACH rearrangement) per example step 3; I counted 2. File's reading better grounded
     (two separate LC-monitored stages). At file granularity performed = 29 not 25.
     Comparative step 3 stays 2 (CHD+ACH charged together).
 (b) acidification convention: file treats HCl acidification as workup/isolation, not a
     transformation - matches my 2b bundling. Consistent.
 (c) twin asymmetry: Claims_Step 3 folds claim-10 ladder (DMF, crystallisation solvents);
     Summary_Step 3 does not fold [0027]. Covered by the twin; noted, not missing.
 (d) ACH role label: catalyst (Claims/Examples/Comp) vs reagent (Summary_Step 3).
 (e) reaction_class wobble: tembotrione step = acylation everywhere except Claims_Step 3
     = other; Scheme Step 5 (acid -> acid chloride) classed halogenation.
 (f) Background 4/6/9 product recorded as the ACID, following the patent's own phrasing
     (在制备[acid]的时候); my pass-1 read them as the ether. Adopted the record's reading.

## Transformation-level edit of reactions.json (2026-09-01)
USER DECISION: gold records at transformation level; edit reactions.json ONLY, no other file.
Done by hand-edit via deterministic script (scratchpad/split_to_transformation_level.py),
original preserved at scratchpad/reactions.json.before-split. Git holds the prior version.
Result: 36 -> 56 records. 14 one-pot parents replaced by 34 stage children:
  Ex1-4 Step 2 -> 2a etherification + 2b hydrolysis/acidification
  Ex1-4 Step 3 -> 3a acid chloride + 3b O-acylation (enol ester) + 3c ACH rearrangement
  Comp Step 2 -> 2a + 2b; Comp Step 3 -> 3a + 3b (CHD+ACH charged together, undivided)
  Claims/Summary Step 2 -> 2a + 2b; Step 3 -> 3a + 3b + 3c
Conventions: final stage keeps parent reaction_uuid; earlier stages get uuid5 ids;
intermediates use existing A1 identifiers (ether ester and acyl chloride as SMILES,
enol ester by its line-101 name); stage temps in structured fields; yields only on
final stages (patent isolates nothing else); flags moved to the stage carrying the
defective charge; claim/paragraph molar-ratio quotes kept on first stage with pointers;
full parent notes preserved verbatim on each first child; procedure_text kept verbatim
on all children. 22 unsplit records byte-identical to before (verified).
KNOWN STALENESS (user forbade touching other files): reactions-provenance.json,
pathways.json, chemistry-rollup.json still reference the 14 old parent ids/counts.

## Dedup edit of reactions.json (2026-09-01, second hand-edit)
USER DECISION: keep the claims copy of the described route; remove the Summary prose
twins (verified field-identical first: same temps, same products, same ratio values,
no unique compound or quantity; only label wobbles - water reactant/solvent, ACH
reagent/catalyst - preserved as notes); merge the 5 scheme records' drawn evidence
into the Claims records as SCHEME EVIDENCE notes (arrow reagents, line 150-152,
drawn-order anomaly, shared arrow for 3b/3c) and remove them.
Result: 56 -> 45 records = exactly the 45-identity checklist, 1:1.
Sections: Background 10, Claims 6, Comparative 5, Examples 6x4.
Backup: scratchpad/reactions.json.before-dedup. Verified: the 11 removals are the only
record deletions; the 6 Claims records changed only in notes/tags; all 33 other records
byte-identical. Sidecars remain stale by user instruction (now also missing-id stale
for the 11 removed records).

## Prompt 5 record verification log (2026-09-01)
Method: .md re-read end to end this pass; every record checked against the whole text;
all printed charges/assays/volumes/temps/times compared mechanically (0 mismatches);
empty-field sweep on every record; cross-record like-vs-like via the expectation table.
Per-record results (idx: verdict):
 0-9 Background 1-10: PASS. Yields 67/75.2/70/69 correct; products per patent phrasing;
   no_conditions correct (patent states none); safety_notes present on 3/8/9/10 where
   the patent makes hazard statements; quotes verbatim L96-108.
 10 Claims_Step 1: values correct and rich (full claim 2-4 ladders, roles right).
   FLAGS: F-005 (ratio ranges prose-only), F-006 (claim-1 <=80 tier not structured).
 11 Claims_2a: F-005, F-006 (claim-1 <=80 vs stored 0-80). 12 Claims_2b: F-005.
 13 Claims_3a: F-005. 14 Claims_3b: F-005, F-006 (<=30 vs 0-30).
 15 Claims_3c: F-005, F-004 (claim text L59 states beige solid).
 16 Comp_1: values correct (13.1g/0.05mol, CCl4 180ml, Br2 8.8g/0.055mol, reflux, 4h,
   15g, 88%); FLAGS: F-003 (500 mL volume prose-only), F-004 (pale yellow solid).
 17 Comp_2a: F-001 (overnight prose-only); cooling 15C captured. 18 Comp_2b: F-004
   (yellow solid); 10% NaOH captured in conditions.concentration + 40 ml. 19 Comp_3a:
   PASS (5h, reflux, 5 eq SOCl2). 20 Comp_3b: F-002 (stirring stated L312, field null),
   F-004; pH2/16h/2mol/L captured.
 21-26 Ex1: S1 F-003+F-004; 2a F-003; 2b F-004; 3a F-003; 3b PASS; 3c F-004.
 27-32 Ex2: same pattern (2a also carries correct molar_mass_inconsistent).
 33-38 Ex3: same (2a mmi+sd, 3a sd correct).
 39-44 Ex4: same (2a mmi+sd, 2b mbi, 3a sd correct; TFE 1.13g/0.2mol and DABCO
   2.26g/0.01mol recorded exactly as printed, never repaired - correct).
No value errors. No missing reactions. No inventions. All findings are structural
(patent value with no structured field): F-001 overnight, F-002 stirring, F-003 flask
volumes (13 records), F-004 product appearance (16 records), F-005 claim ratio ranges
(6 records, 12 values), F-006 claim-1 temperature tiers (3 records).
Unresolved (both readings kept, in-file): Ex4 TFE mass/mole pair; DABCO pairs Ex2/3/4.

## Prompt 5 fixes applied (2026-09-01, annotator decisions)
F-001 FIXED: conditions.time_text = "overnight (反应过夜, room temperature)" on Comp 2a;
  time_h stays null; chemist reading (~12-16 h) in note only, never as data.
F-002 FIXED: stirring.type = "stirred" on Comp 3b.
F-003 DECLINED by annotator (flask volumes stay in notes only). Left flagged.
F-004 FIXED: new field isolated_product_appearance on 16 records, printed form verbatim
  (white powder x8, beige crystalline powder x4, pale yellow solid, yellow solid x2,
  claim-recited beige solid on Claims_Step 3c).
F-005 FIXED: equivalents_min/equivalents_max inside quantity on the compounds named by
  claims 2/5/8 (basis 1 mol of step substrate; substrate 1/1; ranges on generic
  placeholders apply to whichever alternative is used). 12 range values + 3 substrate
  bases structured. equivalents single float stays null.
F-006 NO CHANGE NEEDED: stored minima (45/0/0) are the observed data, annotator
  confirmed; claim-1 tiers consistent and documented in notes.
reactions.json remains the only repo file edited (plus GOLDEN-DATASET-FINDINGS.xlsx,
which this prompt directs). Sidecars still stale as before.

## Prompt 7 record (restored, condensed — the detailed sections were lost when this
## file was rewritten by the compounds session at 22:13)
Prompt 7 ran twice on reactions.json (full pass + confirmation) plus the annotator's
gold decision. Value layer clean both times. Fixes, all generated/derived layer:
D1 Ex4 S1 named_reaction Wohl-Ziegler->null (rule 19, NBS-only, per records 10/21/27/
33); D2 Comp 3a confidence high->medium; D3 Background 5 reactant_names gained the
enol ester; D4 HBr role reagent->reactant on Claims/Ex3 Step 1; D5 the 29 split-stage
records' tags re-derived at stage level (is_one_pot/analytics/purification/workup/
solvent/safety/step_role were parent inheritance); D6 sparse tag sets unified
(33/39/38/44/15/14); D7 byproduct_recovery -> []; D8 Ex4 S1 stray
conditions.concentration nulled (two-strength rule of Ex1-3); D9 concentration null
shape unified on 17 children. GOLD DECISION: named_reaction "saponification" on all
six 2b records, matching CN112645853A; superseding notes on 2b + 17/22/28/34/40.
reactions.json: GOLD, 45 records. Full logs: session scratchpad prompt7-record-log.md.

## Prompt 8 final gold check on compounds.json (2026-09-01)
Full re-derivation pass, record by record, 0-57; every field checked against the
patent (whole .md re-read), in-file like-for-like groups, and both sibling golds.
Value layer: CLEAN. All 114 per-batch array entries + every base quantity verified
against the patent (0 mismatches, source_lines correct); analytics quotes verbatim;
no phantom measurements; ids/uuids unique; reactions.json join fully resolvable;
misprinted TFE/DABCO pairs still exactly as printed with contradiction notes.
Defects found and FIXED (15 records; backup compounds.json.before-prompt8):
 P8-1 idx 21 bromine, idx 25 sodium methoxide: anchored at Concluding Comparison/
   beneficial_effects although their merged fragments come from [0007]/[0009] —
   first-in-document-order per the file's own merge rule -> Background/background,
   matching NBS and sodium trifluoroethoxide.
 P8-2 Merge-stale section_type (label moved, type left behind): idx 32 CHD and 48 SM
   -> summary_of_invention; idx 50 bromide and 51 acid -> background; idx 53 mCPBA
   -> claims. Label/type now pair correctly on all 58 (family convention).
 P8-3 idx 9 acetone cyanohydrin: role reagent->catalyst and duplicate
   compound_class:reagent removed — the record's own note cites claim 8's 0.01-0.1
   loading as supporting catalyst, it carried both class tags, and the reactions gold
   holds catalyst in every procedure.
 P8-4 idx 45 DMF solvent twin: stray compound_class:catalyst removed (the catalyst
   identity lives on the twin record "DMF" by the twin-split ruling).
 P8-5 identifier_type iupac->trivial_name on hydrobromic acid (matches in-file
   hydrochloric acid and both siblings), AIBN, isopropanol, n-butanol,
   triethylenediamine, 2-methyltriethylenediamine (each holds its true IUPAC name as
   an alias — the chloroform pattern; sibling precedent tert-butanol).
Deliberate non-changes, documented in the log: C-010 synonym aliases kept by
annotator ruling (the one standing deviation from the every-alias-literal criterion;
several are join-critical); water's Abstract/summary_of_invention pairing (no
'abstract' section_type exists in the family); cyanoacetone's acknowledged cross-file
role divergence; the role-aware DMF twin join.
Post-fix battery: label/type pairs clean, joins resolve, quotes verbatim, numbers
0 mismatches. Scope diff: 15 records changed in exactly the named fields, 43
byte-identical.
VERDICT: compounds.json is GOLD. 58 records (49 specific substances + 9 generic
class terms; DMF and dichloroethane held as deliberate twins).

## Blind adversarial review, compounds.json (2026-09-01)
A fresh subagent with no context (barred from audit/, scratchpad and reactions.json;
instructed to treat record notes as claims, not authority) reviewed all 58 records
against the whole patent. Its verdict: zero CRITICAL findings — no wrong, invented or
missing substance or value; all batch entries verbatim; misprints preserved; the
氰基丙酮/丙酮氰醇 trap handled. It reported 3 MAJOR + 8 MINOR consistency findings.
Disposition after verification (backup compounds.json.before-blindreview):
FIXED (11 records): DMF base quantity moved to the Example 4 batch (was the lone Ex3
deviation from the file's own convention); anchors normalised to one scheme — mCPBA
Claims->Summary (its same-sentence sibling AIBN and every claim-enumerated alternative
sit there), water Abstract->Summary, DMF-solvent Example 4->Summary, dichloroethane
Example 4->Example 1 (its own first batch); SM commercially_available false->true
(unprepared, assay-carrying starting material; sibling-gold precedent; the merged
fragments themselves disagreed); n-butanol +chemical_family:alcohol; DABCO family
tags pruned to its twin's single tag; chloroform -hazard:toxic (patent silent,
sibling chlorinated solvents untagged); NaOH comparative batch entry now carries the
printed 10% strength (parity with HBr 48%/H2O2 30%); acid comparative appearance
trimmed to the printed 黄色固体 + L188/L193 page-break citation documented; schema
shapes unified on 49/54-57 (analytics [], quantity object, stray smiles key removed).
REFUTED by family/ruling context the blind agent could not see: multi compound_class
tags (both sibling golds do the same, incl. water workup_agent+solvent); the DMF twin
split (annotator ruling, dual role); tembotrione's Technical Field anchor (family
convention for the target compound); dichloroethane comm=true under resolved=false
(either isomer is a commodity). Post-fix battery clean; label/type scheme now fully
regular. compounds.json remains GOLD, now also consistency-hardened.

## Blind adversarial review, reactions.json (2026-09-01)
A fresh no-context subagent (barred from audit/, compounds.json and scratchpad;
notes-as-claims) reviewed all 45 records against the whole patent. Verdict: zero
CRITICAL — "could not break a single number against the page"; every printed value,
misprint preservation, flag and stage assignment verified clean. 3 MAJOR + 4 MINOR
consistency findings; disposition after verification (backup
reactions.json.before-blindreview):
FIXED (7 records): catalyst_class:none removed from Examples 1-4 Step 1 (the token
means no-catalyst-charged, per Comparative Step 1; unclassifiable charged catalysts
are left untagged, per the claims record and every DMF/cyanohydrin stage);
Comparative 3b transformation:condensation -> acylation + rearrangement (the
bundled-record pair of Background_Step 5; condensation stays reserved for Background
Step 7's own 缩合剂) and confidence high->medium (matching every sibling resting on
the same inferred migration reading); Background_Step 5 missing_reactant removed
(the flag means no reactant-role compound, and this record holds one — the lone
violation vs Steps 4/6/9); Background_Step 7 +mechanism:not_determinable (Step 10
and all five WO2022024094A1 analogues carry it).
REFUTED with grounding: Background 9/10 transformation-tag difference (the patent
supplies 缩合剂 for one and nothing for the other); identifier drift across sections
(documented join design; every reference resolves via the compounds gold);
sodium methoxide role reactant-vs-base (text-grounded: 原料 wording vs functional
charge, reasoned in the record note); time_text single key (annotator fix F-001)
and flask volumes in notes (annotator-declined F-003).
Post-fix battery clean, all mirrors and rules now hold file-wide (missing_reactant
iff no reactant-role compound; catalyst_class:none iff no catalyst charged).
reactions.json remains GOLD, consistency-hardened. CN109678767A: both halves now
gold + independently adversarially reviewed.
