# CN109678767A — Pathways audit note

Findings-only pass, 2026-09-04. Companion file: `pathways_findings.csv` (17 rows, all `open`).

## Record count and source

- `output/pathways.json` holds **14 records** — `len(json.load(open('output/pathways.json')))` = 14.
- Source of these pathways (read-only reference):
  `/Users/sathvik.k/Desktop/pipeline_for_patent/patent-extraction/runs/CN109678767A/output/relevant_output/gold/pathways.json`
- Verified myself: `len()` = 14 on both sides and the `pathway_uuid` sets are equal (set difference empty in both
  directions). The working copy is **not** byte-identical: it adds SMILES enrichment keys and canonicalises five
  identifier strings at the `ksm` / `intermediates` / `product` slots with five matching `compound_uuid` remaps.
  Excluding the five enrichment keys there are exactly 10 field diffs (5 `identifier`, 5 `compound_uuid`); every
  rename is nomenclature-only (`methanesulfonyl` -> `methylsulfonyl`, bracketed substituent form) and chemically
  equivalent, so the provenance row is logged at Info (finding 15). The enrichment did **not** introduce the
  dangling pointers or the coverage gap — both are present in the gold too.

## Target molecule

**Tembotrione** (环磺酮) — `.md` **line 37** (title 一种除草剂环磺酮的合成工艺), EN gloss line 38. Confirmed by the
abstract at line 41 and by the chemical name at line 90:
2-{2-chloro-4-methylsulfonyl-3-[(2,2,2-trifluoroethoxy)methyl]benzoyl}cyclohexane-1,3-dione. Not mesotrione and not
tefuryltrione — those appear at lines 90-93 only as *other* HPPD herbicides being compared against, and no pathway
record mistags them.

## Derived distinct-route count vs 14

Derived from `output/reactions.json` alone (45 records): group by `section_label`, chain on `precursor_step`, dedup
by ordered reaction-id set.

| grouping | result |
|---|---|
| multi-step chains (= routes) | **9** |
| lone single reactions (steps, not routes) | 2 |
| total groups | 11 |
| `len(pathways.json)` | **14** |

The 9 chains: Background `[3,4,5]`, `[6,7]`, `[8,9,10]`; Claims `[1,2a,2b,3a,3b,3c]`; Comparative Example
`[1,2a,2b,3a,3b]`; Example 1/2/3/4 each `[1,2a,2b,3a,3b,3c]`. Plus lone Background `[1]` and `[2]`. All 9 have
distinct ordered reaction-id sets, so none dedups away.

The 3-record excess over 11 is accounted for exactly: **2** `Summary of the Invention` records built on reaction
uuids that do not exist (finding 1 — `reactions.json` has no Summary section at all) plus **1** patent-level clone
of the Example 1 record (finding 4).

What the patent actually describes is fewer still: **one** invention route (restated at `.md` 47-59 claims,
115-128 summary prose, 152 scheme, 181-201 Ex 1, 211-229 Ex 2, 240-257 Ex 3, 268-286 Ex 4), **one** Comparative
Example route (`.md` 298-312, distinct chemistry: Br2/CCl4, NaOMe/DMF, MeCN), and **five** prior-art references
(`.md` 96-97, 98-99, 100-101, 102-103, 107-108) of which only three are described as chains.

## Top 3 issues

1. **8 dangling step pointers (High).** Both `Summary of the Invention` pathway records reference `reaction_uuid`s
   absent from `reactions.json`, which contains no Summary section. Records 481032f2 and 234c14bb are unverifiable
   against the reaction layer.
2. **17 orphan synthetic reactions (High).** `reactions.json` was re-split to transformation level on 2026-09-01
   (`split:transformation-level-gold-2026-09-01`), but `pathways.json` still carries the pre-split roll-up and cites
   only the terminal sub-step of each numbered step. The split is a deliberate gold convention and must not be
   reverted — the defect is that `pathways.json` was never regenerated after it.
3. **15 steps whose payload contradicts `reactions.json` for the same uuid (High).** A rolled-up pathway step keeps
   the uuid of its last sub-step but rewrites `reactant_names`, `reaction_id` and `step_label` to describe the whole
   roll-up, so joining the two files on `reaction_uuid` yields two different reactions for one id.

## Clean checks (no findings invented)

Zero non-synthetic operations counted as routes (0 of 45 reactions, 0 of 36 steps have `non_synthetic=true`); no
route stitched across unrelated sections; every route terminates at tembotrione (12 records) or at the declared
intermediate methyl 2-chloro-3-(bromomethyl)-4-(methylsulfonyl)benzoate (2 records); no wrong target tag; all 47
`ksm`/`intermediates`/`product` entries resolve in `compounds.json` by `compound_uuid` with 0 identifier
mismatches; no duplicate uuids in either file.

## Modification statement

**No file was modified.** `pathways.json`, `reactions.json`, `compounds.json` and
`input/CN109678767A-enriched-numbered.md` were opened read-only. The only files written by this pass are
`audit/pathways_findings.csv` and this note; nothing else in `audit/` was touched.
