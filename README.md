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

## Where to start

- Making gold for a new patent → [manual/](manual/), then [prompts/README.md](prompts/README.md)
- Reading the gold → [data/README.md](data/README.md)
