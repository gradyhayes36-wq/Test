# Validating this submod

The design spec called for an observer run with Mexico's population checked at
1550 / 1600 / 1700 / 1800. M&T already ships the instrument for that, so this
does not need a save file or any manual counting.

## M&T's census logger

`in_game/events/SYS-CENSUS.txt` defines a yearly logger, fired from
`pulse_yearly_MnT`. `in_game/common/scripted_effects/SYS-scripted_effect.txt`
holds what it writes. The line we care about:

```
print_regional_population = {
    every_region = {
        error_log = "::POP::[GetCurrentYear]:[THIS.GetRegion.GetNameWithNoTooltip]:[THIS.GetRegion.GetTotalPopulation]"
    }
}
```

One line per region per year, straight into `error.log`. `mesoamerica_region`
is a real region — M&T's own great_pestilence situation checks it by name —
so central Mexico's population is a single greppable field, every year,
automatically.

## Procedure

1. **Enable it.** `LOGGER_CHARTS.01` is marked `orphan = yes`, so it can be
   fired from the console:

   ```
   event LOGGER_CHARTS.01
   ```

   It sets a global variable and logs `"Enabled: Logging yearly non-country
   information"`. `LOGGER_CHARTS.04` turns it back off.

2. **Run.** `LOGGER_CHARTS.02` fires once a year from `pulse_yearly_MnT` and
   calls `run_logging`.

3. **Filter before doing anything else.** The same global variable also enables
   `print_regional_prices`, which M&T's own comment measures at **765,974
   characters per year** — about 900KB/year all told. Over two centuries
   `error.log` becomes far larger than a save file. Pull out the population
   lines and discard the rest:

   ```
   # Windows
   findstr "::POP::" error.log > pop.txt
   # PowerShell
   Select-String "::POP::" error.log | ForEach-Object { $_.Line } > pop.txt
   # bash
   grep "::POP::" error.log > pop.txt
   ```

   That leaves roughly 10KB per year. Filtering further to
   `findstr "mesoamerica" pop.txt` gives one line per year — a couple of
   hundred lines for the whole run.

4. **Plot it, or read it.** `tools/plot/log_parser.py` already parses `::POP::`
   with `r"::POP::(\d+):(.+?):([\d.,MK]+)"` and `tools/plot/MT_grapher.py`
   graphs it. Or just read the numbers.

## Reading the result

Success is 1–3M in mesoamerica_region at 1600 **and still under ~4M at 1700**.
The second number is the real test; the first is easy to hit and easy to lose
again.

A single year's output is also enough to answer the question that started this
project — whether the 20M+ figure is real — without running anything forward.
Enable the logger on an existing late save, let one year tick, grep one line.

## Before building further

The design spec's other validation note still stands: console
`situation great_pestilence` on a live save to re-fire it and read the actual
death toll. The Situation may not be firing at all rather than firing weakly —
different bug, different fix. There are reports of it hanging with two or three
permanently-infected locations and never ending.
