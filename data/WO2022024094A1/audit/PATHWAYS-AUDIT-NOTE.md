# WO2022024094A1 — Pathways (Routes) Audit Note

Findings-only pass, 2026-09-04. Companion file: `pathways_findings.csv` (12 rows).

## Record count and provenance

- `output/pathways.json` holds **16 records**.
  Source expression: `len(json.load(open('output/pathways.json')))` = 16.
- Source of record (read-only reference):
  `/Users/sathvik.k/Desktop/pipeline_for_patent/patent-extraction/runs/WO2022024094A1/output/relevant_output/gold/pathways.json`
- Verified against that gold copy: same count (16 == 16) and identical `pathway_uuid` set
  (`set(working) == set(gold)` is True, 0 records in either difference).
- A recursive leaf-by-leaf diff over all 16 records reports differing field names exactly
  `['molecular_formula', 'molecular_weight', 'smiles', 'smiles_source']` and **0 non-enrichment
  differences**. `compound_uuid` did *not* diverge: the re-derived identifiers reproduce gold
  exactly. Logged as finding #12 (Provenance / Info).

## Target molecule

**Mesotrione** — `.md` line **38**: "(54) Title: PROCESS FOR PREPARATION OF MESOTRIONE AND ITS
INTERMEDIATES". Corroborated at line 40 (abstract), line 55 ("Mesotrione is chemically known as
2-(4-(methylsulfonyl)-2-nitrobenzoyl) cyclohexane-1,3-dione") and line 348 (claim 1).
Declared intermediates: NMSBA (2-nitro-4-methylsulphonyl benzoic acid), the enol ester of formula
(II), and — in the prior art only — NMSBC (2-nitro-4-methylsulphonyl benzoyl chloride).
No tembotrione / tefuryltrione / sulcotrione content in this patent.

## Derived distinct-route count: 13 (vs `len(pathways.json)` = 16)

Derived from `output/reactions.json` alone (20 rows), grouped by section, chained
product -> reactant via `precursor_step` / name / SMILES, then deduped by ordered reaction-id set:

| # | Route | Steps |
|---|---|---|
| 1 | Prior art, US 7,820,863 (.md L64): NMST -> NMSBA -> NMSBC -> enol ester (II) -> mesotrione | Background 1-4 |
| 2 | Prior art, US 5,591,890 (.md L70): NMST -> NMSBA, Co/AcOH/NaOAc | Background 5 |
| 3 | Prior art, US 5,424,481 (.md L77): NMST -> NMSBA, H2SO4/HNO3/V2O5 | Background 6 |
| 4 | Prior art, CN105669504A (.md L81): NMST -> NMSBA, H2SO4/HNO3/O2/V2O5 | Background 7 |
| 5 | Prior art, CN106565561A (.md L88): NMST -> NMSBA -> NMSBC | Background 8 -> 9 |
| 6 | Invention, as described: NMST -> NMSBA -> enol ester (II) -> mesotrione (merged record) | Summary 1 -> 2 -> 3 |
| 7-9 | Invention, as performed: NMST -> NMSBA, 70 / 67 / 68 % (.md L264 / L272 / L285) | Examples 1, 2, 3 |
| 10-12 | Invention, as performed: NMSBA -> enol ester (II), 68 / 65 / 66 % (.md L295 / L308 / L316) | Examples 4, 5, 6 |
| 13 | Invention, as performed: enol ester (II) -> mesotrione, 85 % (.md L329) | Example 7 |

Excluded with reason: `Detailed Description of the Invention_Step 4`
(`57582eff-410c-54eb-b80f-3ab143ff6a97`), the in-situ RuO2 catalyst preparation from RuCl3 + Cl2
(.md L187/L192, [0034]) — makes catalyst, not NMSBA / the enol ester / mesotrione. Not a route
and not a route step.

**Reconciliation of 13 vs 16.** The one described invention route is held four times in
`pathways.json` — `c3ba1294` (Summary), `80fafd41` (Detailed Description), `e6c1deb1` (Claims) and
`a213ad3c` (patent scope) — where `reactions.json` now holds it once as a merged chain.
13 + 3 surplus records = 16, exactly. No route is missing from the derived count and none is
invented. Separately, prior-art route 5 exists as a pathway but is truncated to its first step.

## Mechanical oracles

- **Reaction coverage:** 18 of 20 reactions sit inside a pathway. Two uncovered:
  `5c52a150` (Background Step 9, NMSBA -> NMSBC) — a real defect, finding #3; and `57582eff`
  (catalyst prep) — excludable, finding #10. Coverage closes at 20/20.
- **Pointer integrity: FAIL.** 6 step `reaction_uuid`s in 3 records exist in no `reactions.json`
  row. Finding #1.
- **Continuity:** PASS on both resolvable multi-step routes (`13737806`, `c3ba1294`); the embedded
  step data of the 3 dangling records is also continuous. No broken-chain finding.
- **compounds.json linkage: CLEAN.** All 0 failures — every `ksm` / `intermediates[]` / `product`
  slot across all 16 records resolves by `compound_uuid`, and all 5 distinct compounds also match
  by `identifier`. No finding.
- **Non-synthetic ops counted as routes:** none. No reaction has `non_synthetic=true`.
- **Steps stitched across unrelated sections:** none. No step ordering or duplicate-step problems.
- **Termination:** all 16 records end at mesotrione or a declared intermediate. No finding.
- **Wrong target tag:** none found (see finding #8 for the absent-control observation).
- **ksm / intermediates / product mislabels:** none found; intermediate counts are consistent with
  step counts in every record.

## Top 3 issues

1. **Pointer integrity failure (High, #1).** Pathways `80fafd41` (Detailed Description), `e6c1deb1`
   (Claims) and `a213ad3c` (patent scope) reference 6 `reaction_uuid`s absent from
   `reactions.json`. Root cause is recorded in the notes of reactions `86d4916a` / `aa444481` /
   `dc18d66e`: those Summary steps are MERGED RECORDS that absorbed the Detailed Description and
   Claims recitations, and `pathways.json` was never re-pointed.
2. **Duplicate routes not merged (High, #2).** Four pathway records hold one route; the reactions
   layer already collapsed the three recitations to one chain. Survivor should be a single record
   with `source_sections = [Summary, Detailed Description, Claims]`.
3. **Prior-art route truncated and its second step orphaned (Medium, #3).** Pathway `508ce95c`
   stops at NMSBA, but the CN106565561A citation at `.md` line 88 is a preparation of NMSBC, and
   `reactions.json` Background Step 9 (`5c52a150`, `precursor_step='Step 8'`,
   `linkage_confirmed=true`) sits in no pathway.

Honourable mention: the claimed 2-step route to the enol ester (claim 10, `.md` L380) has no
pathway record at all (#4), a gap the data itself flags via
`claimed_processes_with_other_endpoints_not_emitted`.

## No file was modified

**No file was modified by this pass.** `pathways.json`, `reactions.json`, `compounds.json` and the
enriched `.md` were opened read-only, as were `AUDIT-LOG.md`, `RUN-NOTES.md`, the `audit/README.md`,
`reactions-provenance.json` and `output/README.md` (none of which were read or touched). The only
files written are `audit/pathways_findings.csv` and this note.
