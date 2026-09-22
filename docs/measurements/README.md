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

## Contact *accelerates* growth, and the epidemics leave no mark

Four measured points, 325 locations throughout:

| Year | Mesoamerica | fill | implied cap/loc |
|---|---|---|---|
| 1337 | 10,894.6k | 0.489 | 68.6k |
| 1488 | 11,768.4k | 0.457 | 79.3k |
| 1565 | 14,343.0k | 0.515 | 85.6k |
| 1676 | 19,802.5k | 0.610 | 99.9k |

| Period | Years | Total | Annual | vs pre-contact |
|---|---|---|---|---|
| Pre-contact 1337–1488 | 151 | +8.0% | **+0.051%/yr** | 1.0x |
| **Contact & epidemics 1488–1565** | 77 | +21.9% | **+0.257%/yr** | **5.0x** |
| Colonial 1565–1676 | 111 | +38.1% | +0.291%/yr | 5.7x |

The 1565 point is the one that settles it. That middle period **contains the
entire Great Pestilence** — the Situation, the forced smallpox outbreak, the
measles and influenza waves, the whole Columbian exchange. Mexico grows
through it at five times its pre-contact rate.

**There is no notch. There is not even a slowdown.** The epidemic era is when
central Mexico grows fastest relative to its own history, and the rate then
keeps climbing afterwards. Whatever the Great Pestilence is doing, it is not
visible in the population of the region it targets.

That is the bug, stated as precisely as this project can state it. The
Columbian exchange, in M&T 0.2.9, is a demographic boom for central Mexico.

## Replicated in a second playthrough

A separate Portugal run, 1498, with no contact in Central America:

| | Mesoamerica | fill |
|---|---|---|
| run A, 1488 | 11,768.4k | 0.457 |
| **run B (Portugal), 1498** | **11,382.5k** | **0.460** |

Ten years apart, different playthroughs, different everything: **3.3% apart on
population and 0.7% on fill ratio.** Pre-contact Mesoamerica tracks the same
near-flat line regardless of run — +0.051%/yr in run A, +0.027%/yr in run B,
both effectively static.

That matters because the headline finding is a *ratio* — post-contact growth
being 5x pre-contact. If the pre-contact baseline were noisy the ratio would
mean nothing. It is not noisy.

The Old World over the same era is wildly run-dependent by comparison: North
China −24%, Sahel +64%, Bengal +19%, Iberia +11% between the two runs. So
**pre-contact** the New World is not merely stable but insensitive to player
action, which makes it an unusually clean control.

That insensitivity is strictly a pre-contact property. Run B was later carried
to 1685 and the post-contact halves of the two runs diverge sharply — see
below. An earlier version of this file said the New World was insensitive to
player action without that qualifier, which was too broad.

## Run B carried forward: the finding replicates, the endpoint does not

| | run A | run B |
|---|---|---|
| pre-contact | +0.051%/yr | +0.027%/yr |
| **epidemic era** | **+0.257%/yr (5.0x)** | **+0.181%/yr (6.6x)** |
| colonial era | +0.291%/yr (5.7x) | +0.069%/yr (2.5x) |
| endpoint | **19.8M at 1676** | **13.9M at 1685** |

**The core finding replicates.** In both runs, contact multiplies Mesoamerican
growth several-fold — 5.0x in one, 6.6x in the other — and in neither is there
any notch where the epidemics should be. That is now a property of the mod,
not of a single playthrough.

**The endpoint does not replicate.** Run A reaches 19.8M; run B only 13.9M,
42% lower, because run B's colonial-era growth collapses to +0.069%/yr against
run A's +0.291%/yr. So the headline "19.8M in 1676" figure is one outcome
among several, not a constant — and the earlier claim that post-contact
America is insensitive to player action was wrong.

Both still fail the target badly. The design spec wants **under ~4M at 1700**;
the two runs deliver 13.9M and 19.8M, 3.5x and 5x over.

## The tuning target survives the divergence

This is the practically important result. Computing the required swing
separately for each run, from its own measured contact-era baseline:

| | From | Required for 2M at 1600 | Observed | **Swing** |
|---|---|---|---|---|
| run A | 11.8M at 1488 | −1.57%/yr | +0.257%/yr | **1.83 points** |
| run B | 11.4M at 1498 | −1.69%/yr | +0.181%/yr | **1.87 points** |

**1.83 and 1.87 points.** Despite a 42% gap in where the two runs end up, the
intervention they need is the same size to within 2%. The submod can be tuned
against ~1.85 points of sustained swing and be right for both — which is what
makes the target usable at all.

## The frozen-wilderness finding holds across runs

Exact to the decimal in run A 1337, run A 1488 **and** run B 1498:

| | |
|---|---|
| Canada | 178.592 |
| Caribbean | 149.102 |
| Alaska | 59.967 |
| West Coast | 224.288 |

Three saves, two playthroughs, 161 years — identical. Meanwhile regions Portugal
*did* reach moved: Brazil −5.1%, New Zealand −11.8%. So the earlier reading
holds, with one refinement: it is specifically **uncolonised** land that is
inert, and touching it starts the simulation.

(Brazil going *down* 5.1% once Portugal arrives is worth a look on its own —
it is the only New World region measured that declines after contact.)

## The divergence, against Cook & Borah

| | Cook & Borah | Game | Game ÷ C&B |
|---|---|---|---|
| 1519 / 1488 | 25.2M | 11.8M | **0.47x** |
| 1570 / 1565 | 2.5M | 14.3M | **5.74x** |
| 1620 / 1676 | 1.2M | 19.8M | **16.50x** |

The game starts *below* the high counts and ends sixteen times above them.
It is not that M&T picked a large pre-contact population — it picked a
conservative one and then never applied the collapse.

## The Old World did decline, in the right period

| Period | France | East China |
|---|---|---|
| 1337–1488 | **−28.5%** | **−39.9%** |
| 1488–1565 | +2.8% | +6.6% |
| 1565–1676 | −1.0% | −11.2% |

So the engine is perfectly capable of driving a large sustained population
collapse — the Black Death does exactly that to Eurasia in the first period.
The machinery works. It simply is not pointed at the Americas.

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

| Target at 1600 | Required rate from 11.77M in 1488 (112 yr) | Swing from the observed +0.257%/yr |
|---|---|---|
| 3M | **−1.21%/yr** | 1.47 points |
| 2M | **−1.57%/yr** | 1.83 points |
| 1M | **−2.18%/yr** | 2.44 points |

**The submod's job, in one line: turn +0.26%/yr into −1.6%/yr across the
1490–1600 window.** A swing of about 1.8 percentage points, sustained for a
century. That is the number to tune against, and it is modest next to the
levers available — `province_starving` alone is 2.5 points.

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

---

## `population` is raw pops, not thousands

The density gate added after the Caribbean/Mesoamerica comparison was written
as `population > 10` with the intent of ten thousand. It filtered nothing.

The unit is settled by two numbers already measured:

| Source | Reading |
|---|---|
| `nwp.5` seed log, a value read of `population` | `pop=2392` |
| `nwp.9` census, Caribbean region | 149.102k over 111 locations |

149.102k across 111 locations averages 1,343. A seed location at 2,392 is an
ordinary above-average Caribbean location **in raw pops**. If the value read
were in thousands it would mean 2.392M in a single location inside a region
holding 149k in total, which is impossible. So value reads return raw pops,
and there is no reason for a trigger comparison to resolve `population`
through a different getter.

The gate is therefore `population > 10000`, and the location that fizzled
(2,392) now sits correctly below it while the Mesoamerican average (36,210)
sits well above.

This was caught before the Mesoamerica control run, so no measurement is
invalidated by it — but every run made against the `> 10` gate was running an
unfiltered spawn.
