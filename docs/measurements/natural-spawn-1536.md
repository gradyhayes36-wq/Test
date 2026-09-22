# Natural-spawn production run, 1488 -> 1537 (in progress)

Setup: fresh 1488 start, no forced seed. Spain given one Caribbean colony,
then one conquered Mesoamerican location in the early 1500s. First run with
natural spawning live after the `population > 10` gate was restored.

## Mesoamerica

| | Value |
|---|---|
| 1488 start | 11,773.58k (fill 0.45693) |
| 1536 | **6,876.56k** (fill 0.32527) |
| Change | **-41.6% over 48 years** |
| Mean rate | **-1.120%/yr** |
| Rate over the last 20 monthly ticks | **-1.021%/yr** |

The fill ratio falls with the population (0.457 -> 0.325), so this is a real
demographic decline, not capacity tracking a shrinking ceiling.

Against the established post-contact control trend of +0.257%/yr, the
counterfactual 1536 figure is ~13,319k. Actual is **-48.4% against control** -
the same ratio the previous build reached at 1565, arrived at 29 years earlier.

Projections at the observed -1.021%/yr:

| Date | This run | Previous build |
|---|---|---|
| 1565 | ~5,114k | 7,476.6k (measured) |
| 1600 | **~3,577k** | 5,930k |

Design target at 1600 is 1-3M. This is the first build to come within reach of
it, and the trough will sit lower still: stamping stops issuing at 1600 but
60-year stamps carry the decline into the 1650s before recovery begins.

## But the disease is not doing the work

```
::NWPSTAT::1536:virgin_soil=4366:agri_collapse=191:labour_regime=1444:cocoliztli_locs=0
::NWPDIS::1536:malaria=0:smallpox=0:measles=370:influenza=10
```

**`cocoliztli_locs=0`.** Cocoliztli is absent from the map at 1536 and has been
for the whole tracked window. The -1.12%/yr is being produced by:

- `labour_regime` on **1,444** locations - contact-gated, not disease-gated,
  and therefore unaffected by whether cocoliztli ever spawns
- `agri_collapse` on 191 hemisphere-wide, but only **23 of 325** in Mesoamerica
- `virgin_soil` on 4,366 locations
- M&T's own measles, running in **370** locations

So the demographic result is being carried almost entirely by the modifier
pathway. The disease pathway - the thing three runs were spent debugging - is
still not establishing on its own.

Open: the log excerpt is a tail, so it cannot show whether `::NWPSPAWN::` ever
fired. Needs a grep of the full log to distinguish "never spawned" from
"spawned and burned out".

## Other regions at 1536

| Region | Pop | Fill |
|---|---|---|
| Andes | 8,609.35k | **0.87553** |
| Colombia | 2,350.22k | 0.17319 |
| Brazil | 1,435.92k | 0.06084 |
| Central America | 818.24k | 0.14436 |
| Chaco | 580.63k | 0.07503 |
| Aridoamerica | 473.41k | 0.05561 |
| La Plata | 470.22k | 0.06757 |
| Great Plains | 262.19k | 0.01489 |
| West Coast | 224.29k | 0.03088 |

The Andes at 0.876 fill and 8.61M is the outstanding exposure: the largest
surviving population in the hemisphere, sitting near its capacity ceiling,
apparently untouched. If Spain never reaches Peru in this run it stays that
way, and the New World total stays high however hard Mesoamerica falls.
