# WO2024109718A1 Prompt 5 - record-by-record content verification
Started 2026-09-02. 69 records. Flag only, no fixes.
Method: 6 parallel readers each read the full 1121-line md for their record range;
plus my own mechanical audit below. Every agent finding gets verified by me against
the page before it reaches the Excel.

## Schema facts established first (so fields are judged by what they hold)
- reactant_smiles, product_smiles, smiles_source, product_smiles_source, canonical_rxn
  are typed "null" in reactions.schema.json. They are deprecated placeholders.
  Their emptiness on 69/69 is CORRECT, not a finding.
- conditions.temperature HAS min_c/max_c. melting_point (compounds) HAS min_c/max_c.
- conditions.time_h, conditions.ph_value, product_yield_pct, product_purity_pct are
  single floats. conditions.pressure has value_kpa + qualitative_text only, no min/max.
  These are the loss-prone fields.

## My mechanical findings (verified against the page)

MA. PRESSURE RANGE LOST, 7 records: 33,34,40,41,45,46,53.
   Patent: 通入CO的压力约为1～4MPa / 以约1～4MPa的压力通入CO (lines 320-321,336,493,506,525,537,548).
   1-4 MPa = 1000-4000 kPa, a range. value_kpa is null on all 7; the range survives only
   in pressure.qualitative_text, which is prose. pressure object has no min/max field.
   -> RANGE_LOST + NO_FIELD_EXISTS.

MB. pH RANGE LOST / MISSING, 6 records: 32,33,40,41,44,53.
   Patent: 调酸至pH为2～3 (lines 433,493,506,514,537) and 调节pH至8～9 (line 537).
   conditions.ph_value is null on ALL 69 records. Only record 33 has workup.ph_target=2.0,
   which stores the bottom of a 2-3 range as a point value and loses the top.
   Record 33's second adjustment, pH 8-9 with NaOH before re-acidifying, is absent entirely.
   -> MISSING (32,40,41,44,53) + RANGE_LOST (33).

MC. PREFERENCE TIER LOST, 3 records: 49,50,53. Nested tiers are separate facts.
   49 (I->II):  patent line 270-271 升温至40-100℃，优选升温至70-90℃. Record holds 40-100 only.
   50 (II->III): patent line 293-294 升温至40-100℃，优选升温至50-80℃. Record holds 40-100 only.
   53 (IV->V):  patent line 320-321 在80～120℃、优选80～90℃. Record holds 80-120 only.
   temperature object has no slot for a preferred sub-range.
   -> NO_FIELD_EXISTS (the preferred tier has nowhere to go).

MD. TIME MISSING + RANGE, record 53.
   Patent line 320-321: 反应10～20h. Record conditions.time_h is null.
   Even if filled, a single float cannot hold 10-20.
   -> MISSING + RANGE_LOST.

ME. MOLAR RATIO PROSE-ONLY, record 49.
   Patent line 275-276: 式(I)化合物与双氧水的摩尔比约为1-5，优选为2-3.
   Held only in molar_ratio_text as raw Chinese. Nothing downstream reads prose, and the
   preferred tier 2-3 is a second fact. No structured molar-ratio field exists.
   -> NO_FIELD_EXISTS + RANGE_LOST.

MF. MULTI-STAGE ONE-POT: stage-1 conditions leave the structured fields.
   Record 33 (Ex10, line 537): stage 1 ran 在80～85℃反应约8h, stage 2 降温至70℃...继续反应5h.
   conditions holds only stage 2 (70 C exact, time_h 5.0). Stage 1's 80-85 C and ~8 h
   survive only as prose inside one_pot_steps. The one-pot rule (conditions from the final
   transformation) is applied consistently, but there is no per-stage conditions structure,
   so a genuine measured range and duration are lost to prose.
   Record 39 (Ex5, line 483): stage 1 at -5～5℃ is in compounds[].addition_profile prose,
   not in conditions. temperature max_c=15 with min_c null is CORRECT for 不超过15℃.
   -> NO_FIELD_EXISTS (per-stage conditions).

## Verified CORRECT, not findings (checked, do not re-raise)
- All example charges present: every mass/volume in lines 428-548 matches a
  compounds[].quantity. Cross-checked numerically on all 15 example records.
- Record 46 (Ex9 M2) correctly inherits Method 1's charges via conditions_inherited
  (94.73, 29.41, 27.83, 3 g, 80 g ethanol) plus its own 300 g solvent.
- Melting points: all five captured in compounds.json with min_c/max_c
  (II 138-140, III 148-150, IV 80-82, V 155-157, tembotrione 120-122). No loss.
- Appearance/colour: tembotrione 黄白色粉末状固体 and V white solid captured in compounds.json.
- product_yield_pct set on exactly the 6 records the patent gives a yield for
  (40:81.13, 41:67.57, 44:76.59, 45:80, 46:71.63, 33:64.67). All point values, correct.
- process_control (HPLC) on 14 of 15 example records. Record 34 (Ex11) empty is CORRECT:
  line 548 states no HPLC monitoring.
- product_purity_pct empty on 69/69 is CORRECT: the patent reports no purity figure.
- product_ms_mz / product_ms_type empty is CORRECT: no MS data in the patent.
- Record 34 product_name null is CORRECT: 收率低于10％ is a bound, no product named.

## Agent findings, VERIFIED BY ME against the page

### Background, indices 0-21 (agent 1) - I re-checked every claim below
BA. [0004] NEVER CITED. Line 67 (上述方法中所用的原料二氯甲苯在反应时选择性不好，导致杂质多，收率低)
    is cited by NO record. Verified mechanically. Scheme A therefore carries none of the
    patent's criticism, while Scheme B's line 76 IS cited by records 12, 14 and 21.
    Three separate assertions lost: poor selectivity, many impurities, low yield.
    -> MISSING on record 0 (+ asymmetry against the Scheme B twins).
BB. selectivity is null on 69/69 records, yet line 67 states a selectivity fact about
    record 0's arrow. -> MISSING (the field exists and is empty while the patent speaks).
BC. Record 0 identifier is the generic "dichlorotoluene". The DRAWN structure is
    Cc1c(Cl)cccc1Cl, C7H6Cl2, which I walked atom by atom: ring position 1 methyl,
    2 chloro, 6 chloro = 2,6-DICHLOROTOLUENE. structures-resolved.json carries that
    SMILES under the generic name. Both readings are real: the prose at line 67 says only
    二氯甲苯, the drawing pins the isomer. Every other background identifier is a specific
    IUPAC name taken from the drawing. -> record both readings, do not silently pick one.
BD. UNTRANSLATED CHINESE IDENTIFIERS, 8 of them across 6 records (I found more than the
    agent did): 14 卤仿 (haloform); 32 重氮盐 (diazonium salt); 49 钨酸盐/偏钨酸盐/钒酸盐/
    偏钒酸盐 (tungstate/metatungstate/vanadate/metavanadate); 50 溴代试剂 and 63 溴代试剂
    (brominating reagent). CLAUDE.md: English only in anything that can reach a screen.
    -> NAMING, run-wide.
BE. by_product role EXISTS in the vocabulary and is used once (record 33, triethylamine
    hydrobromide), but record 14's haloform and sodium chloride, which line 76 explicitly
    calls reaction by-products, are role "other" - the same bucket as inert nitrogen.
    -> INCONSISTENT_SIBLING.
BF. SOURCE-DOCUMENT ATTRIBUTION has no field. Which prior-art document a background scheme
    came from (DE19846792A1 at line 63 for records 0-10; CN200510030163.5 at line 69 for
    records 11-21; CN106631941A at line 253 for record 48) survives only in procedure_text
    and notes. It is the single fact that makes routes A and B two routes rather than one.
    -> NO_FIELD_EXISTS, run-wide.
BG. Route-level criticism has nowhere to live: 11 steps / too many steps (line 76), the
    F-C irritancy, the AlCl3 waste water, the NaCl waste water, and the raw-material
    economy clause (line 76-77, 分子中通过F-C反应接上乙酰基后还需要通过氯仿反应断掉甲基...
    存在成本高、效率低). Partly in safety_notes on 12 and 14; the economy clause is nowhere.
    -> NO_FIELD_EXISTS + MISSING.
BH. UNRESOLVED, two readings, do not guess: record 10 (Scheme A step 11, VIII->tembotrione)
    has no catalyst. Line 344 attributes the route to DE19846792A1 and line 348 says a
    cyanide catalyst, particularly acetone cyanohydrin, is preferred for this rearrangement.
    Reading (a): 344/348 describe this very arrow, so the catalyst belongs on record 10.
    Reading (b): 343-350 sit in the invention's own description and may be the applicant's
    preference, not a report of what DE19846792A1 taught. Record both.

Agent-raised but NOT carried forward by me:
- "quote_zh quality": the per-record Chinese quotes are thin slices (record 10's quote is
  just 环磺酮). Real, but it is evidence-presentation, not a data defect, and no field is
  wrong. Noting here only.
- Records 1/3 lacking the [0006] criticism that 12/14 carry: the agent itself concedes
  [0006] says 上述方法 and follows Scheme B, so scoping it to B is defensible. Subsumed
  into BA as the asymmetry point rather than raised as its own defect.

Verified clean in this range: 2, 4, 5, 6, 7, 8, 9, 11, 13, 15, 16, 17, 18, 19, 20.
Background prints no temperature, time, pH, yield or purity anywhere, so the empty
conditions/purification/process_control/yield fields there are correct absences.

### Claims, indices 22-31 (agent 2) - all re-verified by me mechanically
CA. REAGENT COMBINATION DESTROYED, records 26 and 63. Patent lines 585-586 / 603-604 /
    664-665: 溴代试剂为溴素、N-溴代丁二酰亚胺、或者溴化氢与双氧水的组合 = THREE alternatives,
    the third being the COMBINATION of hydrogen bromide WITH hydrogen peroxide.
    Record 26 carries four flat reagents: bromine, NBS, hydrogen bromide, hydrogen peroxide.
    The pairing is gone, so the data now asserts HBr alone and H2O2 alone are each a
    brominating reagent, which the patent never says. -> WRONG_VALUE (structural).
CB. PREFERENCE "优选为溴素" (preferably bromine), stated three times in the claims, lives
    only in procedure_summary and notes. Bromine and NBS are indistinguishable in the data.
    Same class as MC: no preference field exists. -> NO_FIELD_EXISTS.
CC. GENERIC ARROW LABEL DROPPED, record 26. The claim arrows at 591, 601, 643 are labelled
    溴代试剂. Record 63 carries that generic as a reagent identifier; record 26 does not,
    and its own note claims A1 recorded no generic for the claims, which its own
    drawing_evidence contradicts. -> MISSING + INCONSISTENT_SIBLING.
CD. ROLE INCONSISTENCIES across twins describing the same arrow, verified:
    water on IV->V: record 22 role=reactant, record 59 role=reagent, record 53 role=reactant.
    hydrogen peroxide on II->III: record 26 role=reagent, record 63 role=oxidant.
    -> INCONSISTENT_SIBLING.
CE. reactant_names INCOMPLETE, record 27: holds only [aryl bromide, CF3CH2OM] while its
    compounds[] and its twin record 64 both carry CF3CH2ONa and CF3CH2OK as well.
    -> MISSING + INCONSISTENT_SIBLING.
CF. CLAIM GENUS NARROWED, records 22 and 23. Line 565/570 claims 磷配体或其盐 = any
    phosphine ligand OR A SALT THEREOF, with three named only as preferred. The records
    carry the three species and neither the genus nor the salt option. The schema does
    admit generic identifiers (record 63 carries 溴代试剂, record 52 carries CF3CH2OM), so
    this is a fillable gap, not a schema limit. -> MISSING.
CG. SOLVENT COMBINATION ALLOWANCE LOST, records 22, 23, 53. Line 572-573 and 333-334:
    一种或两种以上 = one OR A COMBINATION OF TWO OR MORE. Seven solvents are listed flat with
    no way to say they may be mixed. -> NO_FIELD_EXISTS.
CH. ONE-POT OPTION UNRECORDED, record 23. Lines 610-611 and 622-623 state step (vi')/(vi'-1)
    may be run stepwise WITH isolation of the ester or one-pot WITHOUT isolating it. Record
    23 absorbed (vi') Arrow 1, so it is half the step that sentence governs, yet is_one_pot
    is false (a positive assertion of not-one-pot), one_pot_steps is empty, and unlike its
    sibling record 31 it does not even mention the alternative in notes. Its own provenance
    cites 610 and 622. -> MISSING. Related: record 31 reasons it out in notes only, which
    is prose. A tri-state or one_pot_optional field is what this needs.
CI. STEP_INDEX GAPS INTRODUCED BY MY OWN MERGE. Verified:
    Claims 1,2,3,4,5,6,8,9,10,12 (7 and 11 missing)
    Summary 1,2,3,4,5,6,8,9,10,12 (7 and 11 missing)
    Preparation Routes 1..9 then 16,17,18 (10-15 missing)
    The 2026-09-02 dedup merge repointed precursor_step correctly but did not renumber
    step_index. This is a defect I introduced, not one the extraction shipped.
    -> MUST be disclosed as such in the Excel.

Agent-raised, recorded with BOTH readings rather than decided:
- Record 29 (and its twin 57) carry cyclohexane-1,3-dione on an arrow the claims draw BARE.
  Reading (a): mass-balance inference, since the drawn product is that dione's enol ester
  and the dione IS written on the (v')/(vi') arrows at 563/620. Reading (b): a compound the
  page does not put on this arrow. Applied consistently on both twins, so it is a house
  rule rather than a slip. Record both, do not resolve.

Confirmed independently by this agent, matching my own MC/MD: record 53 loses the
preferred 80-90 C tier and has time_h null against a stated 10-20 h.
Verified clean in this range: 28, 30 (and 29 subject to the borderline above).

### Summary, indices 59-68 (agent 6) - re-verified by me
SA. ROLE DIVERGENCE ON IDENTICAL ARROWS, verified across all three twins:
    CF3CH2OM/ONa/OK: record 27 reactant, record 64 REAGENT, record 51 reactant.
    water on IV->V: record 22 reactant, record 59 REAGENT, record 53 reactant.
    hydrogen peroxide: record 26 reagent, record 63 oxidant.
    In each case the summary record is the odd one out. -> INCONSISTENT_SIBLING.
SB. IDENTIFIER FORM DIVERGES ON IDENTICAL ARROWS, verified:
    cyclohexane-1,3-dione is the SMILES O=C1CCCC(=O)C1 on records 23 and 29, but the bare
    name on records 60 and 66. Every other compound in records 60/66 is a SMILES, so the
    one name-only entry cannot be joined downstream. -> INCONSISTENT_SIBLING.
SC. REACTION_CLASS DIVERGES for one transformation, verified: the IV->VIII carbonylative
    esterification is other_cross_coupling on records 23, 60 and 45 but ester_formation on
    record 54. The notes disclose the divergence, which satisfies rule 8, but the gold still
    classes one reaction two ways. -> INCONSISTENT_SIBLING, both readings stand.
SD. OPTIONALITY / BRANCH STRUCTURE UNRECORDED, section-wide. [0011] line 86 包括如下的任意
    一个步骤 (any ONE of the following); line 145 或 between the (vi) and (vi') schemes;
    [0013] line 101 任意一个或两个以上 (any one or more) versus [0016] line 126 which requires
    the full set. So (v)/(v') and (vi)/(vi') are MUTUALLY EXCLUSIVE branches and (i)-(iv)
    are individually optional in one item but mandatory in another. Nothing in the schema
    records exclusivity or optionality; the ten records are linked only by precursor_step.
    This is the summary's principal legal content. -> NO_FIELD_EXISTS.
SE. Record 68 is_one_pot=false against an explicit either/or ([0020] line 147, [0022] line
    158). A boolean cannot hold "either", and false positively asserts the mode the patent
    permits. Same defect as CH on record 23. The notes admit it; prose is not captured.
SF. Records 59/61 notes claim records are numbered "in the order of the patent's own step
    labels (i) to (vi')", but step (v) has step_index 1 and step (i) has step_index 3, so
    the numbering is page order. The note misdescribes the data. -> minor, internal.
SG. Step aliases (v-1)/(v'-1), used by the patent itself at lines 320 and 352, are recorded
    in notes for (vi-1)/(vi'-1) on records 65-68 but nowhere on 59/60. -> inconsistent.
Verified clean in this range: 61, 62, 65.

### Examples 32-38 (agent 3) - re-verified by me, all confirmed
EA. RECORD 32 LOSES ALL THREE PRINTED TEMPERATURES. Patent line 428/433: 在20℃下 ... 在0℃
    进一步滴加 ... 控温不超过-5～10℃. Record has type=not_specified, all nulls. Record 51
    stores the byte-identical 控温不超过-5～15℃ construction as min -5 / max 15, proving the
    field can hold it. -> RANGE_LOST + MISSING + INCONSISTENT_SIBLING.
EB. pH 2-3 ANSWERED TWO WAYS for one phrase, verified: 调酸至pH为2～3 appears on records
    32, 33, 40, 41, 44; only record 33 stores workup.ph_target=2.0 (and that loses the 3).
    The other four store nothing. -> MISSING + RANGE_LOST + INCONSISTENT_SIBLING.
EC. RECORD 36 INTERNAL CONTRADICTION, verified: purification holds "filter cake washed twice
    with water and dried at 60 C" while tags carry purification:none. -> WRONG_VALUE.
ED. CONCENTRATION INCONSISTENT, verified: records 32 (20% CH3SNa) and 36 (23% H2O2) populate
    conditions.concentration; records 35 and 37 leave it null although the patent prints
    32% NaOH for both. The object also holds ONE concentration, so record 32's other three
    (5.1% HCl, 20% NaNO2, 32% NaOH) survive only inside its free-text sub-field.
    -> MISSING + NO_FIELD_EXISTS.
EE. GUARD PASSING ON ABSENCE (CLAUDE.md rule 6), verified on SEVEN records: 35, 36, 37, 38,
    40, 44, 45 all set cross_reference_unresolved=FALSE while the patent says only
    "HPLC conditions: same as Example 1" (line 445/460/472) or "same as Example 5"
    (line 495/516/529) and the record stores NONE of the referenced method. "We did not
    resolve it" is being recorded as "there is nothing to resolve". The full method, printed
    once at line 435-436 and once at 485-486 (column, mobile phase, the 3.7-4.1 mobile-phase
    pH range, flow rate, column temperature, wavelength, injection volume), plus Example 2's
    254 nm override at line 445, reaches no record. -> WRONG_VALUE + NO_FIELD_EXISTS.
EF. SCALE INCONSISTENT, verified: record 32 is the largest batch in the file (~1.8 kg of
    charges) and is labelled lab, while records 35 (100.75 g) and 36 (146 g) are pilot and
    record 37 (84 g) is lab. -> INCONSISTENT_SIBLING.
EG. analytics:nmr TAG MISSING on records 37 and 38, verified, although line 474 gives 1H and
    13C NMR figures for their product. Records 32, 35, 36, 39 all carry it. -> MISSING.
EH. Record 35 drops the 70 mL wash volumes that its sibling 37 records for its own 50 mL
    washes, and the 2 x 70 mL of NaOH used in washing reaches no quantity field.
EI. Record 32 identifier 重氮盐 untranslated (see BD) and is an in-situ intermediate never
    charged; product_name is "compound of formula (VI)" in English while every sibling uses
    the 式(N)化合物 form, which is why compounds.json holds that substance twice.
EJ. Record 32 is_one_pot=true, but line 433 says the diazonium solution is transferred to
    另一四口烧瓶, a SECOND four-necked flask. Both readings should stand.
EK. Record 34 section_type=comparative_example is an interpretation: the patent prints
    实施例11 and no 对比例 marker anywhere. Disclosed in notes, but the enum resolves a
    reading the patent does not make. Both readings.
EL. "About" is asserted away: 约8h, 约10h, 约12h, 10℃左右 are stored as bare exact values
    with no approximate qualifier anywhere in the schema.
EM. Record 34: both ligands recorded at 4.0 g each although line 548 joins them with 或 (or)
    and charges 4 g TOTAL. Nothing marks them exclusive, so summing mass_g yields 8 g from a
    4 g charge. -> NO_FIELD_EXISTS (alternative-set).
EN. Records 33 vs 34 encode "nothing here" two ways: byproduct_recovery null vs [], and
    selectivity.stereo null vs {type:null,value_pct:null}.
No index in 32-38 is defect-free. Cleanest 38, then 37; worst 32 and 33.
