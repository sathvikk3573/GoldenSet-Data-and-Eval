
## Blind adversarial review, both halves (2026-09-01/02, cross-patent pass)
Two fresh no-context subagents (barred from audit/, each other's file and all
scratchpads; record notes treated as claims to verify) reviewed reactions.json (20
records) and compounds.json (52 records) against the full patent. Both verdicts:
patent-derived content flawless — every quantity pair, quote, melting point,
appearance, drawing fact and yield survived adversarial recomputation; zero CRITICAL
findings in either file. Generated/derived-layer findings dispositioned after
independent verification (backups WO-reactions.json.before-blindreview and
WO-compounds.json.before-blindreview in the session scratchpad):

reactions.json FIXED (20 records touched): F-011 completed on its second mirror
(the seven Example product entries in compounds[] still carried flat purity_pct
where the record level already held the printed ">" lower bound — now purity_pct_min);
linkage:standalone removed from Examples 4-6 (in-document ambiguous precursor, the
situation Example 7 correctly leaves untagged); triethylamine removed from Background
Step 3 reactant_names (role base; the file's own F-036 convention); Background Step 9
step_role intermediate->final (the acid chloride is the cited CN106565561A's stated
target, L88); purification:none removed from Summary Step 3 (field holds the [0044]
optional-purification statement); byproduct_recovery and selectivity.stereo shapes
unified; seven stale/false note clauses superseded (eight-vs-nine record count, two
pre-fix class sentences, a stale cross-record criticism, a dangling merged-record id,
two F-016 quote inaccuracies, a false tag-presence claim).
reactions.json REFUTED: the claimed missing stirring_stated field (no such field
exists anywhere — F-013's actual convention holds stirring in notes with the enum
left null, uniformly); the is_one_pot reading of [0041] "may be conducted"
(documented judgment, both readings in the notes); the DD section_type pairing
(the schema vocabulary has no detailed_description value — same constraint noted on
compounds below).

compounds.json FIXED (13 records + READMEs): mesotrione's sections/roles_by_section
completed to the 8 sections its own merge notes narrate (Field of the Invention
L45/49, Objects of the Invention L100/105 added); the Background L68 drawn "Solvent"
mention added to record 42 with the capitalised token as alias (sibling convention);
seven stale "not recorded" clauses superseded (the C-003 generic records exist);
enzyme identifier_type iupac->other; the enol ester's flat Example-7 charge nulled
(already held in quantities_by_section with role_in_that_example — the C-006
misread-prevention rationale); compound_class:reactant/product on the two generic
records normalised to starting_material/intermediate; a crystals-vs-crystalline-solid
note clause corrected; the 11-generic-terms arithmetic in output/README.md and the
repo README corrected to 12 (C-017's addition was never counted).
compounds.json KEPT with grounding: the deliberate C-005 hydrochloric-acid twin
alias; the DD section_type constraint (no enum value; now documented here).
Post-fix batteries clean on both files (mirrors, shapes, joins, alias verbatim
checks, rbs/sections coherence). BOTH FILES GOLD after this pass.
