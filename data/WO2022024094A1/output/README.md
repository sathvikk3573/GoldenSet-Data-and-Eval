# WO2022024094A1 gold

    reactions.json   20 records
    compounds.json   52 records

Both files intentionally carry fields that `pipeline/schemas` does not define, because
the patent states things the shipped schema cannot hold. See `../audit/README.md` for
the list and the reason for each.

## What "20" and "52" mean

`reactions.json` 20 = 7 worked examples + 9 prior-art processes from five cited patents
+ 3 invention route steps + 1 in-situ catalyst preparation. Only the 7 Examples are
uncontestable; the other 13 depend on conventions chosen during the run, and those
conventions are recorded in `../audit/README.md`. A benchmark scored on the count
without them measures convention-agreement, not extraction quality.

`compounds.json` 52 = 39 named substances + 12 generic class terms + 1 enzyme, held in
52 records because hydrochloric acid is kept as two, concentrated and aqueous, whose
charges differ. The 11 class nouns are the words the claims actually recite, "an
oxidant", "a base", "a solvent", and the extraction had recorded none of them.
