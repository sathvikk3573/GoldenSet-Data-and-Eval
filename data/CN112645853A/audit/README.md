# Audit trail: CN112645853A

Everything behind the two gold files in `../output/`. Read this to see what was
checked, what was found, and what was decided.

| file | what it is |
|---|---|
| `GOLDEN-DATASET-FINDINGS.xlsx` | the findings log, five tabs |
| `AUDIT-LOG.md` | per-record reasoning, written while the audit ran |
| `RUN-NOTES.md` | how the extraction was produced, and by which model |
| `compounds-read-from-patent.md` | the 56 compounds read from the patent, independently |
| `compounds-read-from-patent.tsv` | the same list, machine readable |

## The findings log

    Reactions             26 findings   all closed
    Reactions Withdrawn   31 rows       each with the reason it was overturned
    Compounds              6 findings   all closed
    Compounds Withdrawn    1 row        withdrawn on the reviewer's call
    Legend                              columns, enums, both reconciliations

Withdrawn findings are kept rather than deleted. Each row carries the rule or the
evidence that overturned it, so any reversal can be audited instead of taken on trust.

## The headline numbers

    reactions   the patent describes 55 transformations; the extraction found 54
                the miss was the side reaction at line 147
    compounds   the patent names 56 compounds; the extraction found 43
                the 13 missing were generic class terms, placeholders and background
                context; the only one inside the claims was "alkali metal alkoxide",
                a reagent route of the invention

Nothing was invented by the extraction in either file.

## Two caveats that travel with this data

1. **No human has read it.** Every check was automated or made by a model.
2. **The extraction and the audit were both Claude.** Where the model misread something
   and the audit read it the same way, nothing in this process can see it.
