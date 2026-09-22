# Natural-spawn production run, 1488 -> 1565 (in progress)

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

---

# 1565 checkpoint: target reached, disease still silent

## Mesoamerica

| Date | Pop | Fill | Rate over interval |
|---|---|---|---|
| 1488 | 11,773.58k | 0.45693 | - |
| 1536 | 6,876.56k | 0.32527 | -1.021%/yr |
| 1565 | **4,605.83k** | **0.22234** | **-1.382%/yr** |

**The decline is accelerating**, not decaying - the opposite of every previous
build, where the rate fell away after the first two decades.

| | This run | Previous build |
|---|---|---|
| Mesoamerica 1565 | **4,605.83k** | 7,476.6k |
| vs control (14,343.0k) | **-67.9%** | -47.9% |
| 1600 projection | **~2,839k** | 5,930k |

**~2.84M at 1600 is inside the 1-3M design target.** First build to get there.

## The Andes resolved itself

Last checkpoint flagged the Andes as the outstanding exposure: 8,609.35k at
0.876 fill, apparently untouched. Contact reached it.

| Date | Andes | Fill |
|---|---|---|
| 1536 | 8,609.35k | 0.87553 |
| 1565 | 6,265.28k | 0.57837 |

-27.2% in 29 years, -1.096%/yr. The capacity ceiling that was holding it at
0.876 is no longer binding.

## Hemisphere at 1565

New World total **21,559.9k against a 36,298.0k control: -40.6%**.

This is *not* comparable to the previous build's 20,475.5k, despite looking
similar. That run had heavy player colonisation on the East Coast, Canada and
Brazil, which inflated those regions; this run is a Spain game with one
Caribbean colony and one Mesoamerican conquest, leaving Great Lakes (3,197.9k,
fill 0.341) and East Coast (1,328.5k) as large uncontacted native populations.
**Mesoamerica is the only clean cross-run comparison**, and it is far better.

| Region | 1536 | 1565 | Change |
|---|---|---|---|
| Mesoamerica | 6,876.56k | 4,605.83k | -33.0% |
| Andes | 8,609.35k | 6,265.28k | -27.2% |
| Central America | 818.24k | 460.09k | -43.8% |
| Brazil | 1,435.92k | 1,022.74k | -28.8% |
| Colombia | 2,350.22k | 1,985.74k | -15.5% |
| Aridoamerica | 473.41k | 482.17k | +1.9% |
| Caribbean | - | 343.95k | settler growth |

## Cocoliztli contributed nothing

```
::NWPSTAT::1536:virgin_soil=4366:agri_collapse=191:labour_regime=1444:cocoliztli_locs=0
::NWPSTAT::1565:virgin_soil=4289:agri_collapse=190:labour_regime=1793:cocoliztli_locs=0
```

Zero `::NWPSPAWN::` lines in 3,077 log lines. `cocoliztli_locs=0` at both
checkpoints. Mesoamerican `agri_collapse` frozen at **23 of 325** for the whole
29-year interval, because that stamp is disease-gated and there is no disease.

The entire -67.9% is produced by `labour_regime` (1,444 -> 1,793 locations),
`virgin_soil` (~4,300) and M&T's own measles (370 -> 277 locations).

### Why the spawn probably never fires

At 0.004/month over 77 years, never rolling is a 2.5% outcome - unlikely but
not impossible. The likelier explanation is the limit:

```
OR = {
    owner_from_old_world = yes
    has_variable = had_great_pestilence_in_location
    location_has_pestilence_trigger = yes
}
```

**The spawn requires a location Europe already owns.** In this run Spain owns
one Mesoamerican location. Stamping, by contrast, runs off `nwp_contacted`,
which the Situation sets on *every* location in a region once Europe owns *any*
location in it. So the spawn gate is orders of magnitude stricter than the
stamp gate covering the same ground - and historically backwards, since
cocoliztli and smallpox ran far ahead of the conquistadors.

One-line fix, not applied pending a decision on overshoot: swap
`owner_from_old_world = yes` for `has_variable = nwp_contacted`.

A `::NWPROLL::` diagnostic has been added to the spawn block to separate "never
rolled" from "rolled and found nothing eligible" on the next run.

---

## Changes made after this checkpoint

Both applied together, since the second only makes sense given the first.

**1. The spawn gate follows contact, not ownership.**

```
-    owner_from_old_world = yes
+    has_variable = nwp_contacted
```

`nwp_contacted` is the flag the Situation already sets on every American
location in a region once Europe owns any location in it. The disease now
spawns on the same footprint the stamping sweeps already cover.

**2. `nwp_colonial_labour_regime` population growth -0.012 -> -0.009.**

A deliberately modest cut. At -0.012 across 1,793 locations this modifier was
producing the whole -67.9% on its own; with the disease live,
`nwp_agricultural_collapse` (-0.010) comes off its frozen 23 of 325 and the
disease's own -0.05 location modifier applies during waves.

Rough decomposition of the measured -1.382%/yr:

| Component | Approx. contribution |
|---|---|
| Control growth trend | +0.26 |
| labour_regime, ~100% coverage | -1.20 |
| agri_collapse, 7% coverage | -0.07 |
| measles, virgin_soil, residual | -0.37 |
| **Net** | **-1.38** |

Substituting -0.009 for labour regime and assuming agricultural collapse
reaches 70-100% coverage gives **-1.7 to -2.0%/yr sustained**, which projects
from 4,605.83k at 1565 to **roughly 2.3-2.5M at 1600 before epidemic mortality
spikes**, and plausibly 1.8-2.3M with them.

This decomposition is rough - it attributes the residual to measles and virgin
soil without isolating either, and it assumes coverage figures that have not
been measured. Treat the projection as a direction, not a number.
