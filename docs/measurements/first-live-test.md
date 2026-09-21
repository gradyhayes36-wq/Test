# First live test, 1488 save + submod, Spain given Veracruz

## Chain status

```
::NWPSTAT::1488:virgin_soil=4378:agri_collapse=0:labour_regime=0:cocoliztli_locs=0
::NWPSTAT::situation nwp_demographic_collapse ACTIVE
```

| Link | Status |
|---|---|
| Situation loads and starts | **works** |
| `can_start` passes on contact | **works** |
| `on_monthly` sweep | **works** |
| `dominant_culture` test | **works** |
| `add_location_modifier` | **works** |
| `nwp_virgin_soil` applied | **works — 4,378 locations** |
| cocoliztli spawned | not yet |
| `nwp.1` / collapse modifiers | waiting on cocoliztli |

4,378 against ~4,434 populated New World locations, so essentially every
native-majority American location got stamped. Half A's **application path is
confirmed end to end.**

cocoliztli at zero is expected, not a failure: `monthly_spawn_chance` is
0.004, about one roll in 250 months, so the wait is ~20 years.

Also note: mid-save initialisation **works**. The Situation started on a save
that predates the submod.

## The unexplained drop

Same save, same date, against the control measured before the submod existed:

| Region | control | with submod | change |
|---|---|---|---|
| **Mesoamerica** | 11,768.4k | 10,018.7k | **−14.87%** |
| Aridoamerica | 712.1k | 659.3k | −7.40% |
| Central America | 1,805.1k | 1,724.1k | −4.49% |
| Andes | 8,248.8k | 8,254.8k | +0.07% |
| Colombia | 3,011.1k | 3,012.0k | +0.03% |
| East Coast | 1,760.2k | 1,761.7k | +0.09% |
| Caribbean | 149.1k | 149.1k | 0.00% |
| France | 10,555.6k | 10,594.5k | +0.37% |
| Britain | 3,524.0k | 3,534.6k | +0.30% |

Mesoamerica's fill ratio fell 0.457 → 0.389 alongside it.

The Old World is unchanged to within 0.4%, confirming this is the same save
and not a different run. Something removed **1.75M people from Mesoamerica**,
plus smaller amounts from its neighbours, while leaving the Andes, Colombia,
the East Coast and the Caribbean untouched.

**It is not cocoliztli** — that has not spawned.

Two candidates:

1. **`nwp_virgin_soil` × endemic malaria.** The modifier carries
   `local_disease_resistance = -0.75`, which makes *existing* diseases
   deadlier. Malaria is endemic, environmental rather than person-to-person,
   and carries `mortality_rate = { 0.85 0.95 }`. Its `environmental_infection`
   block multiplies by 0 for `topography = mountains` — which would explain why
   the Andes are untouched while lowland Mesoamerica is hit hardest.
2. **M&T's own Great Pestilence.** Giving Spain Veracruz satisfies
   `can_start`, and the Situation force-spawns smallpox at `value = 1` in a
   random Old-World-owned American location — which would be Veracruz.

The regional pattern fits (1) better than (2): a single smallpox outbreak
should be concentrated near its origin, not spread across Mesoamerica,
Aridoamerica and Central America simultaneously in the same year.

**`nwp.9` now reports presence counts for all five diseases** to settle it:

```
::NWPDIS::<year>:malaria=N:smallpox=N:measles=N:influenza=N
```

If malaria is present in hundreds of locations, hypothesis 1. If smallpox is
in a handful near Veracruz, hypothesis 2.

## Why this matters either way

If it is malaria, `nwp_virgin_soil` at −0.75 is **far too strong** — it
produced a 15% regional loss essentially on contact, before any epidemic
arrived, and it would keep doing so everywhere at once. The historically right
behaviour is waves, not a permanent attrition floor. Likely fix: reduce to
−0.25 (vanilla's `spa_mild_smallpox_outbreak` value), or exclude malaria by
applying resistance reduction only to the crowd diseases if that is possible.

If it is smallpox, then M&T's Great Pestilence **does** bite on contact, much
harder than the 1337→1676 series suggested, and the failure is entirely one of
recovery rather than mortality. That would shift weight back toward Half A.

Either answer is worth more than the number itself.
