# Full run, 1488 -> 1660: the design target is met

Setup: fresh 1488 start, no forced seed. Spain given one Caribbean colony, then
one conquered Mesoamerican location in the early 1500s. Natural spawning live.

## Headline

| Milestone | Mesoamerica | vs 1488 |
|---|---|---|
| 1488 start | 11,773.6k | - |
| 1565 | 4,577.1k | -61.1% |
| **1600** | **2,932.8k** | **-75.1%** |
| **1633 trough** | **1,685.7k** | **-85.7%** |
| 1660 | 2,025.7k | -82.8% |

**Design target was 1-3M at 1600. Actual: 2.93M.** The 1565 projection called
2,839k; the run delivered 2,932.8k, 3.2% off.

Against the +0.257%/yr control trend, the counterfactual 1660 figure is
~18,310k. Actual is **-88.9% against control**.

## Cocoliztli works, 100 years late

Nine natural spawns, first at 1569:

| Year | Seed pop |
|---|---|
| 1569 | 10.46k |
| 1594 | 11.05k |
| 1594 | 16.64k |
| 1606 | **39.18k** |
| 1619 | 21.44k |
| 1631 | **20.78k** |
| 1642 | 10.11k |
| 1652 | 10.22k |
| 1658 | 13.45k |

Eight fizzled. The ninth, at **1631**, ignited:

| Year | Mesoamerica | Infected locations |
|---|---|---|
| 1630 | 2,397.4k | 0 |
| **1631** | **1,922.2k** | **213** |
| 1632 | 1,691.1k | 5 |
| 1633 | 1,685.7k | 0 |

**-29.7% in two years across 213 of 325 locations.** This is the cocoliztli
event the submod was built for - arriving in 1631 rather than 1545/1576.

Note the seeds: the 39.18k spawn in 1606 fizzled and the 20.78k spawn in 1631
took the region. **Density is confirmed irrelevant to establishment for the
third time.** Establishment is stochastic.

The `population > 10` gate is nonetheless doing its job - every seed landed
between 10.1k and 39.2k, none in a thin colonial outpost.

## The one spec deviation: recovery begins 1633, not 1700

```
::NWPSTAT::1536: agri_collapse=191  labour_regime=1444  virgin_soil=4366
::NWPSTAT::1565: agri_collapse=190  labour_regime=1793  virgin_soil=4289
::NWPSTAT::1660: agri_collapse=0    labour_regime=0     virgin_soil=3255
```

Mesoamerican `agri_collapse` fell from 23 to **0 at 1590** - those stamps were
issued around 1530 and expired on the 60-year clock, and could not be renewed
because the disease was absent for the whole intervening century. `labour_regime`
is at 0 by 1660: last issued 1600 under the cutoff, expired by 1660.

So from 1633 the region recovers at **+0.68%/yr**, reaching 2,025.7k by 1660.
The design spec's hardest requirement was that recovery must not start before
~1700.

Whether this is a failure is arguable. Cook & Borah's series bottoms around
1605-1620 and stays low into the mid-century before turning up, so a 1633 trough
with slow recovery is defensible - arguably closer to the historiography than
the spec's 1700. But it is a deviation and should be a deliberate choice, not an
accident of stamp arithmetic.

Levers, if the floor should hold longer:
- raise the stamp issuance cutoff from 1600 toward 1650
- lengthen stamp duration past 60 years
- drop the disease gate on `agri_collapse`, which is what let it lapse at 1590

## Hemisphere at 1660

New World total **15,313.6k**.

| Region | 1660 | Fill | Note |
|---|---|---|---|
| Great Lakes | 2,948.8k | 0.305 | uncontacted natives |
| Mesoamerica | 2,027.4k | 0.081 | |
| East Coast | 1,893.0k | 0.033 | settler growth |
| Andes | 1,722.4k | 0.083 | **-80.0% from 1536** |
| Canada | 1,640.2k | 0.056 | settler growth |
| Colombia | 1,275.8k | 0.066 | |
| Brazil | 1,112.4k | 0.027 | |
| La Plata | 540.8k | 0.070 | |
| Chaco | 460.9k | 0.059 | |
| Caribbean | 435.9k | 0.048 | settler growth |
| Aridoamerica | 392.5k | 0.039 | |
| Central America | 300.4k | 0.030 | |
| Great Plains | 274.9k | 0.013 | |
| West Coast | 216.5k | 0.030 | |
| Alaska | 71.5k | 0.009 | |

The Andes fell 8,609.4k -> 1,722.4k between 1536 and 1660, fill 0.876 -> 0.083.
The two dense agrarian cores both collapsed by ~80-86%.

Great Lakes at 2,948.8k and fill 0.305 is the remaining uncontacted pool. East
Coast, Canada and the Caribbean are rising on settler population, which is the
intended behaviour - the collapse modifiers lift once a location is no longer
native-majority.
