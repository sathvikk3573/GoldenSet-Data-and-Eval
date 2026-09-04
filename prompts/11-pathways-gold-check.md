# Prompt 11: verify, merge and gold-check pathways.json

The final pathways pass. Run this after Prompts 9 and 10, and after `reactions.json` and
`compounds.json` are already gold — because a pathway is a projection of those two files,
and it can only be gold if it agrees with them. This pass verifies every field of every
pathway record, merges the duplicate routes, and ends with a plain gold verdict. Like
Prompt 7, it corrects as it goes.

---

## Re-project the step content from the gold first — before you read anything

A pathway step is meant to be a verbatim copy of its gold reaction record, but the file
you were handed is the raw pipeline output: its step copies are whatever the reactions
looked like *before* the reactions gold passes corrected them. If Prompt 10's freshness
oracle reported many differing steps, the file is a stale projection, and auditing those
copies line by line means rediscovering one root cause dozens of times.

So fix the root cause once, mechanically, before any reading: **for every pathway step,
overwrite its reaction-derived fields with the current gold `reactions.json` record it
points at** (matched by `reaction_uuid`, repointed to the survivor if that reaction was
merged in the dedup). Edit in place; if you snapshot before this bulk refresh, delete it
once verified — the finished folder carries no `.bak`.
This is the in-folder equivalent of regenerating the projection: never hand-edit a step
value to match the gold — copy the whole gold record's fields across, so the step and the
reaction cannot drift again. Log the re-projection as one `FIXED` action, with the count
of steps refreshed, not one row per field.

After this, the step content is byte-identical to reactions you already verified as gold,
and this pass can spend its reading where it actually matters: the pathway-specific fields.

## The loop

1. **Open `output/pathways.json`** and read the schema actually present, so you know every
   field: `ksm`, `intermediates`, `product`, `steps`, `overall_yield_pct`,
   `overall_purity_pct`, `tags`, `honest_uncertainty_flags`, the `*_score` fields, the
   `min_*_count` fields, `pathway_uuid`.

2. **Take one record**, in file order, index visible. Understand the route it describes.

3. **Read the patent for the pathway-specific fields, once per distinct route.** The step
   content is already gold after the re-projection, so do not re-read the whole file for
   every record to re-check temperatures you already verified in the reactions passes.
   Read the `.md` once per *distinct* route (this patent has only a handful), route in
   mind, and spend that reading on what is pathway-specific and checked nowhere else:
   - **Linkage** — does the patent actually state that step _n+1_ consumes step _n_'s
     product ("the salt prepared in step (1)"), or is the chain only inferred? Mark the
     inferred links; an inferred linkage presented as stated is a finding.
   - **Starting-material choice** — is the `ksm` the compound the patent names as the
     route's start, under the skeleton rule?
   - **Product identity** — real distinctions (sodium vs potassium salt) are gold;
     cosmetic ones (methanesulfonyl vs methylsulfonyl) are not, and must not be "corrected"
     into a difference.
   - **Scope and `section_label`**, the **tags** derived from chain length and branching,
     and the **honesty flags**.

   For an Example that merely re-runs a route already in the ledger, check only its linkage
   sentence and its cross-reference — not every value again.

4. **Verify every field, deciding first where its answer comes from:**

   **a) Fields the PATENT must answer** — the KSM, the intermediate list and its order,
   the product, the step chain, `overall_yield_pct`, `overall_purity_pct`. Check each
   against the page. An empty field is a question: if the patent gives an overall yield
   or an intermediate the record omits, that is a finding.

   **b) Fields that must AGREE WITH THE GOLD** — `pathways.json` is not independent of the
   gold you already built:
   - **Every step's `reaction_uuid` / `reaction_id` points at a record in the gold
     `reactions.json`** (repointed to the survivor if merged in the dedup). The per-step
     values were made byte-identical to the gold by the re-projection above, so here you
     only confirm the pointer resolves; any value still disagreeing with the gold means the
     re-projection missed that step — a finding, not a value to hand-edit.
   - **Every `ksm`, `intermediate` and `product` must correspond to a gold
     `compounds.json` record** (by `compound_uuid`, then `identifier`).
   - **The `smiles` slot on `ksm` / each `intermediate` / `product` is filled and verified
     by the dedicated structures phase (Prompts 12–13), which resolves a structure once per
     `compound_uuid` from multiple sources and propagates it into these slots. Do not
     resolve names here.** In this pass only confirm the slot exists and its compound maps
     to a gold `compounds.json` record by `compound_uuid`; leave the value for Prompt 12 to
     propagate. A slot whose compound has no `compound_uuid` to propagate onto is a finding.
   - **`overall_yield_pct` — the patent's stated figure wins; never let a step-multiply
     rule overwrite it or force it null.** If the patent states an overall yield from the
     starting material, that number is the gold value, even where the pipeline left the
     field null because an unisolated-salt step has no yield to multiply. Only where the
     patent gives no overall figure may you compute one, and only if every step carries a
     yield; otherwise null is correct — say so. A patent-stated overall yield lost while the
     field sits null is a finding.

   **c) Fields the LLM GENERATES** — `tags`, `honest_uncertainty_flags`, and the scoring
   fields (`safety_score`, `green_score`, `feasibility_score`, `cost_score`, `yield_score`,
   `byproduct_score`, `confidence_score`) and the `min_*_count` fields. These are generated
   judgements, not patent facts, and are usually null. Do not invent a score the patent
   gives no basis for. Where they are null, confirm null is right and say so; where filled,
   check the value is defensible and consistent with how the file scores the other routes.
   Flag a filled score the patent cannot support.

5. **Check the same record a second time**, then move to the next. When all are done,
   sweep the whole file once more.

## Merge the duplicate routes

Execute the `Duplicate` findings from Prompt 10. Edit in place — do not leave a `.bak`
behind; if you snapshot before the bulk edit, delete it once the result is verified.
Collapse each set of section-restatements of one route to the richest survivor (usually
the Example route), **unioning any non-null field a dropped record carries that the
survivor lacks** (the survivor wins conflicts). Never merge two genuinely different
routes. Keep `source_sections` on the survivor (the distinct sections, including its
own), but **never write a `merged_from` field** onto a record. There is no
`dedup_pathways.csv` ledger; each merge is closed as its `Duplicate` finding in
`findings.csv`, whose resolution names the survivor and the routes folded into it.
Update any stated route count with a dated note. Verify: the file parses as a list, no
record carries a `merged_from` key, and the surviving route count == original − the
number of routes folded away.

## What "gold" has to mean by the end

- Every route the patent teaches is present, once, with the right KSM, intermediates in
  order, and product.
- Every step points at a real gold reaction, and every value the pathway repeats agrees
  with that gold reaction.
- Every KSM/intermediate/product maps to a gold compound, with SMILES filled wherever the
  gold or a name resolution can supply it.
- Every empty field is empty because the patent (or the gold) genuinely says nothing, and
  you checked.
- Every generated score is either a defensible judgement consistent across the routes, or
  correctly null.

## Close the audit folder

1. **Build `audit/pathways_audit.csv` as you go**, one row per field checked, passes
   included, flushing each row to disk as you finish it so the sheet doubles as your
   resume point. Same shape as the reactions sheet:

       #, Pathway, Section, Line (English), Field, Item, Role in the source,
       What the source says (English), What pathways.json has, Status,
       "Problem, in plain words"

   where `#` numbers the rows from 1, `Pathway` is the `section_label`, and Status is one
   of `OK`, `MISSING`, `WRONG`, `NOTE`, `EXCLUDED`, `FIXED`. After this pass no row may
   describe a value the file no longer holds.

2. **Close the pathways findings in `audit/findings.csv`**: one row for every pathway
  finding raised in Prompts 10 and this pass — every `pathways.json` row of
   `findings.csv` (including every `Duplicate` row now merged) and a fresh row for every
   `MISSING`/`WRONG` row of `pathways_audit.csv` — none skipped, each with its `Verdict`
   (`NO LONGER TRUE` / `STILL TRUE` / `NEVER TRUE`) and `Resolution / evidence` filled
   (field, current value, line). For a merged `Duplicate`, name the survivor and the
   routes folded into it.

The file is not gold while any verdict says `STILL TRUE`.

## Rules

- **Correct as you go**, but reason out every change, verify it against the patent and the
  gold afterward, and record it.
- **The gold `reactions.json` / `compounds.json` win every conflict** — if the pathway
  disagrees with them, the pathway is what is wrong, unless you find the gold itself is
  wrong, in which case stop and tell me rather than editing gold from here.
- **If you are unsure, stop and ask.** Never guess a value or a score into the gold.
- **Never treat the pipeline's behaviour as correct.** It is the thing being measured.

## What to give me at the end

- The finished `audit/pathways_audit.csv`, and the pathway rows of `audit/findings.csv`
  with a verdict on every finding. No `dedup_pathways.csv`, no `findings_verdicts.csv`,
  no `.bak`.
- The record-by-record result: how many passed clean, what was corrected, with the index
  and line numbers.
- A plain statement of whether `pathways.json` is gold, and if not, exactly what is
  blocking it.
- Anything you could not settle, with both readings.

Ask me if anything is unclear before you start, and stop and ask if you get stuck part way
through rather than guessing.
