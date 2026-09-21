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

## It was never flat, and the start was never 20M

Measured on a fresh 1337 start:

| | 1337 | 1676 | change |
|---|---|---|---|
| Mesoamerica | **10,894.6k** | 19,802.5k | **+81.8%** |
| fill ratio | 0.489 | 0.610 | |

Two corrections fall out, one of them to the design spec itself.

**The spec's §2 premise is wrong.** It said "1337 starting population: leave
alone. ~20M for the region is defensible under high counts. The mod isn't wrong
about where Mexico starts." M&T actually starts Mesoamerica at **10.9M**, not
20M — squarely in the Cook & Simpson (10.5M) and upper-Zambardino range, a
mid-range count rather than a Cook & Borah high one. The mod's starting point
is *more conservative* than the spec credited it for, and the "leave it alone"
advice is right for a better reason than the one given.

**The flat line was an artifact of that wrong baseline.** Mesoamerica did not
sit still for three centuries. It **grew 82%**, at 0.176%/yr compound, straight
through the Columbian exchange. The Great Pestilence did not fail to kill
enough — it failed to interrupt a growth trend at all.

## The real shape: the world inverted

| Old World | 1337 → 1676 | | New World | 1337 → 1676 |
|---|---|---|---|---|
| East China | ×0.57 | | Caribbean | **×11.79** |
| Bengal | ×0.68 | | Colombia | ×2.56 |
| Italy | ×0.70 | | Central America | ×1.93 |
| France | ×0.73 | | **Mesoamerica** | **×1.82** |
| North China | ×0.73 | | Andes | ×1.29 |
| Iberia | ×0.74 | | | |
| Hindustan | ×0.77 | | | |
| Japan | ×0.83 | | | |
| Britain | ×0.99 | | | |

The Old World **shrank by a quarter to a half** — the Black Death hit and never
recovered. The New World grew. In the one era when the Americas should have been
the only part of the world collapsing, they are the only part reliably growing.

That is a cleaner statement of the bug than "the disease is too weak", and it
suggests the fix does not need to be enormous — it needs to be *sustained and
in the right direction*.

The Caribbean at **×11.79** (149k → 1.76M) is the worst single distortion in the
unplayed New World. Historically its indigenous population was annihilated
within a century of contact.

## Contact *accelerates* growth — the decisive measurement

A 1488 save, two years pre-contact, splits the series at the right place:

| | 1337 | 1488 | 1676 |
|---|---|---|---|
| Mesoamerica | 10,894.6k | 11,768.4k | 19,802.5k |
| fill | 0.489 | 0.457 | 0.610 |
| implied cap/location | 68.6k | 79.3k | 99.9k |

| Period | Total | Annual |
|---|---|---|
| **Pre-contact** 1337–1488 (151 yr) | +8.0% | **+0.051%/yr** |
| **Post-contact** 1488–1676 (188 yr) | +68.3% | **+0.277%/yr** |

**Post-contact growth is 5.4x the pre-contact rate.** Pre-contact Mesoamerica
is very nearly stable. What the mod does at contact is not "fail to kill
people" — it is *start the population growing*, five times faster than before,
in the exact century when it should have lost 80–90% of its people.

That is the bug, stated as precisely as this project can state it. The
Columbian exchange, in M&T 0.2.9, is a demographic boom for central Mexico.

## Two mechanisms underneath it

**Capacity is rising.** Implied capacity per location goes 68.6k → 79.3k →
99.9k, up **46%** across the run, and it was already climbing before contact.
The collapse has to fight a ceiling that is itself moving up, not a static one.

**Unowned land does not simulate at all.** Several New World regions are
identical *to the decimal* in 1337 and 1488:

| | 1337 | 1488 |
|---|---|---|
| Caribbean | 149.102 | 149.102 |
| Canada | 178.592 | 178.592 |
| Brazil | 2699.704 | 2699.704 |
| Chaco | 580.809 | 580.809 |
| La Plata | 470.611 | 470.611 |
| Alaska | 59.967 | 59.967 |
| West Coast | 224.288 | 224.288 |
| New Zealand | 42.907 | 42.907 |

Frozen — not slow. Regions with real countries (Mesoamerica, Andes, Colombia,
Aridoamerica, Great Plains, East Coast) do move over the same period. So
colonisation *switches on* a population simulation that was previously
inert, against a capacity sized for settled agriculture.

That explains the Caribbean's ×11.79 without any appeal to disease: it sat at
149.102k until Europeans arrived, then began simulating and grew twelvefold.
It is a different bug from Mexico's and this submod does not address it.

## The design target, stated precisely

Earlier this file extrapolated ~14.5M for 1500 using the blended 0.176%/yr
rate. That was wrong — it applied a post-contact-inflated average to a
pre-contact period. The measured 1488 figure is **11.77M**, and the required
rates are correspondingly gentler:

| Target at 1600 | Required sustained rate from 11.77M in 1488 (112 yr) |
|---|---|
| 3M | **−1.21%/yr** |
| 2M | **−1.57%/yr** |
| 1M | **−2.18%/yr** |

And what the submod's current levers produce over that century, against the
observed +0.176%/yr baseline:

| Levers active | Result at 1600 |
|---|---|
| collapse modifiers only (−0.005 + −0.008) | 4.71M — not enough |
| collapse + starvation half the time (−0.025) | **1.32M — in range** |
| collapse + starvation continuously | 0.36M — far too much |

**The target is bracketed by what is already built.** Collapse modifiers alone
undershoot the effect; adding continuous starvation overshoots by 5x. The
tuning problem is therefore *what fraction of locations and time starvation
applies to* — not whether the levers are strong enough. That is a much better
problem to have than the one this project started with.

With the corrected baseline the requirement is −1.21% to −2.18%/yr rather than
−1.56% to −2.64%, so the bracket above is, if anything, slightly wider than it
looked. But note what the levers must now overcome: not a static population but
a **+0.277%/yr post-contact trend** and a capacity rising 0.12%/yr underneath
it. The submod has to reverse an accelerating curve, not arrest a flat one.
