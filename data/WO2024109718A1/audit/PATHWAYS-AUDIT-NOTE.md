# WO2024109718A1 — PATHWAYS (routes) audit note

Findings-only pass, 2026-09-04. Companion file: `pathways_findings.csv` (14 rows).

## Record count and source

- `output/pathways.json` holds **23 records** — `len(json.load(open('output/pathways.json')))` = 23.
- Source of the pathways: `/Users/sathvik.k/Desktop/pipeline_for_patent/patent-extraction/runs/WO2024109718A1/output/relevant_output/gold/pathways.json`, also 23 records.
- Verified myself: the two `pathway_uuid` sets are **equal** (`set(x['pathway_uuid'] for x in working) == set(... gold ...)` → True; both differences empty). The working copy is not byte-identical — it adds SMILES enrichment and changes identifier labels (below).

## Target molecule

**Tembotrione** (环磺酮), `CS(=O)(=O)c1ccc(C(=O)C2C(=O)CCCC2=O)c(Cl)c1COCC(F)(F)F`, C17H16ClF3O6S.

- Title: `.md` **line 40 / 42** ("Method for preparing cyclosulfonone, and intermediates" / 环磺酮的制备方法和中间体). "Cyclosulfonone" is the machine rendering of 环磺酮; **line 54** renders the same title as "Preparation method and intermediates of tembotrione".
- Abstract: **line 44 / 46**.
- Confirmed in the body at **lines 57-58** ("relates to a new process for preparing the herbicide tembotrione") and **lines 61-62**.
- Structure fixed from `.md` **line 65**, InChIKey `IUQAXCIUEPFPSF-UHFFFAOYSA-N`. The formula (VIII) enol ester (`OBAMHTWSCBVWGB-UHFFFAOYSA-N`) is its isomer and is **not** the target.

## Derived distinct-route count

Source: `output/reactions.json` (69 records). Method: chained on `precursor_step` (authoritative — it separates Background Scheme A from Scheme B, which share intermediates within one `section_label`), enumerated maximal root-to-leaf chains, deduped by ordered reaction-id tuple. All 69 `precursor_step` references resolve; 7 roots.

- **16 maximal chains**; 1 of them (`Preparation Routes..._Step 6`, trifluoroethanol → CF3CH2OM) is a single non-chaining reagent preparation → **15 route-shaped chains**.
- Applying the merge rule "same chain restated in another section = one route": the Claims 8-step, Summary 8-step and Prep-Routes 9-step chains are one route, and the Claims 6-step, Summary 6-step and Prep-Routes 7-step chains are one route. 6 chains collapse to 2.
- **My derived count: 11 distinct routes** = 2 prior-art (Background Scheme A from dichlorotoluene, Scheme B from 3-chloro-2-methylaniline, `.md` lines 63-65 and 69-74) + 2 generic invention routes + 7 worked Example runs.
- At the chemistry-family level the patent describes **4** distinct routes: 2 invention (via acid (V)→(VII)→(VIII), and direct (IV)→(VIII) either stepwise or one-pot) + the 2 prior-art routes.

**Comparison to 23.** The 23 records reconcile exactly, so the count is inflated by shape, not by invention:

| component | n |
|---|---|
| route-shaped maximal chains | 15 |
| reagent-preparation pseudo-route (`cc1e26c3`) | 1 |
| strict-prefix records terminating at a declared intermediate | 5 |
| `scope=patent` records duplicating a `scope=section` record | 2 |
| **total** | **23** |

The 5 prefixes are defensible (the patent claims step (v)/(v') standalone, `.md` lines 553-557, and declares (V) and the (VIII) ester intermediates at lines 343-344). The reagent pseudo-route and the 2 duplicates are logged as findings.

## Verdict on the generic-label identifier changes

I found **117** identifier-level differences from gold, not 34. My verdict: **115 of 117 are acceptable; 2 are real degradations.**

- **Acceptable (115).** They are label normalisations, not information loss. Most are English renderings of the patent-literal Chinese (式(I)化合物 → "compound of formula (I)"); the rest replace a bare SMILES string with the formula label the patent itself defines for that exact structure (`.md` line 231 for (I)-(IV); lines 316, 339, 343 for (V), (VII), (VIII)). In every case the node still carries the correct `smiles`, `molecular_formula` and `compound_uuid`, and the concrete IUPAC/Chinese name survives in `aliases`. I verified the join is not broken: **0 of the name strings used in `reactions.json` fail to resolve to a `compounds.json` identifier or alias**, and name-level continuity holds in every record that has no dangling pointer. Two changes are outright improvements (`Cc1c(N)cccc1Cl` → `3-chloro-2-methylaniline` as KSM of three Prep-Routes records).
- **Real degradations (2)** — and these are not generic-label changes at all, they are wrong-compound substitutions: `34b4af1a` intermediates[5] and `375eff4f` intermediates[7], where gold's 式(VIII)的酯化合物 became **"tembotrione"**. The (VIII) node disappears from the chain and the target appears as its own precursor, so continuity across the last junction is no longer readable. Logged as finding #3, High.
- One further wobble, logged Low (#9): relabeling the prior-art Background intermediates with the invention's (V)/(VII)/(VIII) numbering, which the `.md` never applies to the cited DE19846792A1 / CN200510030163.5 schemes.

## Mechanical oracles

- **(a) Reaction coverage — PASS.** 0 of 69 `reaction_uuid`s in `reactions.json` are unused by any pathway. No orphan synthetic reactions, nothing needing an exclusion reason.
- **(b) Pointer integrity — FAIL.** 10 distinct step `reaction_uuid`s in 5 pathway records do not exist in `reactions.json`. Finding #1, High.

## Top 3 issues

1. **10 dangling step pointers** (`2106ad7d`, `44727906`, `4403dbdd`, `200f57af`, `375eff4f`) — pathways kept the pre-merge uuids for step (vi)/(vi') "Arrow 1" and Scheme Steps 1-6 after `reactions.json` deliberately merged them, so 5 of 23 routes cannot be continuity-checked at all.
2. **The formula (VIII) enol ester carries tembotrione's SMILES in 11 `reactions.json` product records**, making the closing rearrangement a null transformation (`92025cd1`, `40f23744` have product == reactant) and hiding the target/intermediate boundary from any structure-based check — while `pathways.json` node `69f81fcc` holds the correct ester SMILES, so the two files disagree.
3. **`34b4af1a` and `375eff4f` list "tembotrione" as an intermediate** and omit the (VIII) ester that actually sits between their last two steps — the only genuine chemical degradation introduced relative to gold.

## Findings by severity

High 3 · Medium 5 · Low 2 · Info 4 — 14 rows, all `Status=open`.

Categories with nothing to report: no orphan synthetic reactions; no wrong target tag; no non-synthetic operation counted as a route step (all 69 reactions have `non_synthetic=false`); no illegitimate stitching across unrelated sections (the 7 multi-Example records are one sequential worked route, every link declared by `precursor_step`); no genuinely different routes wrongly merged; no pathway node missing from `compounds.json` except the null product of `212a3eff`.

## Modification statement

**No file was modified.** `pathways.json`, `reactions.json`, `compounds.json` and `WO2024109718A1-enriched-numbered.md` were opened read-only. The only files written by this pass are `audit/pathways_findings.csv` and this note; no other file in `audit/` was touched. Temp scripts live in the session scratchpad.
