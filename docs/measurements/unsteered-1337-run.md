# Unsteered 1337 run - the production test

No player intervention at any point. Fresh 1337 start, observer, no console
events fired. The Cortes expedition is the only thing substituting for the
hand-placed colony and conquest that every earlier result depended on.

## Headline

| Date | Mesoamerica | Fill | Note |
|---|---|---|---|
| 1435 | 10,763.44k | 0.430 | pre-contact baseline |
| 1484 | 11,160.92k | 0.432 | still pre-contact |
| **1510** | **11,727.3k** | 0.557 | **peak - expedition fires, contacted 0 -> 325** |
| 1558 | 6,120.9k | 0.294 | |
| **1600** | **3,426.9k** | | **-70.8% from peak** |
| 1604 | 3,310.7k | 0.166 | |

**-1.367%/yr sustained over 90 years.**

## The decline did not decay

This is the difference from every earlier build.

| Interval | Rate |
|---|---|
| 1510-1558 | -1.355%/yr |
| 1558-1600 | **-1.381%/yr** |

Flat. Earlier builds fell away after two decades - the 1511-1528 run went
-1.52%/yr and decayed to -0.66%/yr. This one holds its rate for nine decades.

## Against the design target and the steered run

| | 1600 |
|---|---|
| Design target | 1-3M |
| **This run, unsteered** | **3.43M** |
| Hand-steered 1660 run | 2.93M |

14% above the target's upper bound, and only **16.8% above the steered run** -
which is the real result here. A game where nobody placed a colony, conquered a
province or fired a console command lands within a sixth of one where the
player did all three. The Cortes expedition substitutes for player
intervention, which is what it was built to do.

## Hemisphere at 1604

New World total **17,039.0k against 34,116.8k at 1484: -50.1%.**

| Region | 1510 | 1604 | |
|---|---|---|---|
| Mesoamerica | 11,727.3k | 3,308.5k | -71.8% |
| Andes | 8,285.4k | 3,684.9k | -55.5% |
| Brazil | 2,419.8k | 1,010.4k | -58.2% |
| Central America | 1,808.4k | 917.9k | -49.2% |
| East Coast | 1,816.6k | 1,039.9k | -42.8% |
| Colombia | 2,961.5k | 1,833.7k | -38.1% |

## Correction: the disease IS the mechanism, it just arrives late

The section below was written at the 1600 checkpoint and its conclusion is
wrong for the full run. Kept because the 1600 reading itself was accurate.

Three more natural spawns followed, with the eligible-location count climbing
as European ownership spread - 107 in 1516, then 298, 354, 389:

```
::NWPSPAWN::1650:cocoliztli spawned:pop=22.39528
::NWPSPAWN::1671:cocoliztli spawned:pop=16.12683
::NWPSPAWN::1681:cocoliztli spawned:pop=11.12459
```

The 1650 spawn established. It reached **240 of 325 locations** and took
Mesoamerica from 2,737.7k to **1,944.1k in two years - -29.0%** - then burned
out inside three.

| Year | Mesoamerica | Infected |
|---|---|---|
| 1649 | 2,737.7k | 0 |
| **1650** | 2,203.3k | **240** |
| **1651** | **1,944.1k** (trough) | 237 |
| 1652 | 1,963.2k | 3 |
| 1653 | 1,982.9k | 0 |

That is the second time an established cocoliztli wave has been measured, and
the two agree closely: -29.5% over 1631-32 in the steered run, -29.0% over
1650-51 here. When it takes hold it is worth roughly a third of the region in
two years, reliably.

**Trough: 1,944.1k in 1651, -83.4% from the 1510 peak.** Without that wave the
region would have bottomed near 2,700k - so the disease is worth about 30% of
the total collapse, and all of the bottom.

## Trough, recovery and teardown

| Phase | Rate |
|---|---|
| 1510-1600, collapse | -1.367%/yr |
| 1600-1649, slow grind | -0.458%/yr |
| 1650-51, epidemic | -29.0% total |
| 1651-1704, recovery | **+0.789%/yr** |

Recovery matches the steered run almost exactly (+0.708%/yr there). By 1704
Mesoamerica is back to 2,952.7k, still 74.8% below its 1510 peak.

Stamp teardown is on schedule: at 1704 `agri_collapse=0` and `labour_regime=0`,
both expired as designed (last issued before the 1600 cutoff, 60-year
duration). Only `virgin_soil` remains at 3,317, and the Situation is still
ACTIVE pending its 1730 end date.

## Hemisphere at 1704

New World total **23,252.8k, -31.8% against 1484** - recovered from the -50.1%
low at 1604, and the recovery is overwhelmingly colonial rather than
indigenous: Canada 220k -> 1,823k, East Coast 1,040k -> 4,228k, Caribbean 192k
-> 826k. The 1600s colonial boom is modelled.

## The 1600 reading, as written at the time


```
::NWPROLL::1516:spawn roll succeeded:eligible_ignoring_climate=107
::NWPSPAWN::1516:cocoliztli spawned:pop=97.66374
```

One natural spawn in ninety years, and the density gate worked exactly as
designed - 107 locations eligible, and it landed in a **97.7k-pop** location
rather than a thin colonial outpost. But `cocoliztli_locs` is 0 at every
checkpoint after it, and `agri_collapse` peaked at 7 locations and is 0 at
1604, because that stamp is disease-gated.

M&T's own diseases are absent too: malaria, smallpox and measles all 0 through
1558 and 1604. The 1660 run had measles in 370 locations.

**So the entire -70.8% is `labour_regime` (1,848 locations at 1604) and
`virgin_soil` (4,219), with no epidemic contribution at all.** That is a
stronger result for the modifier pathway than any previous run, and it means
the disease remains decoration rather than mechanism.

## One unexplained event

1598 -> 1599 drops 13.1% in a single year, 4,179.8k -> 3,631.2k, with
`cocoliztli_locs=0` and no M&T disease active. Not identified. Candidates are a
war, a famine cascade, or a large cohort of stamps landing at once before the
1600 issuing cutoff.


```
::NWPROLL::1516:spawn roll succeeded:eligible_ignoring_climate=107
::NWPSPAWN::1516:cocoliztli spawned:pop=97.66374
```

One natural spawn in ninety years, and the density gate worked exactly as
designed - 107 locations eligible, and it landed in a **97.7k-pop** location
rather than a thin colonial outpost. But `cocoliztli_locs` is 0 at every
checkpoint after it, and `agri_collapse` peaked at 7 locations and is 0 at
1604, because that stamp is disease-gated.

M&T's own diseases are absent too: malaria, smallpox and measles all 0 through
1558 and 1604. The 1660 run had measles in 370 locations.

**So the entire -70.8% is `labour_regime` (1,848 locations at 1604) and
`virgin_soil` (4,219), with no epidemic contribution at all.** That is a
stronger result for the modifier pathway than any previous run, and it means
the disease remains decoration rather than mechanism.

## One unexplained event

1598 -> 1599 drops 13.1% in a single year, 4,179.8k -> 3,631.2k, with
`cocoliztli_locs=0` and no M&T disease active. Not identified. Candidates are a
war, a famine cascade, or a large cohort of stamps landing at once before the
1600 issuing cutoff.

---

## The complete series

`data/unsteered-1337-mesoamerica.csv` holds the full year-by-year record,
1499-1704, 206 samples, recovered from the live log archiver rather than a
truncated `error.log` tail. Peak 11,719.9k (1509), trough 1,944.1k (1651), end
2,952.7k (1704).

Biggest single-year falls:

| Years | | |
|---|---|---|
| 1649-1650 | 2,737.7k -> 2,203.3k | **-19.5%** (cocoliztli) |
| 1598-1599 | 4,179.8k -> 3,631.2k | -13.1% |
| 1631-1632 | 2,739.8k -> 2,402.5k | -12.3% |
| 1650-1651 | 2,203.3k -> 1,944.1k | -11.8% (cocoliztli) |
| 1539-1540 | 8,114.5k -> 7,532.4k | -7.2% |

Three of the five largest are not the epidemic, and none of those three has an
identified cause - `cocoliztli_locs=0` and M&T's diseases absent at each. They
are most likely war, famine cascades, or large stamp cohorts landing together.

## An instrumentation limitation, found in the archive

The 1516 spawn never appears in the tracker. `cocoliztli_locs` reads 0 at every
monthly sample through 1516-1517 - yet `nwp.1` fired **nine times** in those two
years, and that event only fires when the disease spreads to a country.

The explanation is in the tracker's own scope: `nwp_trk_coc` counts locations
**in mesoamerica_region only**. The 1516 spawn landed in a 97.7k-pop location,
far above the Mesoamerican average of ~34k at that date, so it was plausibly in
the Andes - where the tracker cannot see it.

So `cocoliztli_locs=0` has always meant "none in Mesoamerica", not "none
anywhere", and every earlier statement in these notes that the disease was
absent should be read with that caveat. A hemisphere-wide counter would fix it.

Related: `nwp.1 fired, stamping 1 locations` is the typical line - the
country-spread event reaches one location at a time, which is why the monthly
sweep in the Situation does the real stamping work. That was the 1503 coverage
fix and it is still holding.

