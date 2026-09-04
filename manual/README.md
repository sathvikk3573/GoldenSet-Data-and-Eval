# Operator manual

`golden-patents-manual.html` and a Chrome-rendered `golden-patents-manual.pdf` beside it.
Revision 0.2, 2026-09-04. White, gold and black; text diagrams instead of prose.

Three sections so far, and deliberately short:

1. What the golden dataset is, in one line.
2. Before you start: clone the repo, run the patent, build `<PATENT_ID>/input, output`,
   open Claude Code there, paste the prompts from [../prompts/](../prompts/) in order.
   The `audit/` folder with its four CSVs is created by the prompts.
3. Your role: Claude reads and flags; you decide every flag and order the fix.

The per-pass chapters come next. Where this manual and the prompts disagree, the prompts win.
`golden-patents-manual.v0.1-long.html.bak` is the previous long revision, kept for reference.
