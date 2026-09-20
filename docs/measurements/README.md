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

## The capacity hypothesis was wrong

`nwp.9` now reports fill ratio. **Mesoamerica is at 0.610** — six-tenths of
capacity, not pinned at it. Several Old World regions are fuller: Egypt 0.805,
Arabia 0.801, Deccan 0.730, Western India 0.698. So the flat line is not a
population pressed against a ceiling, and the "mortality is the wrong lever"
conclusion does not follow. Withdrawn.

What the fill ratio gives instead is the capacity per location, and that turns
a guess into arithmetic:

| Region | pop (k) | fill | locations | pop/loc | implied cap/loc |
|---|---|---|---|---|---|
| Mesoamerica | 19,802 | 0.610 | 325 | 60.9k | **99.9k** |
| Colombia | 7,325 | 0.353 | 207 | 35.4k | 100.4k |
| Caribbean | 1,758 | 0.168 | 111 | 15.8k | 94.0k |
| Britain | 3,883 | 0.167 | 234 | 16.6k | 99.3k |
| Iberia | 6,051 | 0.142 | 424 | 14.3k | 100.6k |
| Andes | 10,141 | 0.456 | 375 | 27.0k | 59.4k |

Mesoamerica's capacity per location is **utterly ordinary** — the same ~100k as
Britain, Iberia, Colombia and the Caribbean. Its capacity is not the anomaly.
Its *population* is: 60.9k per location against Britain's 16.6k.

## The trap, now measured

The design spec's §3 fear was right, and here is the size of it. Mesoamerica
has 325 locations and ~100k capacity each. At every target population in the
1–3M band:

| Target | pop/loc | under 10k? | fill | under 10%? | `abundant_free_land`? |
|---|---|---|---|---|---|
| 1.0M | 3.1k | yes | 3.1% | yes | **YES, +1.5%/yr** |
| 1.5M | 4.6k | yes | 4.6% | yes | **YES, +1.5%/yr** |
| 2.0M | 6.2k | yes | 6.2% | yes | **YES, +1.5%/yr** |
| 3.0M | 9.2k | yes | 9.2% | yes | **YES, +1.5%/yr** |
| 4.0M | 12.3k | no | 12.3% | no | no |

**Every historically correct outcome lands in the fast-rebound bracket.** At
+1.5%/yr a 2M Mesoamerica doubles in 46 years. Collapse it by 1600 and it is
back over 4M by 1650 — the design spec's 1700 test fails, and it fails
*because* the mortality worked.

Note where the cliff sits: 4.0M is outside the bracket, 3.0M is inside it at
9.2%. The target band straddles the threshold almost exactly.

## Why capacity reduction is the answer after all

Not because pops are pinned — they aren't — but because **cutting capacity is
what keeps the collapsed population out of the +1.5% bracket.** Failing either
condition disqualifies it, and the fill condition is the one we can move.

| Floor | cap/loc must be under | cut from 99.9k |
|---|---|---|
| 1.0M | 30.8k | 69% |
| 1.5M | 46.2k | 54% |
| 2.0M | 61.5k | **38%** |
| 3.0M | 92.3k | 8% |

`nwp_agricultural_collapse` carries `local_population_capacity_modifier =
-0.25` and `nwp_colonial_labour_regime` `-0.40`. If those stack additively
that is −65%, supporting a floor near 1.0M; multiplicatively, −55%, supporting
about 1.5M. Either way the sizing is roughly right — which was luck, and is now
justified by measurement.

**Whether they stack additively or multiplicatively is untested**, and it
changes the supportable floor by half a million.

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

**Tested and disproved** — see above, the fill ratio is 0.610. Mortality can
in principle win. But something is holding Mesoamerica at six-tenths of
capacity rather than letting it grow toward the ceiling, and we do not know
what. Food, M&T's soft-cap taper and prosperity are all candidates. Whatever it
is, it is the equilibrium the submod has to move.

**The next measurement is a fresh 1337 start**, `event nwp.9` on day one. That
gives the true baseline instead of the design spec's remembered ~20M, and says
whether Mesoamerica has been flat for 339 years, declined slightly, or grew and
fell back. It takes five minutes and it is the most informative thing left to
measure.
