# Prompt 11: reconcile your route list against pathways.json

Only after Prompt 10 is finished and the route count is written down.

---

You now have your own list of the routes this patent describes, read from the `.md`
alone. Open `output/pathways.json` and reconcile the two.

**Do not trust `pathways.json`.** It is the thing being tested. And know its shape before
you judge it: the pipeline stores **one pathway record per section** (`scope` is
`section`, keyed by `section_label`), so the same route is restated as a Claims record, a
Summary record, an Example record and a scheme record — heavily duplicated. Each record
carries a `ksm`, a list of `intermediates`, a `product` (each `{identifier, smiles,
compound_uuid}`), and a `steps` array where every step embeds a reaction by
`reaction_uuid` / `reaction_id`.

## How to reconcile

**By identity, never by number — and define a route's identity as the ordered set of
reaction ids it runs, not its endpoints.** Match a record to one of your routes on its
sequence of transformations, in order, after repointing any step whose reaction was merged
away in the reactions dedup to its survivor `reaction_id`. Two records with the same
starting material and product but a *different* reaction-id set — different runs under
different conditions — are two performed routes, not one, and must not be collapsed on
their endpoints. Work one record at a time: read what route it claims to be, go to the
section it cites, read that passage in the `.md`, then read the whole `.md` again with that
route in mind.

## What you are looking for

- **In the patent but not in the file.** A route the patent teaches that no record
  carries. Say where it is and why it is genuine.
- **In the file but not in the patent.** A route the extraction invented, or a record
  built from a passage that is not a route.
- **Present in both but wrong.** Wrong KSM, wrong product, wrong or missing intermediate,
  steps out of order, two routes fused into one or one split into two.
- **Duplicate restatements to merge.** Same route = same ordered reaction-id set (after
  repointing merged reactions to their survivors). When several section records reduce to
  the *same* sequence — a Claims, Summary or scheme record echoing the Example route with
  no distinct step — that is one route restated: flag the generic ones `Duplicate`,
  survivor = the richest record (usually the worked Example). **A record whose reaction-id
  set differs is never a duplicate**, even if its endpoints match: that is a distinct
  performed run, or a different KSM/variant — keep it. A Claims record and a patent-scope
  record that share identical steps are a by-design restatement; whether to merge or keep
  them is the described-routes-vs-performed-runs convention you settled in Prompt 10 —
  apply it, do not decide it afresh here. The merge itself happens in Prompt 12.
- **A difference you cannot resolve.** Record both readings.

## Cross-file linkage — pathways is a projection, so check it joins

`pathways.json` is not independent of the gold you already built; its steps are the same
transformations that live in the gold `reactions.json`, and its KSM/intermediates/product
are compounds that live in the gold `compounds.json`. So also check:

- **Every step's `reaction_uuid` / `reaction_id` exists in the gold `reactions.json`.** A
  step that points at no gold reaction, or at a reaction that was merged away in the
  reactions dedup, is a finding — the pointer must be repaired to the survivor.
- **Every `ksm`, `intermediate` and `product` corresponds to a gold `compounds.json`
  record** (match on `compound_uuid`, then on `identifier`). One that does not is a
  finding.

You may open the gold `reactions.json` and `compounds.json` for this linkage check — they
are already gold, so here they are a reference, not a thing under test.

## Two mechanical oracles — run them, they need no chemistry

- **Reaction coverage.** Every record in the gold `reactions.json` must sit inside some
  pathway, or be excluded with a written reason. Build the set of reaction ids used across
  all pathways and diff it against the gold reaction ids. A gold reaction in no pathway is
  either a missing step in some route or a legitimate exclusion (a background side reaction
  that leads nowhere) — say which, in writing, for each one.
- **Projection freshness.** Every pathway step is meant to be a verbatim copy of its gold
  reaction record. Diff each step's fields against the reaction it points at
  (by `reaction_uuid`). Report the count of steps that differ. A large count means
  `pathways.json` is a **stale projection** — it was not regenerated after `reactions.json`
  was corrected — and Prompt 12 must re-project it from the gold before any content is
  judged. Record this as one finding, not one per differing field, so the root cause is
  fixed once rather than rediscovered hundreds of times.

## Classify every finding by where its fix lives

A finding is only actionable if you say where it gets fixed. Tag each one:

- **Reactions-level** — a missing or extra pathway is almost always a missing or wrong
  precursor link in `reactions.json` (the pipeline's pathway builder reads those links).
  The fix lives there, not in `pathways.json`.
- **Pathways-level** — a stale copy, a wrong `section_label`, a bad SMILES backfill: fixed
  in `pathways.json` (or by re-projection).
- **Definitional** — a disagreement about what counts as one route. The gold carries what
  the patent says under the P1 conventions; note that the pipeline may score it
  differently, and do not treat the pipeline's choice as the error.

## What to give me at the end

**Tables, one row per route — never grouped.** Table 1: matches (`your route` |
`pathways.json section_label` | `match: exact/identity`). Table 2: routes in the patent
but not in the file. Table 3: records in the file but not in the patent. Table 4:
duplicate restatements, each with its survivor. Then the counts: matched, missing, extra,
duplicate.

**Record every finding in `audit/findings.csv`** (append to the shared file), one row
each, in the one findings schema the whole suite shares:

    #, Raised in pass, File, Line (.md), Finding, Status when raised, Verdict,
      Resolution / evidence

where `Raised in pass` is `Prompt 11`, `File` is `pathways.json`, `Line (.md)` is the
line in the `.md`, `Status when raised` is `Missing`, `Extra`, `Wrong`, `Duplicate`
(name the survivor's `section_label` in the Finding) or `Definitional`, `Verdict` is
left `STILL TRUE` for now, and `Resolution / evidence` is left blank. The pathways gold
check (Prompt 12) fills the verdict and resolution on every one of these rows.

Flag only. Do not change `pathways.json` until I have reviewed the flags and told you to.
