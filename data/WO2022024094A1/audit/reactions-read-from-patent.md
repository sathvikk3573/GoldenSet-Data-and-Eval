# WO2022024094A1 - Pass 1 reaction audit (independent, reactions.json NOT opened)

Source: runs/WO2022024094A1/input/WO2022024094A1-enriched-numbered.md, 406 lines, 23 pages.
All pages are scans; every line comes from the vision pass.

## Mechanical counts
- IMAGE_EXTRACT blocks: 31 (29 reaction, 2 molecule-only)
- Drawn reaction steps inside those blocks: 31 (L66 and L68 each carry 2 steps)
- Distinct drawn transformations: 5
    15x NMST -> NMSBA
     9x NMSBA + 1,3-CHD -> enol ester
     5x enol ester -> mesotrione
     1x NMSBA -> NMSBC        (prior art only, L66)
     1x NMSBC + 1,3-CHD -> enol ester (prior art only, L68)
- Example headings: 7 (L260, 268, 281, 291, 304, 312, 320)
- Example paragraphs: [0047]..[0053] = 7

## Yield reproduction (mass basis vs printed %)
Ex4 68.0 / 68 | Ex5 64.3 / 65 | Ex6 65.9 / 66 | Ex7 85.0 / 85   -> all consistent
Confirms enol ester and mesotrione are isomers, both C14H13NO7S, MW 339.32.

## Patent-side molar-arithmetic slack (patent's own figures, not defects to fix)
Ex1 61.2 g NMST -> computed 0.284 mol, printed 0.278 (2.2% low)
Ex3 20 g NMST   -> computed 0.093 mol, printed 0.088 (5.3% low)
All others within 3%.

## Defect found: drawing bound to the wrong example
L287 carries NMSBA + 1,3-CHD -> enol ester, sitting between [0049] (Example 3, L285)
and Example 3's own yield line (L289). Example 3 makes NMSBA, not the enol ester.
vision/p16.json drawings[0] anchors it between_markers ['[0049]','[0050]'] and
paragraphs[7].notes says the Example 4 title line is printed "immediately above the
reaction scheme". The scheme belongs to Example 4. The .md placed it ~4 lines early.
Nearest-preceding-paragraph binding therefore attaches an enol-ester scheme to Example 3.

## Count under prompt rule 2 (dedupe repeat mentions): 19
  prior art 8 | invention generic 3 | worked example runs 7 | catalyst prep 1

## Count under the reference-run per-section convention: ~36
  (runs/CN104292137A/output/reactions.json ids are Claims_Step N,
   Example 1_Step N, Summary of the Invention_Step N, Summary ... Scheme Step N,
   i.e. the same chemistry is recorded once per section)
  Background 8 | Summary 5 | Detailed description 9-10 | Examples 7 | Claims 7

## Borderline / excluded, flagged not dropped
1. [0033] L185  RuO2 + hypochlorite -> ruthenate / perruthenate / RuO4.
   Catalyst redox mechanism. No scheme, no example. EXCLUDE as mechanism.
2. [0034] L187 + L192  RuCl3 + aqueous caustic + Cl2 purge -> RuO2 in situ.
   NOW INCLUDED at the user's instruction, as entry 19, tagged prophetic /
   not-performed / off-route. Reason it was borderline: stated as "can be formed",
   no example performs it, and it makes catalyst rather than anything on the
   mesotrione route.
3. HCl acidification to pH < 3 in Ex 1/2/3 and Ex 7.
   Liberates the free acid / free dione from the salt formed in the basic medium.
   The patent draws one arrow and titles the examples "Synthesis of X from Y",
   so treat as workup of the parent reaction, not a separate step. DECISION, not silent.
4. Dicyclohexylurea, the DCC by-product, is never named anywhere in the patent.
   Ex 6 filters a solid off; that is almost certainly DCU. Do not invent the name.

Nothing in this patent is described as not working, so there is no rule-8 exclusion
of that kind.


## DCU check (asked for explicitly) - CONFIRMED ABSENT
grep for urea / dicyclohexylurea / by-product over the .md returns only "Bureau"
(L8, WIPO International Bureau). grep over all 23 vision/p*.json returns only
"ureau". Dicyclohexylurea is named nowhere in this document. Ex 6 L316 filters a
solid under vacuum and that solid is almost certainly DCU, but the patent never
says so. Do NOT add it to the gold as a named by-product. If it is recorded at
all it must be as an inference with that provenance, never as patent text.

## Dedup and conflicting conditions - the rule actually applied
Dedup is on reaction IDENTITY (reactants -> product) ONLY. Condition VALUES are
never merged. Where sections differ, all variants are carried with provenance.
CLAUDE.md hard rule 8: record disagreement, never resolve it silently.

Three kinds of difference, handled differently:

(a) SCOPE LADDER, not disagreement. Generic -> preferred -> performed.
    oxidant:  "an oxidant" (claims 1,5,10)
           -> "alkali metal hypochlorite, or peroxide such as H2O2" ([0032] L183)
           -> "sodium hypochlorite" ([0035] L194; claims 2,6)
           -> NaOCl 500 mL / 0.745 mol (Ex 1 L264)
    Keep every rung with its section. The top rung is claim breadth; the bottom
    rung is what was performed. Collapsing to one destroys both.

(b) GENUINE TEXTUAL DIVERGENCE between sections. One found:
    step B solvent list, [0037] L209 says "dichloroethane"
                         claim 9  L378 says "1,2-dichloroethane"
    The claim is narrower than the description (description also covers 1,1-DCE).
    Two values, two provenances. Never averaged to one.

(c) CONTRADICTION proper. None in this patent.

## Derived facts the patent never states (record as inference, with method)
- Examples 1-3 "ruthenium oxide" is ANHYDROUS RuO2, MW 133.07. The printed moles
  fit only the anhydrous MW: 1.5 g -> 0.0113 (printed 0.011); 0.5 g -> 0.0038
  (printed 0.004). Monohydrate gives 0.0099 and 0.0033, dihydrate 0.0089 and
  0.0030. So the examples do NOT use the RuO2 hydrate that [0033] L185 says is
  preferred. Description-preferred form and example-used form disagree.
- NaOCl strength is never stated but is constant across all three runs:
  Ex1 0.745/0.500 = 1.490 M, Ex2 0.149/0.100 = 1.490 M, Ex3 0.222/0.150 = 1.480 M.
  About 1.49 mol/L, roughly 11% w/v.

## DEDUP RULE - CONFIRMED WITH THE USER
Discriminator is DESCRIBED vs PERFORMED, plus reagent-system identity.
- Same reaction described in abstract / summary / detailed description / claims
  -> ONE record. Variant wording becomes fields on that record, never new records.
- Different examples running the same reaction with their own charges, conditions,
  yields and purities -> ONE RECORD EACH. Never merged.
- Prior-art citations with different reagent systems and different source documents
  -> ONE RECORD EACH (5 separate NMST->NMSBA oxidations). Confirmed by user:
  where the patent gives distinguishing data, that data is why the record exists.
FINAL COUNT 19 = 8 prior art + 3 invention generic + 7 example runs + 1 catalyst prep.

## RANGES - record every one as min / max
Full verified table in ranges.tsv (48 values, all confirmed verbatim at cited line).
Schema rules that follow from them:

1. OPEN BOUNDS HAVE ONLY ONE SIDE. Never invent the other.
   ">95%" is min=95, max=null. Writing max=95 or max=100 is fabrication.
   19 of the 48 values are open on one side.
2. pH IS THE ONE INVERTED FIELD. "pH < 3" is min=null, max=3.
   Appears 4 times (Ex 1, 2, 3, 7). A parser that takes the first number as min
   inverts every one of them.
3. NESTED RANGES MUST KEEP ALL TIERS. [0035] L194 gives three for step A time:
   broad "more than 2 hours" (2, null); preferred "about 2 to about 8 hours" (2, 8);
   most preferred "about 6 to about 8 hours" (6, 8). Keeping only the narrowest
   loses claim breadth; keeping only the broadest loses the teaching.
4. "about" is defined at [0023] L151 as "+/- 5 of the specific figure preceding
   the term". Whether that is +/-5 absolute or +/-5 percent is not stated.
   Do NOT compute a numeric envelope from it. Record the raw phrase.
5. Point values get min = max (prior art US5424481, 145 C).

## CROSS-RECORD COMPARISON (siblings lined up, per "compare like against like")
NMSBA runs, equivalents from the patent's own printed moles:
        NaOCl eq   RuO2 mol%   HCl eq   yield   purity    mp
  Ex 1      2.68         4.0     4.42     70%     >95%   210-214
  Ex 2      6.48        17.4     7.39     67%     >92%   207-210
  Ex 3      2.52         4.5     5.80     68%     >95%   210-214
  -> Ex 2 IS THE OUTLIER RUN. 2.4x the NaOCl and 4x the catalyst loading of its
     two siblings, and it returns the worst purity, lowest mp and lowest yield.
     Invisible one record at a time. Record Ex 2 as-is; do not normalise it.

Enol-ester runs:
        CHD eq  DCC eq  DCM mL/g  yield   mp        order of addition
  Ex 4    1.00    1.09       5.9    68%   158-163   dione first, then DCC
  Ex 5    1.00    1.00      10.0    65%   152-158   DCC FIRST, then dione
  Ex 6    1.10    1.00       4.0    66%   157-163   dione first, then DCC
  -> Ex 5 is the only reversed addition order and it is lowest on both mp and
     yield, and the only one called "off white" where Ex 4 is "white".
     Consistent gradient. This is why Ex 4/5/6 must stay three records.
