# Prompt 10: identify every route the patent describes

The pathways track. Run this FIRST, before `pathways.json` is opened. As with reactions
and compounds, the independence is the whole point: once you have seen what the pipeline
found, you will find the same thing, and your count is worthless.

A **pathway** (a route) is one full synthetic sequence: a key starting material (KSM),
through its intermediates, to the final product, by an ordered chain of steps. It is the
"No. of Routes" number. Where a reaction is a single transformation, a pathway is the
whole chain of them that gets from a starting material to the target.

---

## Decide what counts as one pathway, before you read a line

The count depends on definitions, and if you settle them only as you go the adversarial
round below will surface definitional disagreements that look exactly like missed routes,
and you will not be able to tell the two apart. So decide these first, write each decision
down with its reasoning, and apply it consistently. In most patents the questions are:

- **Is a reagent or intermediate preparation its own pathway, or part of one?** An
  alkoxide or salt prepared in a side step to feed the main route is normally not a route
  to the target — record it as a reagent prep, not a pathway.
- **Is a side reaction a pathway?** A route to an impurity or by-product the patent
  describes is not a route to the target. Exclude it, with the reason.
- **One route told many times, or many routes?** A route stated in the Claims, the
  Summary and twenty Examples is one route by description but may be many *performed*
  runs — settle now whether you are counting described routes or performed runs, because
  the answer decides the count. (The reconciliation pass resolves this against the file by
  the ordered set of reaction ids; see Prompt 11.)
- **Is a generic Markush chain a pathway distinct from its specific instances?** Decide
  whether "make the alkoxymethyl compound" and the specific trifluoroethoxymethyl instance
  are one route or two.

These decisions are conventions that must hold across every patent, so record them in a
written rules file (not just this session's notes) and reuse them next time.

Read the single `*-enriched-numbered.md` in `input/` line by line, in depth. Keep a running
scratchpad. Your job in this pass is one thing: **decide how many genuine, distinct
routes to the product this patent describes, where each begins and ends, and what its
step chain is.**

## Rules

1. **Do not open `pathways.json`, `reactions.json` or `compounds.json`.** The `.md` is
   the only source. If you have already seen the extraction output, say so now.
2. **One route restated in several sections is one route.** A patent states its route in
   the Claims, again in the Summary of the Invention, again as a worked Example, and
   again as an overview scheme. That is one route carried by four descriptions, not four
   routes. Repeat descriptions add detail; they do not multiply the count.
3. **A genuinely different route is a separate route.** A different key starting material,
   a different set of intermediates, or an alternative variant (a thioether route beside
   the main route, a route that converges with the main one only from step 4 onward) is a
   distinct route. Say where it diverges and where it converges.
4. **Ground every route in line numbers**, and give its step chain KSM → intermediate →
   ... → product, each arrow tied to the transformation that makes it.
5. **Preserve document order**, and read the drawings — a route drawn only as a scheme
   (`[IMAGE_EXTRACT: ...]`) is still a route.
6. **Scope.** In: any route the patent actually teaches as a way to make the target. Out:
   a route mentioned only to disparage it, or a single reaction that is not part of any
   sequence to the product. Flag exclusions explicitly, with the reason.

## What "read carefully" actually means

**First, read normally, with the route in mind.** Ask of each passage: does this extend,
begin or end a route? Build the whole chain end to end before you check anything — what
the KSM is, what each intermediate is, which transformation joins each pair, where the
route converges with or diverges from another.

**Then do the checkable things.** Count the steps in each route and check they chain (the
product of step _n_ is a reactant of step _n+1_); line up routes that look the same side
by side to see whether they are the same route or two; read for the step that is implied
but not spelled out ("the salt prepared in step (1)"); confirm every KSM, intermediate
and product name against the page.

## How to read: chunk by section, keep one route ledger

Chunk the file **by section** — Claims, Summary, each scheme drawing, each Example — not
by a fixed line count, because a route is told across all of them and an Example will say
"same as Example 11". Show me the chunk boundaries before you start reading.

Keep **one route ledger that grows across chunks**, never a per-chunk list. Each route in
it carries: its tellings (which sections describe it, with line numbers), its KSM,
intermediates in order, product, and every cross-reference ("same as Example 11") resolved
to the concrete step it points at. When a later chunk adds detail to a route already in the
ledger, extend that entry rather than opening a new one.

## Mechanical oracle

Alongside the reading, run one check that needs no chemistry and catches what a reader
skims past: **every product the patent says it prepared must be the terminal of some route
in your ledger.** List the prepared products from the `.md` (every "was obtained", every
Example title compound), and confirm each is the endpoint of a route. A prepared product
that ends no route is a route you missed.

## Prove yourself wrong, then stop

When your ledger feels complete, hand the `.md` (and only the `.md`) to a fresh sub-agent
and have it try to prove you missed a route. Give the **second** adversary a different
framing from the first — for example "list every compound the patent says it made and
trace each backward to a starting material" — because two rounds with the same framing
from the same model are correlated and agree for the wrong reason. Neither adversary may
open `pathways.json`. **Stop rule:** two consecutive adversarial rounds that add nothing
beyond disagreements already in your ledger. Add back any genuine miss and note every
disagreement as a definitional call, resolved against the conventions you wrote up top.

## What to give me at the end

1. **A route map first**, one diagram per route: KSM → intermediate → ... → product, each
   arrow labelled with the transformation and its line number.
2. **Then the count of distinct routes**, each with the sections it is drawn from and its
   line span.
3. **Then, per route, the ordered step chain**, one line per step: what reacts, what
   forms, which section, line number.
4. **Anything excluded**, with the reason.

Ask me before you start if anything is unclear. Do not assume.
