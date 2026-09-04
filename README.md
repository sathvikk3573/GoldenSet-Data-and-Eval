# GoldenSet — Data and Eval

Hand-checked reference annotations of patent chemistry, in the same JSON shapes the
extraction pipeline writes, so the two can be compared field by field.

Four patents are annotated and verified. Everything needed to read the gold, to make
more of it, and to score a pipeline run against it is in this repo.

## Layout

    data/       the gold, one folder per patent
      <PATENT_ID>/
        input/    the source PDF and the translated line-numbered markdown
        output/   reactions.json, compounds.json, pathways.json  <- the GOLD
        audit/    what was flagged, fixed or withdrawn, and why

    prompts/    the twelve prompts that build the gold, run in order, per patent
    manual/     the operator manual: how to run a patent end to end
    reports/    rendered, human-readable views of the data

The markdown in `input/` is line-numbered on purpose: every value in the gold traces
back to a line in it, and every finding was recorded against a line number.

Per-patent notes, the conventions the files follow, the structure layer and what is
deliberately left out are in [data/README.md](data/README.md).

## Four caveats to carry with the data

1. **No human has read this yet**, except for WO2024109718A1, which the annotator read
   and accepted on 2026-09-02, and WO2022024094A1, where the reviewer made every
   judgement call. Every other check in every run was automated or made by a model.
2. **The extraction and the audit were both Claude.** Where the model misread
   something and the audit read it the same way, nothing in this process can see it.
3. **The four shipped patents predate the current prompt set.** They were built with the
   earlier eight-prompt version, so they carry the old `audit/` layout (`AUDIT-LOG.md`,
   `RUN-NOTES.md`, `GOLDEN-DATASET-FINDINGS.xlsx`) rather than the four CSVs
   [prompts/README.md](prompts/README.md) now mandates, and none of them went through
   the pathways passes (10–12) as written.
4. **`pathways.json` is not gold.** In all four patents it is raw pipeline output (the
   A3 pass); prompts 1–8 only ever touched reactions and compounds. In CN109678767A it
   is known stale — it still references the pre-split reaction ids and the old count of
   36, because the annotator directed that only `reactions.json` be edited. Treat the
   pathways column below as a record count, not a verified one.

## Where to start

- Making gold for a new patent → [manual/](manual/), then [prompts/README.md](prompts/README.md)
- Reading the gold → [data/README.md](data/README.md)
