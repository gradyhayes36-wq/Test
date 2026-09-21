# Coverage fix verified, 1496 wave

Protocol corrected: run to 1496 with the Situation already ACTIVE, **then**
`event nwp.7`. The previous attempt seeded at 1488, five years before the
Situation could exist, so the monthly sweep never had a live disease to find.

## The coverage fix works

| | old build | this run |
|---|---|---|
| `agri_collapse` | 25 | **250 during the wave, 335 at census** |
| `cocoliztli_locs` peak | 1 | **243 of 325 Mesoamerican locations** |

## The wave, month by month

`::NWPTRACK::` from the Situation's monthly sweep:

| Month | Mesoamerica | infected | stamped | cumulative |
|---|---|---|---|---|
| 1496 pre | 12,019.6k | 0 | 0 | — |
| 1496 +1 | 12,018.5k | 18 | 2 | −0.0% |
| 1496 +2 | 11,762.2k | 196 | 25 | −2.1% |
| 1496 +3 | 10,564.7k | 243 | 182 | −12.1% |
| 1496 +4 | 9,807.7k | 239 | 238 | −18.4% |
| 1496 +5 | 9,165.2k | 222 | 243 | −23.7% |
| 1497 +6 | 8,771.2k | 214 | 244 | −27.0% |
| 1497 +8 | 8,322.8k | 90 | 248 | −30.8% |
| 1497 +10 | 8,254.4k | 68 | 250 | **−31.3%** |

**One wave: −31.3% in about ten months.** The 1520 smallpox epidemic in central
Mexico is estimated at −30% to −40%, so the magnitude is historically defensible
— but it arrives faster than any real epidemic, and it is a single forced wave
at full presence rather than a natural spawn at 0.75.

Note the shape: infection peaks at 243 locations in month 3, then decays to 68
by month 10. It behaves like a wave, which is right.

## Capacity is now engaging

Implied capacity per location: 79.3k → **73.0k** (−7.9%), and falling as the
335 stamps take hold. In the old build, with 25 stamps, capacity never moved.

## Projection and the open risk

From 8,254.4k at 1497, applying the **−1.41%/yr** sustained suppression measured
in the earlier debug run, and assuming **no further waves**:

| | Projected | Target |
|---|---|---|
| 1550 | 3.89M | — |
| **1600** | **1.91M** | **1–3M** ✓ |
| 1700 | 0.46M | too low |

1600 lands on target from a single wave plus suppression. **But
`monthly_spawn_chance = 0.004` should produce a wave roughly every 21 years.**
Four or five more waves at −31% each would overshoot enormously.

Two things push back on that and neither is measured yet:

- **Resistance.** `monthly_resistance_reduction = 0.004` clears resistance over
  about 21 years, so a wave arriving at that interval meets a partly-immune
  population and should kill far less than the first.
- **Coverage saturation.** The sweep is guarded by
  `NOT = { has_location_modifier = nwp_agricultural_collapse }` and stamps for
  100 years, so later waves add few new stamps.

Whether those two are enough to keep repeated waves from overshooting is the
next measurement, not a thing to tune blind.

## Regional spread

At 1497: Mesoamerica −31%, Aridoamerica −31% (712.1k → 489.0k). Central America
+0.4%, Andes flat, Caribbean +5%. The wave stayed in and next to its origin
region rather than sweeping the hemisphere.
