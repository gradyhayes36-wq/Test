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

## The disease still is not the mechanism

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
