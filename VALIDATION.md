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

## Procedure (M&T's logger — see the caveat below first)

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

## M&T's population logger appears to be broken on 1.3

A log from a live 1677 save, filtered for `Mesoamerica`, returned `::GP::`
(prices) and `::RT::` (roads) records and **no `::POP::` at all** — and no
`::BT:` either.

The line numbers are the evidence. The first `Mesoamerica` price hit was at
`error.log:81`, and subsequent ones at 163, 245, 327 — exactly 82 apart, one
good's full region loop. So `::GP::` is the first thing in the file. But
`run_logging` calls `print_regional_building_types` and
`print_regional_population` *before* prices, and M&T's own comment measures the
building-types output at 147,474 characters. Had those run, thousands of lines
would precede line 81.

Corroborating: in `print_country_information`, the main `::TG::` line is
commented out in M&T's source. These loggers do rot against game updates.

Confirm with:

```powershell
Select-String "::POP::" error.log | Measure-Object
Select-String "::BT:"   error.log | Measure-Object
Get-Content error.log -Head 40
```

## Fallback: this submod's own census

`nwp.9` in `in_game/events/nwp_collapse_events.txt` is a console-fired census,
built only from patterns observed working in that same log:

| Pattern | Observed output |
|---|---|
| `[GetCurrentYear]` | `1677` |
| `[THIS.GetRegion.GetNameWithNoTooltip]` | `Mesoamerica` |
| `[SCOPE.GetLocalVariable('x').GetValue]` | price values |

It sums `population` over `every_ownable_location_in_region` into a local
variable rather than calling `GetTotalPopulation`, which is the one promote
`print_regional_population` uses that this does not — and therefore the prime
suspect.

```
event nwp.9
```

then

```powershell
Select-String "::NWPPOP::" error.log
```

It is `orphan = yes`, so it fires from the console on demand at any date,
against any save, with no pulse or situation required. Output is one line per
region — about 40 lines, not 900KB.

## Baseline: 1676

Measured. See `docs/measurements/`. Mesoamerica 19.8M against a 1–3M target,
in a world that is itself running at ~60% of historical — so the distortion is
worse as a share than in absolute terms. Mesoamerica has been flat at ~20M
since the 1337 start, which suggests a population pinned at its carrying
capacity rather than one that was never killed.

`nwp.9` now logs three fields:

```
::NWPPOP::<year>:<region>:<population>:<avg fill ratio>:<inhabited locations>
```

The fill ratio is the test. Near 1.0 means capacity-limited, and mortality
alone can never hold the number down.

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
