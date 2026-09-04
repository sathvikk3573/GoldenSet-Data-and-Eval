# Operator manual

`golden-patents-manual.html` (printable A4, two columns) and a Chrome-rendered
`golden-patents-manual.pdf` beside it. Revision 0.1, 2026-09-03.

**It is one revision behind the prompts.** The manual was written before the twelve-prompt
set in [../prompts/](../prompts/) was delivered. Two things in it are now stale:

- It numbers prompt **roles** P1–P15, and cites file paths under
  `patent-extraction/prompts-for-gold/` for files 1–6 only. The shipped set is 1–12, and
  it lives in [../prompts/](../prompts/). The role numbers were never file numbers, but
  they no longer line up either — file 9 is the adversarial pass and 10–12 are pathways.
- It mandates an `audit/` folder holding `AUDIT-LOG.md`, `RUN-NOTES.md` and
  `GOLDEN-DATASET-FINDINGS.xlsx`. [../prompts/README.md](../prompts/README.md) now
  mandates exactly four CSVs instead: `reactions_audit.csv`, `compounds_audit.csv`,
  `pathways_audit.csv`, `findings.csv`.

Where the two disagree, **the prompts win** — they are what actually runs. The manual's
chapters on how a run is organised, and why each pass is shaped the way it is, still hold.
