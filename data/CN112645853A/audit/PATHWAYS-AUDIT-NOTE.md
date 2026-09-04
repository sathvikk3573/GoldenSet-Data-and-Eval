# CN112645853A — PATHWAYS AUDIT NOTE

Findings-only pass, 2026-09-04. Companion file: `pathways_findings.csv` (15 rows, all `open`).

## Record count + source

- **32 records** in `output/pathways.json` — from `len(json.load(open('/Users/sathvik.k/Desktop/goldenPatents/data/CN112645853A/output/pathways.json')))`.
- **Source of the pathways:** `/Users/sathvik.k/Desktop/pipeline_for_patent/patent-extraction/runs/CN112645853A/output/relevant_output/gold/pathways.json` (also 32 records).
- `pathway_uuid` sets are **identical** between working and gold (set difference empty in both directions). Working copy additionally carries SMILES enrichment, and 9 records have re-pointed step `reaction_uuid`s — see below.
- 31 records are `scope="section"`; 1 record (`c2c19cd8`) is `scope="patent"` with `section_label=null`.

## Target molecule

**2-chloro-3-(alkoxymethyl)-4-(methanesulfonyl)benzoic acid** — a Markush intermediate.

- **`.md` line 36** (title of invention). Reinforced at line 133: "the compound 2-chloro-3-alkoxymethyl-4-methanesulfonylbenzoic acid is the **key intermediate** of this series of compounds".
- Concretely two products: 2-chloro-3-[(2,2,2-trifluoroethoxy)methyl]- and 2-chloro-3-{[(RS)-tetrahydrofuran-2-yl]methoxymethyl}-4-(methanesulfonyl)benzoic acid (`.md` lines 121-122 and 606).
- **This patent's target is NOT tembotrione.** Tembotrione and tefuryltrione are the downstream herbicides this intermediate feeds (`.md` lines 125-126, 146, 266). No pathway record mis-tags them as its product; the 6 mentions across `pathways.json` / `reactions.json` are all inside descriptive `notes` and are correct.

## Derived distinct-route count: 22 (vs `len(pathways.json)` = 32)

Derived from `output/reactions.json` alone — 55 reactions over 24 `section_label`s (Background 3, Claims 2, Summary 2, Examples 1-20 = 48) — grouped by section, chained `product_name` → `reactant_names`, then deduped on the ordered reaction-id set, and cross-checked against the `.md`:

| | routes |
|---|---|
| Prior-art Background route (etherify, then saponify) | 1 |
| Generic invention route (cleave ester first, then etherify) — restated in Claims, Summary and the patent-scope roll-up | 1 |
| Worked Example runs, Examples 1-20 | 20 |
| **Derived total** | **22** |

The 10-record gap to 32 is: **8** reagent-prep records (finding #1) + **2** redundant restatements of the generic route (finding #3). Under the stricter reading — the 20 examples being condition variants of the one claimed route — the patent describes only **2 route topologies**.

## Top 3 issues

1. **8 of 32 records are reagent preparations, not routes** (`1c79768d`, `44ae37f0`, `1cc65a32`, `f3678fe3`, `3336dafe`, `60832274`, `73eb6f69`, `cee35a94`): single alkoxide-forming steps terminating at a reagent, with an etherification-reagent alcohol mislabeled as the KSM — the pipeline self-declares this via `reagent_preparation_not_route_to_target`, but the raw record count overstates the route inventory by 8.
2. **The generic invention route is stored three times un-merged** (`f922125c` Claims, `29d0a768` Summary, `c2c19cd8` patent-scope — the last re-using the Claims step UUIDs verbatim), and `merged_from` / `source_sections` are absent from all 55 reaction records, so no merge provenance exists anywhere.
3. **`overall_yield_pct` is null on all 32 records** although the patent states an explicit two-step overall yield for each of the 20 examples (93.8% at `.md` 294-295, etc.); the value sits on the final step's `product_yield_pct` and was never promoted.

## The 9 step-list divergences from gold — resolved, working copy is correct

`de6c81b6`, `082f622c`, `44ae37f0`, `7c19852e`, `88c5d169`, `1cc65a32`, `4e55afd6`, `f3678fe3`, `fa128a5f`.

Not broken re-compositions. The working `reactions.json` **relabeled** reaction ids (`Example N_Step 2` → `Step 2b`, `Example 8_Step 3` → `Step 2b`, `Example N_Alkoxide Preparation` → `Step 2a`); `reaction_uuid` is a deterministic UUID5 over that id, so the uuid moved with the label and the pathway steps were correctly re-pointed. All 9 gold-side uuids are **absent** from the working `reactions.json`, so retaining gold's lists would have left 9 dangling pointers. `ksm`, `intermediates`, `product`, step count and step order are unchanged in all 9, continuity holds, and the relabel makes Example 8 consistent with the Step 1 / 2a / 2b convention of Examples 1, 2, 5, 17, 18, 19, 20. Logged as one `Category=Provenance` row (#13); no per-record problem row.

## Mechanical oracles

- **(a) Reaction coverage** — 54 of 55 reactions sit inside some pathway. The single uncovered reaction, `6d1fd235` `Background_Side Reaction`, is legitimately excludable: it is the impurity-forming side reaction (`.md` 147-148) that the invention exists to avoid, tagged `step_role:side_reaction`. Reason recorded in row #8.
- **(b) Pointer integrity** — PASS. All 54 distinct step `reaction_uuid`s referenced by the 32 pathways exist in `reactions.json`; 0 dangling.
- Also PASS: continuity (0 broken chains), compounds linkage (0 of 74 ksm/intermediate/product entries unmatched on `compound_uuid`), ksm-is-a-step-1-reactant (32/32), declared intermediates present in their own chain, single-section containment (no steps stitched across sections), no wrong target tag.

## Categories with nothing found

Broken chains, steps stitched across unrelated sections, orphan synthetic reactions (beyond the excludable side reaction), dangling `reaction_uuid` pointers, missing `compounds.json` records, and wrong target tags — **no findings in any of these**.

## No file was modified

**No file was modified by this audit.** `pathways.json`, `reactions.json`, `compounds.json` and `CN112645853A-enriched-numbered.md` were opened read-only. The only files written are `audit/pathways_findings.csv` and this note; nothing else in `audit/` was touched.
