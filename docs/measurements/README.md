# Baseline measurement, 1676

First real data from the game, via `event nwp.9` on a live M&T 0.2.9 save.
Raw output: `census-1676.txt` (region : population in thousands).

## The headline

**Mesoamerica: 19,802k = 19.8M.**

The design spec's target for ~1600 was 1–3M, and under ~4M at 1700. At 1676
the region is between **6.6x and 20x** over target. The spec's claim was
right, and if anything understated.

## The world is undersized, which makes it worse

| | Game, 1676 | Historical, ~1676 |
|---|---|---|
| World | 397.6M | ~600M |
| New World | 58.9M (14.8% of world) | ~10–13M (~2% of world) |
| Mesoamerica | 19.8M | ~1.5M |

The Old World is running at roughly 60–65% of historical — France 10.7M
against ~20M, Britain 3.9M against ~6.5M, all of China 58M against 100M+,
all of India 79.6M against ~150M. So the New World is not merely too big in
absolute terms; as a **share of this game's own world** it is about 7x
over-represented. Scaled to the game's world size, Mesoamerica would sit near
**1.0M**, making the distortion closer to 20x than 7x.

## Which regions the error actually lives in

**Canada, the East Coast and Brazil are not evidence of anything.** This save
has had heavy player colonisation in exactly those areas, so their numbers are
the player's own doing rather than a mod defect. An earlier version of this
file computed ~15–20x ratios for them against historical estimates; that
comparison was invalid and has been withdrawn.

What survives as evidence is the unplayed, native-majority part of the New
World:

| Region | Game | Historical, ~1676 | Ratio |
|---|---|---|---|
| Mesoamerica | 19,802k | ~1.5M | ~13x |
| Andes | 10,141k | ~2M | ~5x |
| Colombia | 7,325k | ~1M | ~7x |
| Caribbean | 1,758k | ~0.5M | ~3.5x |

These are the three regions M&T's Great Pestilence situation tracks by name,
plus Colombia. Mesoamerica is both the largest absolute error and the worst
ratio among them, so the submod's original scope was right after all.

## Settlers and natives

The engine cannot make one outbreak kill natives and spare settlers in the
same location. Mortality is applied at the location, scaled by presence;
`local_disease_resistance` is a location modifier; and `specific_pop_type_effect`
keys on pop **type** (peasants, nobles, burghers) — social class, not origin.
Once a colonist and a native peasant share a location they share its epidemic.

What the engine does give is **composition-aware targeting**, and after this
correction the submod uses it everywhere:

| Where | Test |
|---|---|
| `nwp_virgin_soil` applied | `dominant_culture = { is_culture_native_american = yes }` |
| `nwp_virgin_soil` **removed** | re-evaluated monthly; lifts when the location goes settler-majority |
| `nwp_agricultural_collapse` | same dominant-culture test |
| `cocoliztli` spawn | `any_pop = { culture = { is_culture_native_american = yes } }`, as vanilla `great_pestilence` does |

The first version had a real bug here: the Situation used `any_pop`, so a
single surviving native pop kept the full virgin-soil penalty on a location
that had become 95% colonists — and nothing ever removed it. It now lifts as
well as applies.

The practical effect is close to what was wanted. A colony that is still mostly
native is virgin soil and collapses; once settlers dominate, the penalties come
off and it grows normally. Settlers in a native-majority location still catch
the epidemic, which is not only unavoidable but correct — Europeans in New
Spain did die in the cocoliztli waves, just at far lower rates.

## The flat line

M&T starts Mesoamerica at roughly 20M in 1337. It is 19.8M in 1676.

Over 339 years, across the entire Columbian exchange, the net change is
approximately zero. Either the Great Pestilence killed almost nobody, or it
killed some and they had fully regrown by 1676.

A flat line at the same value for three centuries is the signature of a
population **pinned at its carrying capacity**. If that is what is happening,
it reframes the whole design:

- killing pops does nothing durable, because below the cap growth is positive
  and simply refills the hole
- the only lever that moves the equilibrium is the cap itself

That would make `local_population_capacity_modifier` the load-bearing part of
`nwp_collapse.txt`, and mortality merely the thing that gets you there faster.

**This is a hypothesis, not a finding.** The next measurement tests it:
`nwp.9` now also logs `location_population_percentage` averaged over inhabited
locations. If Mesoamerica's fill ratio is near 1.0, the diagnosis holds and the
design should pivot toward capacity. If it is well below 1.0, the population is
growth-limited rather than capacity-limited and mortality can in principle win.
