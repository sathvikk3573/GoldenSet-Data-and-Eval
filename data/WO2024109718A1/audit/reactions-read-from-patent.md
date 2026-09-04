# WO2024109718A1 - Prompt 1 independent reaction identification audit
Source read: runs/WO2024109718A1/input/WO2024109718A1-enriched-numbered.md (1121 lines, all read)
Extraction outputs NOT opened. Disclosure: directory listing exposed output SVG filenames
(compound names incl. tembotrione) before reading; no reaction records or counts were seen.

## Compound key (roman numerals per patent)
- VI  = 3-chloro-2-methylthiotoluene        CSc1cccc(Cl)c1C          QNMYJM
- I   = 4-bromo VI (Ar-Br)                  CSc1ccc(Br)c(Cl)c1C      VEKIRH
- II  = sulfone of I                        Cc1c(S(C)(=O)=O)ccc(Br)c1Cl  KHLKTM
- III = benzylic bromide of II              CS(=O)(=O)c1ccc(Br)c(Cl)c1CBr GXQKMZ
- IV  = trifluoroethoxymethyl ether         CS(=O)(=O)c1ccc(Br)c(Cl)c1COCC(F)(F)F VHLAIV
- V   = benzoic acid (carbonylation)        ...C(=O)O...             YGMJOC
- VII = acid chloride                       ...C(=O)Cl...            YGWVWX
- VIII= enol ester with 1,3-cyclohexanedione OBAMHT
- tembotrione (环磺酮)                       IUQAXC
- background-only: 2,6-dichlorotoluene DMEDNT; 3-chloro-2-methylaniline ZUVPLK;
  MeS-acetophenone ZSHNSX; sulfone-acetophenone KLJPFW; acid RRFGGU; Me ester BBWCBP;
  CH2Br ester YQYVOG; CF3CH2O ester NOVXMM; 1,3-cyclohexanedione HJSLFC

## Mechanical counts (verified by script)
- 105 IMAGE_EXTRACT blocks: 83 with reactions, 22 molecules-only
- 166 drawn reaction steps, 22 distinct reactant->product pairs
- 14 claims; 11 examples ([0106]-[0146])
- Draw counts per pair: VI->I 19, I->II 19, II->III 19, III->IV 19, IV->V 16,
  VIII->temb 16, IV->VIII 12(+3 with dione as reactant), V->VII 8, VII->VIII 8,
  aniline->VI 7, IV->temb 1 (Ex10 one-pot), V->NONE 2 / NONE->VIII 2 (vision artifact,
  L141+L151 (vi) scheme, VII dropped), background pairs 2 each, dichlorotoluene->VI 1.

## Unique invention transformations (described entries)
N1  aniline -> VI, NaNO2/HCl then CH3SNa (per CN106631941A): L253-254; routes L405,407,414,416; Ex1 L426
N2  (i)  VI + Br2 -> I: L103,116,128s1,135s1,237,241,379,392; claims L576s1,580,597s1,601s1,639,651
N3  (ii) I + H2O2 (Na2WO4, AcOH) -> II: L108,118,128s2,135s2,258,262,381,394; claims L576s2,589,597s2,601s2,641,653
N4  (iii) II + Br-agent (Br2/NBS/HBr+H2O2, pref Br2) -> III: L110,120,124,128s3,135s3,137,279,283,287,386,396,418; claims L576s3,585,591,597s3,601s3,603,643,655,664
N5  (iv) III + CF3CH2OM (Na/K) -> IV: L112,122,124,128s4,135s4,137,297,304,306,388,398; claims L576s4,587,593,597s4,601s4,603,645,657,666
N5a reagent prep: NaH/KH|NaOMe/KOMe|NaOH/KOH + CF3CH2OH -> CF3CH2OM: L314-315; performed inside Ex5 L483
N6  (v/v-1) IV + CO + H2O (Pd/phosphine/base) -> V: L6,91,97,318,362,373; L141s1,151s1,612s1,618s1; claims L555,561; conds L320-338
N7  (v'/v'-1) IV + CO + 1,3-cyclohexanedione -> VIII: L8,93,99,341,367,375,416s6; L143s1,153s1,614s1,620s1; claims L557,563
N8  V + SOCl2 -> VII: L350s1(unlabeled),405s7,414s7(SOCl2),612s2,618s2
N9  VII + dione (Et3N) -> VIII: L350s2,405s8,414s8,612s3,618s3
N10 VIII -> tembotrione (acetone cyanohydrin cat.): L348,350s3,405s9,407s7,414s9,416s7,143s2,151s4,153s2,612s4,614s2,618s4,620s2
N11 one-pot option IV -> temb (N7+N10 telescoped, no VIII isolation): L147,158,354,535,610,622

## Background described entries (prior art, separate by cited document)
B-route-1 DE19846792A1, L63-65, 11 steps:
  1 dichlorotoluene+CH3SNa->VI-analog; 2 AcCl/AlCl3 F-C; 3 H2O2; 4 NaClO haloform;
  5 MeOH esterif.; 6 NBS; 7 CF3CH2ONa; 8 NaOH hydrolysis; 9 (COCl)2; 10 dione; 11 rearr.
B-route-2 CN200510030163.5, L69-74, 11 steps:
  1 aniline NaNO2/CH3SNa/HCl; 2 AcCl (no AlCl3 drawn); 3 H2O2; 4 NaClO; 5 MeOH;
  6 Br2; 7 CF3CH2ONa; 8 NaOH; 9 SOCl2; 10 dione; 11 rearr.
[0004] L67 criticises route 1; [0006] L76 criticises route 2 - described as working prior art, in scope.

## Performed runs (15)
Ex1  L424-438  aniline->VI (NaNO2/HCl 0C; CH3SNa/NaOH; pH2-3)      1 run
Ex2  L439-448  VI+Br2/DCM 10C -> I                                  1 run
Ex3  L449-463  I+H2O2 23% / Na2WO4.2H2O / AcOH 80C -> II, mp138-140 1 run
Ex4  L464-475  II->III: M1 Br2/BPO/DCE 60C (mp148-150); M2 NBS/BPO/CCl4 60C   2 runs
Ex5  L476-488  III+CF3CH2ONa(in situ NaH+TFE/THF) -> IV, mp80-82    1 run
Ex6  L489-498  IV+CO/H2O PdCl2+dppb dioxane 80C 12h -> V 81.13%     1 run
Ex7  L499-507  same but dppp/THF -> V 67.57%                        1 run
Ex8  L508-517  V+SOCl2->VII; VII+dione/Et3N->VIII; VIII+ACH 60C->temb 76.59%  3 runs
Ex9  L518-532  IV+CO+dione Pd(A-taPhos)2Cl2: M1 dioxane 80%; M2 glycol diethyl ether 71.63%  2 runs
Ex10 L533-545  one-pot IV+CO... then ACH -> temb 64.67%, mp120-122  1 run
Ex11 L546-549  IV+CO+dione, SPhos or Xantphos ligand, <10% unsatisfactory     1 run (flagged)

## Discrepancies / flags
F1 Ex10 text charges (L537) omit 1,3-cyclohexanedione; drawn scheme (L535) includes it. Record both.
F2 (vi) scheme draws at L141,L151: V->(empty) + (empty)->VIII; VII dropped by vision. VII exists (8 other draws + Ex8).
F3 L414 step9 conds "acetone cyanohydrin" vs L416 step7 "[illegible]"; L510 step3 "[illegible]" - text L514 supplies ACH.
F4 Ex11 performed but <10% yield, patent calls it unsatisfactory - include, flag as negative/comparative run.
F5 Background route-1 step-1 product drawn = VI (same InChI) though from dichlorotoluene; SMILES loses one Cl - check in later pass (CSc1cccc(Cl)c1C from Cc1c(Cl)cccc1Cl + CH3SNa is plausible: SNAr of one Cl).
F6 Claim numbering oddity: claim 6 depends on claim 4 (a solvent claim) rather than 5; textual, no reaction impact.
F7 Ex5 in-situ alkoxide prep (N5a) is a real performed transformation inside Ex1 run - decide whether gold carries it as separate entry or as condition detail.

## Totals (DECIDED with user 2026-09-02)
Described entries: 22 background + 10 invention (N1-N10) + N5a alkoxide prep + N11 one-pot = 34.
Performed runs: 15.
GRAND TOTAL: 49 entries. User decision: N5a and N11 are counted as their own
described entries because the patent states them explicitly ([0080] L314 and
[0020]/[0093] L147/L354) - data stated in the patent must be captured in the gold.

## Prompt 2 reconciliation result (2026-09-02)
reactions.json holds 79 records. ALL 79 map to the 49-entry list; 0 missing, 0 extra.
Count difference (79 vs 49) is structural, not substantive:
- file keeps one record PER SECTION per transformation (Claims + Summary + Description
  retellings = up to 6 records for one transformation, the twin structure);
- file additionally keeps within-section duplicates where a claim recites a step both
  standalone and inside a composite route ((v)=22 vs (vi)A1=28; (v')=23 vs (vi')A1=32;
  (vi)A4=31 vs (vi')A2=33; summary mirrors 67/73, 68/77, 76/78) - deliberate, documented
  in notes;
- N5a described = record 54; N5a performed folded into Ex5 record 41 via one_pot_steps;
- N11 one-pot: no separate record; carried as is_one_pot=true on Ex10 (35) + one-pot
  statements in notes of (vi') records - mode-not-record convention, matched by identity.
Prior flags found already handled IN the file: F1 Ex10 dione (reagent_drawn_not_written),
F2 VII artifact (a1_missing_compound on 74/75; VII recovered), F3 illegible ACH (captured
via L348), F4 Ex11 negative (comparative_example + missing_product, both readings in notes).
NEW minor observations (flag only, not yet fixed):
 M1 record 42 (Ex6) reactant_names omits water while sibling 43 (Ex7) lists it; water IS
    in 42's compounds as role=reagent - derived-field labeling inconsistency only.
 M2 records 64/65: compound VII carried as raw SMILES string in product/reactant name
    (scheme L405 draws it without a name) - cosmetic.
 M3 records 50/58: aniline reactant name is raw SMILES Cc1c(N)cccc1Cl - cosmetic.
 M4 record 42 catalyst "PdCl₂" (unicode subscript) vs 43 "PdCl2" - cosmetic.
Provenance spot-checks (5 records) all match my line map.

## GOLD MERGE EXECUTED (2026-09-02, user-authorized)
79 -> 69 records. Merged (strict-subset verified): Descr Scheme Steps 1-6 -> Steps 2,1,3,4,5,7;
Claims (vi)A1 -> (v); Claims (vi')A1 -> (v'); Summary (vi)A1 -> (v); Summary (vi')A1 -> (v').
5 precursor repoints. Fixes: Ex6 reactant_names += water; provenance L348/350 added to Scheme
Steps 7/8. Kept deliberately: route-convergence twins (31/33-style), cross-section twins,
background A/B pairs, all runs. Logged in output/GOLDEN-DATASET-FINDINGS.xlsx (R-001..R-021).
Gold copies synced. Export CSV/JSON + manifest NOT regenerated (stale, noted in NOTES.md).
Final: 69 = 22 background + 10 claims + 10 summary + 12 description + 15 runs.
