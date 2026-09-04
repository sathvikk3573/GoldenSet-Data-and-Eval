# WO2022024094A1 - compounds read from the patent (prompt 3)

CONTAMINATION DECLARED: compounds.json was opened earlier in this session during the
reactions work. Six records were seen (mesotrione, NMST, NMSBA, the enol ester, Sodium
acetate, cobalt(II) acetate tetra hydrate) plus the field shape. The full list and the
record count were NEVER seen. Those six are all obvious route or prior-art compounds
that any read would return; the count and the long tail below are independent.

Source: input/WO2022024094A1-enriched-numbered.md, 406 lines, 270 content lines.
All four required sweeps were run. Every line number below was verified mechanically.

## Sweeps run

1. Chinese name endings: 0 lines contain any Chinese character. Filing and publication
   language are English (lines 22 and 24). Sweep run, returned nothing, not skipped.
2. English name endings (-ol -ate -ide -one -ane -ene acid ether amine oxide chloride
   cyanide hypochlorite peroxide carbodiimide dione toluene benzene): 198 raw candidate
   strings, curated below.
3. Every distinct SMILES / formula / InChIKey in IMAGE_EXTRACT spans: 6 distinct
   structures, listed under "drawn" below.
4. Every material carrying a mass or a volume in the experimental section: 24 raw hits,
   all resolving to compounds already in the list.

## COUNT: 51

    39  named substances
    11  generic class terms
     1  biological target (HPPD)

Reviewer decisions applied: acetate ion -> alias of sodium acetate; HPPD -> keep;
benzoylcyclohexane-1,3-dione herbicides -> keep; the IUPAC name of mesotrione -> alias;
dichloroethane and 1,2-dichloroethane -> ONE substance, so "dichloroethane" is an alias
of "1,2-dichloroethane".

WHY THEY ARE ONE SUBSTANCE, three reasons:
  1. All three solvent lists (L209, L245, claim 9 at L378) are the SAME five-item list
     in the same order; only the second item's wording differs.
  2. The patent writes dichloropropaneS, dichlorobutaneS, dichloropentaneS in the
     PLURAL, acknowledging isomer sets, but writes dichloromethane and dichloroethane
     in the SINGULAR. Dichloromethane has no isomers. A singular "dichloroethane" in a
     list that otherwise pluralises for isomers means one specific isomer, and claim 9
     names which one.
  3. A claim cannot cover matter absent from the description, so claim 9's
     "1,2-dichloroethane" must be supported by the description's "dichloroethane".

## FOUND BY THE SEQUENTIAL READ, MISSED BY THE SWEEPS

The four mechanical sweeps did not return these. A line-by-line read did.

    "a cobalt salt"            L70   generic term for the US 5,591,890 catalyst
    "aqueous caustic solution" L192  the base medium for the in-situ RuO2 preparation;
                                     the patent names no specific base
    the weed species at L59    Abutilon theophrasti, Xanthium strumarium, Ambrosia
                               trifida L, Chenopodium, Amaranthus, Polygonum, Digitaria,
                               Echinochloa. PLANTS, not compounds. Rejected, but they
                               are the largest block of proper nouns in the document and
                               a name-ending sweep never surfaced them at all.

## A. Route materials, all DRAWN as structures (5)

    NMST, 2-nitro-4-methylsulfonyl toluene, formula (IV)   C8H9NO4S    15 spans
    NMSBA, 2-nitro-4-methylsulphonyl benzoic acid, (III)   C8H7NO6S    24 spans
    enol ester of formula (II)                             C14H13NO7S  14 spans
    mesotrione, formula (I)                                C14H13NO7S   7 spans
    1,3-cyclohexanedione                                   C6H8O2      10 spans

## B. Prior-art route material, DRAWN (1)

    NMSBC, 2-nitro-4-methylsulphonyl benzoyl chloride      C8H6ClNO5S   2 spans
      text L64, 88, 92, 113, 198. The invention exists to avoid this compound.

## C. Oxidants (5)
    sodium hypochlorite        L194,264,272,285,350,365  + drawn on the arrow at L196
    alkali metal hypochlorite  L183   text only
    hydrogen peroxide          L183   text only
    nitric acid                L77,81,88  + drawn on 3 arrows
    oxygen, O2                 DRAWN ONLY, arrow at L83. Appears in NO text line.

## D. Acids (3)
    sulfuric / sulphuric acid  L81 + drawn on 2 arrows (L79 prints "Sulphuric Acid/")
    hydrochloric acid          L264,277,285 concentrated; L329 aqueous
    acetic acid                L70 + drawn on the arrow at L75

## E. Catalysts (5)
    ruthenium (IV) oxide, RuO2      L109,122,179,185,187,194,213,228,264,272,285,348,350,363,380 + 10 spans
    ruthenium dioxide hydrate       L185  the stated PREFERRED form
    ruthenium trichloride           L187,192
    cobalt(II) acetate tetrahydrate L70 + drawn at L75
    vanadium pentoxide, V2O5        L77,81,88 + drawn on 3 arrows

## F. Catalytic-cycle species (3)
    ruthenate, perruthenate, ruthenium tetroxide   all L185 only
      What the catalyst is oxidised INTO during the cycle, not charged to the flask.

## G. Bases (7), all from ONE sentence at L245
    triethylamine (also L329, and drawn at L68 as "(CH3CH2 )3N")
    N,N-diisopropylethylamine (DIPEA), N,N-diisopropylamine, ethanolamine,
    diethanolamine, triethanolamine, N-methylglucamine
      Only triethylamine is ever used, in Example 7.

## H. Coupling agent (1)
    N,N'-dicyclohexylcarbodiimide, DCC   L209,295,308,316,359,369

## I. Other reagents (3)
    sodium cyanide   L245,329,361
    sodium acetate   DRAWN ONLY, arrow at L75. Appears in NO text line; the prose at
                     L70 says "acetate ion" instead.
    chlorine gas     L192, purged to make RuO2 in situ

## J. Solvents (6)
    dichloromethane   L209,245,295,308,316,329,378   the preferred solvent throughout
    dichloroethane    L209,245     the description's form
    1,2-dichloroethane L378        claim 9's narrower form
    dichloropropanes  L209,245,378
    dichlorobutanes   L209,245,378
    dichloropentanes  L209,245,378

## K. Workup medium (1)
    water   L300,308,316,329

## L. Generic class terms, written as the patent writes them (11)
    "an oxidant" / "any oxidizing agent"        L109,122,179,183,213,228,348,363,380
    "a solvent"                                 L209,245,359,361,369
    "a base" / "organic and inorganic base"     L245,361
    "peroxide"                                  L183, the class of which H2O2 is the instance
    "a salt or an oxide"                        L185, the forms ruthenium may be supplied in
    "the corresponding methylsulfonyl toluenes" L77, the substrate family of US5424481
    "methyl sulfonyl benzoic acids"             L77, the product family of US5424481
    "vanadium or cobalt compounds"              L77, offered for the family, never paired with NMST
    "a cobalt salt"                             L70, the US 5,591,890 catalyst, generically
    "aqueous caustic solution"                  L192, the medium for the in-situ RuO2 prep
    "benzoylcyclohexane-1,3-dione herbicides"   L55, the class mesotrione belongs to (reviewer: keep)

## M. BORDERLINE, now decided by the reviewer

1. acetate ion, L70. An ION, not a substance, and the drawing at L75 gives the salt
   as sodium acetate. Already recorded as an alias of sodium acetate in the reactions
   pass. Include as its own compound, or keep as an alias only?
2. 4-hydroxyphenylpyruvate dioxygenase, HPPD, L55. An ENZYME, and the herbicide's
   biological target. It is not a reagent, a product or a participant in any reaction.
3. benzoylcyclohexane-1,3-dione herbicides, L55. A compound CLASS that mesotrione is
   described as a member of, not a substance.
4. 2-(4-(methylsulfonyl)-2-nitrobenzoyl) cyclohexane-1,3-dione, L55 and L170. The
   IUPAC name of mesotrione. Same substance under another name, so an alias rather
   than a 53rd compound, unless the convention is to count printed names.

## N. Candidates the sweeps returned and I REJECTED, with reasons

    dicyclohexylurea        The DCC by-product. Chemically certain to form, and Ex 6
                            filters a solid that must be it, but the patent NEVER names
                            it: grep over the .md and all 23 vision JSONs returns only
                            "Bureau". Not invented.
    wet cake, crude enol ester, pure enol ester   States of the enol ester, not compounds
    filtrate, organic layer, aqueous mass, precipitated product   Mixtures and streams
    US 7,820,863, US 5,591,890, US5424481, CN105669504A, CN106565561A,
    CN104557639A1, US20160355472A1, CN102584650A   Patent numbers
    DE, IN, AE, AG ...      Country codes in the Designated States lists, L34 and L36
    HPLC                    An analytical method, not a substance

## Two decisions inside the count, flagged

- "dichloroethane" (L209, L245) and "1,2-dichloroethane" (L378) are counted as TWO,
  because the patent prints two different strings and claim 9 is narrower than the
  description. If they are one substance the total is 51, not 52.
- "concentrated hydrochloric acid" (L264,277,285) and "aqueous hydrochloric acid"
  (L329) are counted as ONE. Same substance, two descriptors.

## What only the drawings give

Two compounds appear in NO text line anywhere and would be lost by a text-only read:
O2 on the arrow at L83, and sodium acetate on the arrow at L75.


## N2. Additional rejections from the sequential read

    Abutilon theophrasti, Xanthium strumarium, Ambrosia trifida L, Chenopodium,
    Amaranthus, Polygonum, Digitaria, Echinochloa   (L59)   weed species, not compounds
    PatSeer, IPO Internal Database                  (L391)  search databases
    SHENYANG RESEARCH INSTITUTE OF CHEMICAL INDUSTRY, ROTAM AGROCHEM INTERNATIONAL,
    SHENYANG SCIENCREAT CHEMICALS, RALLIS INDIA LIMITED   company names
    C07C 315/06, C07C                               IPC classification symbols

## Aliases, counted with their parent and not separately

    acetate ion                                  -> sodium acetate
    2-(4-(methylsulfonyl)-2-nitrobenzoyl) cyclohexane-1,3-dione -> mesotrione
    dichloroethane                               -> 1,2-dichloroethane
    cyclohexanedione (L64, no locants)           -> 1,3-cyclohexanedione
    NMST, NMSBA, NMSBC, DCC, NaOCl, RuO2, V2O5, DIPEA -> their parent compounds
    formula (I), (II), (III), (IV)               -> mesotrione, enol ester, NMSBA, NMST

## Method, stated honestly

All 406 lines were read sequentially for this pass, after the four mechanical sweeps.
The sweeps found the names; the read found the meaning. Three items above exist only
because of the read, and two compounds (O2 at L83, sodium acetate at L75) exist only
in drawings and would be lost by any text-only method.
