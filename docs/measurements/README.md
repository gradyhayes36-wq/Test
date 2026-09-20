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

## It is not only Mesoamerica

Rough ratios against historical estimates for the late seventeenth century:

| Region | Game | Historical | Ratio |
|---|---|---|---|
| Canada | 3,701k | ~0.15M | ~20x |
| East Coast | 5,488k | ~0.35M | ~15x |
| Brazil | 4,675k | ~0.3M | ~15x |
| Mesoamerica | 19,802k | ~1.5M | ~13x |
| Colombia | 7,325k | ~1M | ~7x |
| Andes | 10,141k | ~2M | ~5x |
| Caribbean | 1,758k | ~0.5M | ~3.5x |

Mesoamerica is the largest absolute error but **not the worst ratio**. Canada,
the East Coast and Brazil are proportionally worse. That is worth keeping in
view: the submod was scoped to central Mexico, and M&T's Great Pestilence
situation tracks Caribbean / Mesoamerica / Andes specifically, but the northern
and Brazilian numbers are at least as distorted and are probably a different
problem — those regions never held millions, so their error is about starting
population and colonial growth rather than epidemic mortality.

`nwp_virgin_soil` applies to every American location with native pops, so it
does reach them. `cocoliztli` largely does not, by design.

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
