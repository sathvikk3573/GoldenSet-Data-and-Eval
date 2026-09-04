# Prompt 14: adversarial independent audit by fresh no-context agents

Phase 6. Run this LAST, after Prompts 7, 8, 11 and 13 — when all three gold files
(`reactions.json`, `compounds.json`, `pathways.json`), including their resolved structures,
are believed to be gold. Everything
before this was done by one mind that has now read the patent a hundred times and cannot
unsee what it found. This pass hands the files to people who have never seen them, and
asks them to prove the gold wrong.

The value of this pass is the same as the value of Prompts 1, 3 and 10: **independence**.
An auditor who has already decided the file is gold is the worst person to find the
last defect in it. A fresh reader with no stake in the answer is the best.

---

## Why this pass exists

Two independent runs of this pass on finished files have each found real defects the
main audit missed: role fields that contradicted their own notes, `named_reaction` and
`reaction_class` labels that disagreed between twin records, quantities and qualifier
statements that survived only in prose, and claim-scope facts absent outright. None of
these were caught by the passes that built the file, because the mind that built it had
stopped being surprised by it. Do not skip this pass on the assumption that the passes
before it were thorough. They were, and it still found things.

## What you (the main agent) do

You have full context: you ran Prompts 1–13, you know every decision. **That is exactly
why you do not do the finding here.** You dispatch fresh subagents that have none of
your context, collect what they find, and then — and only then — judge it.

1. **Spawn the finders, in parallel, one file each.** At minimum three, run at the same
   time: one against `output/reactions.json`, one against `output/compounds.json`, one
   against `output/pathways.json`. They do not talk to each other and they do not see
   each other's output.

2. **Optionally add hunters and an original-document check** (see below), also with no
   context.

3. **Wait for every finder to report**, then judge every finding yourself against the
   patent and the current file, correct the real ones, and close the loop in the audit
   folder.

## The finder's brief — copy this into every finder subagent

Each finder starts blank. Give it only what it needs and nothing that would contaminate
it. The brief must say, in your own words but keeping every constraint:

- **You have no prior context, and that is deliberate.** Do not ask for it.
- **You may read exactly two things:** the patent (the single `*-enriched-numbered.md` in `input/`),
  and the one file you are auditing (`output/reactions.json` OR `output/compounds.json` OR
  `output/pathways.json`, never more than one).
- **You are forbidden from opening the `audit/` folder, the other json files, or any
  findings, notes or summaries from a previous pass.** If you read what someone else
  already found, you will find the same things, and your pass is worthless. This is the
  same rule as the identification passes.
- **Your job is to prove this file wrong.** Assume it has defects — it was produced by a
  pipeline that is the thing being measured, not a standard. Find:
  - **In the `.md` but not in the file** — a reaction, compound, quantity, condition,
    measurement, alias, or scope statement the patent gives and the file does not carry.
  - **In the file but not in the `.md`** — a value invented, hallucinated, or built from
    a passage that does not support it.
  - **Present in both but wrong** — values that disagree, twins that should match and do
    not, a label that is chemically wrong or inconsistent with how the file treats the
    same situation elsewhere.
  - **Captured only in prose** — a real value sitting in `notes`, `procedure_text` or
    `molar_ratio_text` where nothing downstream can read it. "Recoverable from the
    prose" is not "captured". Flag it.
- **A structure that is not the one drawn** — where the patent draws a compound
  (`[IMAGE_EXTRACT]`) and the file's `smiles` for it is a different molecule, or a
  `compound_uuid` carries two different structures across the three files. Check the
  structure against the drawing, not against another name resolution.
- **Read the whole `.md`, end to end, for what you check.** A line can concern a record
  without naming it: "the salt prepared in step (1)", "the same procedure as Example 1",
  a structure on an arrow, the line that fixes what R or M stands for.
- **Do the checkable things mechanically:** verbatim string-match every quoted field
  against the patent; recompute every mass/mole/yield you can; diff the file's list of
  compounds or reactions against your own reading in both directions; compare twin
  records field by field.
- **Report every finding with a line number in the `.md` and the record index in the
  json.** A finding with no line number cannot be checked and does not count. Say plainly
  which kind it is (missing / extra / wrong / prose-only) and why you are confident.
- **List passes too, briefly**, so it is clear what you checked and found clean, not only
  what you flagged.
- **If the file is large, read it in section-sized chunks and keep your own ledger** — a
  scratch file of your own, never in `audit/` — resuming from where you left off, so a lost
  context does not make you skip the middle. Chunk on the `<!-- page ... :: <section_type>
  -->` markers; a reaction or route is told across sections, so grow one ledger, never a
  per-chunk list.

For the **pathways finder**, "the file" is a set of routes, so the same four questions
become: a route the `.md` describes that no record carries (missing); a route or step in
the record the patent does not support (invented); a KSM, intermediate order, product,
step chain or overall yield that disagrees with the patent (wrong); an overall yield or a
step linkage that lives only in prose (prose-only). Judge each route against the patent
alone — like the other finders, read no second json.

## Optional: hunters and the original document

Two extra kinds of no-context agent have earned their place:

- **Hunters.** Instead of auditing a whole file, a hunter is pointed at one weakness:
  "find every value in the `.md` that is not in the json", or "find every scope,
  qualifier or relationship statement the patent makes that no record carries". A narrow
  brief finds things a whole-file audit skims past.
- **The original document.** The `.md` is itself a lossy transcription. Fetch the
  published patent from its number (search Google Patents for the folder name), hand it to a fresh
  no-context agent alongside the json, and ask the same question. This catches values the
  enriched `.md` dropped before the pipeline ever saw them. Use this when the `.md` looks
  thin against what a patent of this kind should contain.

Run whichever of these the file warrants. Two whole-file finders is the floor, not the
ceiling.

## When the finders report: judge, correct, close

Now you use your context. For every finding any finder raised:

1. **Judge it against the patent and the current file**, exactly as the gold-check passes
   (7, 8, 11) judge a field: is it real? Go to the line, read it, decide.
2. **Correct the real ones as you go**, reasoning out each change before you make it and
   verifying it against the patent afterward — the same correct-as-you-go rule as Phase 3.
   A finding that is a duplicate of one already fixed is noted as such, not re-fixed.
   If you correct a value in `reactions.json` or `compounds.json` that a pathway step
   projects from, propagate it into `pathways.json` by re-copying that gold record's
   fields into the affected step (the mechanical re-projection of Prompt 11), so the three
   files cannot drift apart — never hand-edit the step to match.
3. **Settle every finding with a verdict**, none skipped:
   - `NO LONGER TRUE` — the file now has it right (you just fixed it, or it was already
     right and the finder was working from a stale reading).
   - `STILL TRUE` — a real open defect. Say what is blocking it.
   - `NEVER TRUE` — the finding itself was mistaken. Say why.

## Close the audit folder

1. **Update `audit/reactions_audit.csv`, `audit/compounds_audit.csv` and
   `audit/pathways_audit.csv`.** Every value you corrected on this pass becomes a `FIXED`
   row (old value, new value, line number in "Problem, in plain words"). A field a finder
   checked that had no row yet gets one, so the sheets still cover the whole file. After
   this pass no row may describe a value the file no longer holds.

2. **Append the Prompt 14 findings to `audit/findings.csv`**, one row per finding each
   finder raised — none skipped, because a finding with no verdict is a finding that was
   silently dropped. Use the shared findings schema, filling the verdict and resolution:

       #, Raised in pass, File, Line (.md), Finding, Status when raised, Verdict,
         Resolution / evidence

   - **Raised in pass** names the finder (`Prompt 14 reactions finder`, `Prompt 14
     pathways finder`, `Prompt 14 original-document check`). **Verdict** is
     `NO LONGER TRUE` / `STILL TRUE` /
     `NEVER TRUE`. **Resolution / evidence** points at the current file and the patent:
     the field, the value now present, the line number, so the verdict is checkable from
     its own row.

The file is not gold while any Prompt 14 verdict says `STILL TRUE`.

## Rules

- **The finders must stay blind.** Do not paste your findings, your audit sheets, or your
  reasoning into their brief. The moment they see what you found, they stop being
  independent. If a finder asks for context, tell it to work from the two files alone.
- **Run the finders in parallel.** The reactions, compounds and pathways files are
  independent; there is no reason to serialize them.
- **You judge, they find.** Do not let a finder correct the file — it lacks the context to
  do it safely. It reports; you decide and fix.
- **If you are unsure, stop and ask.** A flagged uncertainty is worth more than a
  confident invention pushed into the gold.
- **Never treat the pipeline's behaviour as correct.** It is the thing being measured.

## What to give me at the end

- The finished audit folder: all three `*_audit.csv` sheets reflecting any corrections, and
  the Prompt 14 rows of `audit/findings.csv` with a verdict on every finding.
- Each finder's report, and what came of it: how many findings, how many real, what you
  fixed, with record indices and line numbers.
- A plain statement of whether the file is still gold after this pass, and if not, exactly
  what is blocking it.
- Anything you could not settle, with both readings rather than a guess.

Ask me if anything is unclear before you start, and stop and ask if you get stuck part
way through rather than guessing.
