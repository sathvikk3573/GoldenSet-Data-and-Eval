# Prompts 12+13 (consolidated): resolve, verify and gold-check a structure for every compound

This is the merged structures track. It supersedes the separate Prompt 12 (resolve) and
Prompt 13 (gold-check) by folding in the offline-OPSIN engine, the mechanical precision
guards, and the null discipline from the `smiles-enrichment` reference, while keeping our
multi-source cross-agreement, conflict adjudication, and confidence tiers — which the
reference deliberately defers to a later pass and we do not.

`smiles` is not a fourth file. It is a field in all three: `compounds.json` carries one
structure per compound, `reactions.json` carries `reactant_smiles` / `product_smiles` /
`canonical_rxn`, and `pathways.json` carries a `smiles` slot on each route's `ksm` /
`intermediates` / `product`. Resolve **once per compound**, then propagate.

Run after Prompts 7, 8 and 11 — once the three files are gold — because the resolver needs
the corrected names and the propagation targets must already be gold records.

The pass runs in two phases, exactly the split the reference recommends and we already use
elsewhere: **Phase A FILL** (first-answer-wins, get a good structure onto every record) then
**Phase B VALIDATE** (cross-source agreement, conflicts, the free InChIKey check — the pass
that is *not* optional, because a single reader wins unopposed in Phase A and OPSIN reads a
bare element name as one atom).

---

## The one rule that makes this safe

**Never write a SMILES from memory, and never trust a string just because it parses.** Every
stored structure comes from a deterministic source — the patent's own `[IMAGE_EXTRACT]`
drawing, or a name resolver (OPSIN, PubChem) — and passes RDKit (`MolFromSmiles` → canonical
SMILES → InChIKey) before it is accepted. When Claude is involved it repairs the **name**
(translate, fix a typo, resolve a cross-reference) and hands that back to a resolver; it
never emits the structure itself, except the explicit, flagged last-resort draw.

`molecular_formula` and `molecular_weight` are **always recomputed from the stored SMILES**,
never carried over from a reader, so the three can never drift apart.

## Setup — the engine is offline and deterministic

- **OPSIN runs from the vendored jar, never the web service.** The web service is
  rate-limited and needs the network; the jar (`pipeline/vendor/opsin-core-2.9.0.jar`) is
  deterministic, unlimited, and offline. One JVM start per patent, all names batched — the
  grammar tables cost more to build than every parse in this repo costs to run. Prereq: Java
  21+. Confirm with `echo "sodium chloride" | java -cp pipeline/vendor/opsin-core-2.9.0.jar
  pipeline/vendor/OpsinBatch.java` → one JSON line, status SUCCESS, smiles `[Cl-].[Na+]`.
- **PubChem is cached per patent.** `≤5 req/s, ≤400/min, ≤300 s compute/min`; over that it
  returns HTTP 503. **Never cache a 503 or a network error as "no entry"** — that turns a
  transient into a permanent null. Cache only real 404s and real hits.
- **Refuse a silently short OPSIN batch.** If OPSIN answers N of M names, raise — a short
  answer looks exactly like a run where every name failed and would be cached as such.
- Run compute in one-shot `bash` + `.venv/bin/python3` subprocesses with hardcoded absolute
  paths and a pre-write assertion on `patent_id` + record count. Do not carry path variables
  in a persistent kernel (confirmed cross-worker bleed).

---

# Phase A — FILL

## A0. The discreteness gate, before any resolver runs

Decide first whether the record is a single, discrete molecule. A resolver fed a class name
returns a misleading "representative" (ask OPSIN for `benzoylcyclohexanediones of the general
formula (I)` and it hands you one arbitrary member — a false gold). Classify:

- **Markush / general formula** ("compound of the general formula (I)", a drawn core + R-group
  table) → `null`, reason `markush_generic`. Optionally store `markush_core` (valid SMARTS
  with attachment points) + the R-group definitions with line numbers; never a single SMILES.
- **Class / mixture / material** ("mineral oil", "kaolin", "polyglycol ethers", "hydrocarbons",
  "higher-boiling aromatics", "substituted benzyl bromides") → `null`, reason
  `class_or_mixture`.
- **Role descriptor** ("condensing agent", "the base", "the solvent", "active ingredient") →
  `null`, reason `role_descriptor`.
- **Cross-reference** ("the compound of Example 5", "化合物II", "compounds according to the
  invention") → do not resolve by name; route to the crossref step.
- **Discrete molecule** → into the waterfall.

The gate matches whole words/phrases, not bare substrings. `diethyl ether` is a discrete
solvent, not the `ether` class; `…methyl ester` in a systematic name is not the `ester`
class. A bare-substring gate is a known defect — do not reproduce it.

## A1. Collect the candidate names — pool across records

Gather the `identifier` and every `alias`, identifier first. Then **pool names across every
record the gold says is the same substance** (shared `compound_uuid`, or a shared alias):
a bare label like `式(I)化合物` in `reactions.json`, unresolvable on its own, inherits the
SMILES-bearing names its `compounds.json` sibling carries. This is not inheriting a
structure — every identifier still walks its own ladder; it is letting an identifier see
every name the gold attaches to the thing it names.

Tag each candidate ASCII / non-ASCII so the translation branch knows what it is looking at.

## A2. The ladder — first answer wins, in this order

**0. Any name on the record is already a SMILES → source `identifier_name`.** Sweep **every**
name (identifier + all aliases) for a structure **before any reader is asked about any of
them**. Across the reference's four patents, 76 of 340 identifiers resolved this way and 64
of those were in an *alias*, not the identifier. It also stops a junk name reaching a reader
first: walking name-by-name, `compound of formula (II)` reached PubChem, matched a 41-carbon
macrolide, and the ladder stopped one name short of the real `Cc1c(S(C)(=O)=O)ccc(Br)c1Cl`
the record was carrying all along.

  A name is a SMILES only if it **parses AND carries structure** — a ring closure, a branch,
  a bond, or a bracket atom (`any(c in s for c in "()[]=#123456789")` and
  `MolFromSmiles(s) is not None`, and no spaces). Parsing alone is not enough: `NBS`
  tokenises as an N–B–S chain, `CO` is valid SMILES and this patent's carbon monoxide. This
  guard is deliberately conservative and rejects both.

**1. `patent_drawing`** — an `[IMAGE_EXTRACT]` structure in the `.md` that is this compound.
Ground truth: what the inventor drew. Match by document adjacency; confirm by InChIKey once
another source resolves the same compound.

**2. `opsin`** (vendored jar) — parse each ASCII candidate as a systematic name. Fails
silently on anything non-systematic, which is fine. Known trap: it names a bare element as a
single atom (`bromine` → `[Br]`, `chlorine` → `[Cl]`) — half the mass of the real reagent
(`BrBr`). Phase B flags this; do not patch it here.

**3. `pubchem`** — look up each ASCII candidate by name. Catches trivial / INN / brand names
OPSIN cannot parse (`tembotrione`, `sulcotrione`). Returns the *registered* form, so salts
and minerals come back multi-component. A candidate generator, not an oracle.

**4. each alias** → OPSIN then PubChem, in turn.

**5. Claude tier — only where 0–4 all returned nothing** (see the parked corrected-name
protocol below). Records the transform + the resolver that finished it
(`claude_translation+opsin`, `claude_namefix+pubchem`, `claude_crossref`, `claude_manual`).

Canonicalize every hit through RDKit; a hit RDKit rejects is logged as a failure, not stored.

## A3. Two mechanical accept/reject guards, applied to every hit

- **Stated-formula rejection.** When the record states its own molecular formula in a name
  (`C8H8BrClO2S`, `K3PO4`, `V2O5` — a real formula, `>2` atoms, at least one digit), throw
  away any resolver answer whose atoms do not match it exactly. The gold checking the reader
  with nothing but its own data. (A digit is required: `NBS`/`DMF`/`THF` tokenise as element
  symbols and are not formulas. `Et3N` is not a formula — `Et` is not an element. Exclude
  `D`/`T`: RDKit counts deuterium as H.)
- **Formula answer must have the formula's atoms.** PubChem answers `CO` with cobalt; `CO`
  names one carbon and one oxygen; cobalt is neither, so the answer is thrown away and the
  alias `carbon monoxide` resolves it. Ask about formula-shaped strings (so `O2`, `HCl`,
  `SOCl2` are not lost) and let the arithmetic decide.

## A4. The null taxonomy — `unresolved_after_all_sources` is forbidden as a terminal reason

Every null ends in **one of two states**, never a vague mechanical catch-all:

- **a specific `null_reason`** in this closed vocabulary — `markush_generic`,
  `class_or_mixture`, `role_descriptor`, `ambiguous_reference`, `refers_to_another_record`,
  `plural_family`, `isomer_unstated`, `enzyme_or_protein`, `charge_type_not_a_compound`,
  `halogen_class_unstated` (the CHX₃ case) — each short and saying *which kind* of thing it
  is; **or**
- **`parked_needs_human`** — a discrete-looking name no reader could resolve. Park it (see
  below); it must be turned into `corrected` or a specific `no_structure` reason before the
  file can be gold. A null that only says "we tried and nothing came back" is not a finished
  record — it hides both real recall misses and unlabelled classes (measured: 107 of our 222
  nulls sat behind this label; on inspection most were classes/mixtures or obscure codes, and
  a direct PubChem probe recovered 0 of 18, so the honest fix is a specific reason, not a
  re-run).

## A5. The parked corrected-name protocol (the step a program cannot do)

For every `parked_needs_human` record: read the identifier, every alias, the notes, and the
`tried` trail of what each reader said about each name; if it is Chinese read
`translations.json`; where the record's own text does not settle it, read the patent `.md`.
Then decide `corrected` (with a name a reader CAN resolve) or `no_structure` (with a short
specific reason).

**THE ONE RULE: a correction must be the SAME SUBSTANCE.** Fixing an OCR error, expanding an
abbreviation the record itself defines, or giving the systematic form of a name the record
already carries are corrections. Guessing a plausible neighbouring compound is not — and it
is invisible once written.

REFUSED (different substance): `metatungstate` → "sodium metatungstate" resolves but the
patent names no counter-ion. `<phosphine> palladium dichloride` → the free ligand resolves
but the record is the complex. `prenylquinones` → resolves to one member; the patent means
the family.
ACCEPTED (same substance): `saturated brine` → "sodium chloride" (water is solvent, salt is
the charge). `…benzoate salt` → "…benzoate" (the patent names the anion, never a cation).

**Test before you propose.** Run the candidate through OPSIN (the jar) and confirm it
resolves; for a trade/common name check PubChem. A correction that does not resolve is worse
than none — it hides the real reason. Where the record states its own formula, the corrected
name's answer is checked against it and refused if it disagrees. Cover every parked
identifier and no others.

## A6. Propagate by `compound_uuid`

A structure resolved once fills every place the same compound appears:
- **`reactions.json`** — `reactant_smiles` / `product_smiles` of every reaction naming this
  compound (match on `compound_uuid`, then identity).
- **`pathways.json`** — the `smiles` slot on every `ksm` / `intermediate` / `product`.

Propagation is a copy, never a re-resolution. Mark propagated fields with `smiles_source:
propagated` **and** keep the origin compound's source in a `smiles_source_origin` field, so
provenance is traceable.

**`canonical_rxn` convention (pinned):** `reactant.reactant>>product`, reactants only,
dot-joined. Reagents, catalysts and solvents are excluded from both `reactant_smiles` and
`canonical_rxn` — no convention puts them left of the arrow, and a consumer atom-mapping the
reaction must not be handed a balance that cannot close. Rebuild it from the resolved
participants; never keep a stale value.

---

# Phase B — VALIDATE and gold-check

Go compound by compound, index visible. Run every check that applies; write each as a row in
`audit/smiles_audit.csv` (passes included — an `OK` row is the evidence the check was made).
A structure that fails any check is a finding, corrected here or escalated.

**1. RDKit validity (the floor).** Parses, canonicalizes to itself, reproduces the stored
`inchi_key` and `molecular_formula` (recompute all three from the SMILES). A mismatch means
stale derived fields — fix them.

**2. Cross-source agreement (the main signal).** Read the per-source matrix Phase A recorded
and judge the chosen structure by corroboration on the skeleton InChIKey (first block):
two+ independent sources agree, or the drawing confirms → `smiles_confidence: high`; only one
source → `medium`, and it must be *justified* against the drawing or the formula/name; a
manual draw or a salt stripped to parent → `low`. An unjustified single-source structure is a
finding.

**3. Patent-drawing cross-check (highest authority).** Every drawn `[IMAGE_EXTRACT]` skeleton
must match its compound's stored InChIKey; the drawing outranks any name resolution. A drawn
structure matched to no compound is a missed fill.

**4. The free InChIKey check.** Some gold records carry an InChIKey in their aliases (e.g.
`IUQAXCIUEPFPSF-UHFFFAOYSA-N`). Nothing in resolution reads them, so comparing the InChIKey
computed from the filled SMILES against the one the gold already carries is a free
independent check. It agreed 20/20 on the reference's four patents, and the one time it did
not it had caught a real bug (PubChem's macrolide for `compound of formula (II)`). A
disagreement is a finding.

**5. Molecular-formula and name sanity.** The formula must be consistent with any formula the
patent/record states and plausible for the name. Catches a PubChem wrong-isomer or a resolved
near-name.

**6. Salt and mixture rule.** Any `.` in the SMILES is confirmed against the compound's role.
Keep the salt/mixture only where the compound genuinely *is* that form (a sodium alkoxide
used as reagent, a named salt). Where the patent means the neutral parent, strip to the
parent fragment, set `low`, note it. A silent salt-for-parent (or reverse) is a finding.

**7. Resolve every conflict.** For each conflict Phase A raised, adjudicate against the patent
and the drawing — by the chemistry and the page, never by which source is "usually right":
`bromine` `[Br]` vs `BrBr` → the reagent is molecular bromine; `NBS` the coincidental N–B–S
vs `O=C1CCC(=O)N1Br` → the succinimide; a non-ASCII/label coincidental parse (`化合物II` → the
di-iodine `II`, a bare `H` alias → `[HH]`) → the linked structure. Record the resolution and
the evidence line; flip the finding's verdict.

**8. Provenance completeness.** Every compound ends `resolved` (smiles + inchi_key +
molecular_formula + a real source + a confidence) or `null` (with a specific reason from A4
and the failed-attempt trail). No filled smiles with no source; no `high` with only one
source; no `parked_needs_human` left open.

**9. Propagation consistency.** One `compound_uuid` carries a byte-identical structure in all
three files. A uuid with two different structures is a finding — repair the copies to the one
gold structure, never resolve twice. Confirm `canonical_rxn` was rebuilt, not stale.

**10. Reaction mass-balance (where every participant resolved).** Every heavy atom in the
products is accounted for by the reactants. A gross imbalance points at a wrong participant
structure. Note that reagent exclusion (the pinned `canonical_rxn` convention) produces
*expected* imbalances (a bromination's Br is not on the left) — log those as observations,
not findings; flag only a genuinely wrong structure.

## Correct as you go

Fix real defects on this pass — replace a drawing-contradicted structure, strip a salt,
repair a propagated copy, resolve a conflict — reasoning out and verifying each against the
patent and the drawing. Never write a structure you cannot source. A fix that would require
guessing a structure the patent does not support is stopped and flagged, not made.

## The audit sheet and findings

`audit/smiles_audit.csv` — one row per compound × source attempted (failures included, with
the reason), reflecting the finished state; every corrected value shown as a `FIXED` row
(old → new, with the line/source/drawing that settled it):

    #, Compound, Source, Query, Result, SMILES, InChIKey skeleton, Chosen, Confirmed by drawing,
      Confidence, Status, "Problem, in plain words"

`Source` ∈ `identifier_name` / `patent_drawing` / `opsin` / `pubchem` / `claude_translation`
/ `claude_namefix` / `claude_crossref` / `claude_manual` / `propagated` / `gate`.

Close every structure finding in `audit/findings.csv` with a `Verdict` (`NO LONGER TRUE` /
`STILL TRUE` / `NEVER TRUE`) and a `Resolution / evidence` that points at the current
structure, its source, and the line or drawing that settles it.

## What "gold" means by the end

- Every discrete compound has a structure that survived checks 1–10, with a source and a
  confidence; every non-discrete one is `null` with a **specific** reason that is genuinely
  true — no `parked_needs_human` left, no `unresolved_after_all_sources` anywhere.
- Every drawn structure matches its compound; every gold-carried InChIKey agrees.
- No `compound_uuid` carries two structures across the three files; no conflict is open; no
  filled structure is unsourced.
- The files are not gold while any structure finding's verdict says `STILL TRUE`.

## What to give me at the end

1. Counts: resolved / conflict / null, by `smiles_source` and `smiles_confidence`, plus the
   agreement histogram (1 / 2 / 3+ independent sources agree).
2. Fill rate for compound records, reaction participants, reaction products, pathway refs.
3. `audit/smiles_audit.csv`, complete.
4. Every correction made (with the reason) and every correction considered and **refused**
   (with why) — the refused list is as much a deliverable as the made one.
5. The result of checks 1–10, and what propagated into `reactions.json` / `pathways.json`.
6. Anything you could not settle, with both readings rather than a guess.

Ask before you start if anything is unclear. Do not assume.
