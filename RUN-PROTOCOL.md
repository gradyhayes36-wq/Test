# Run protocol: fresh 1488, natural spawn

Everything below assumes the current branch. No `nwp.6`, and **no `nwp.7`** —
colonial disruption now applies hemisphere-wide once the Situation starts, so
the collapse runs without forcing a wave. That is the production behaviour and
it is what needs validating.

## Checkpoints

Fire `event nwp.9` at each. Every line carries its own year, so one grep at the
end collects everything.

| Year | What it answers | Compare against |
|---|---|---|
| **~1500** | did the hemisphere fix work? | `labour_regime` should be **thousands**, not 0 |
| **1550** | is the collapse running? | Mesoamerica well below 12.9M |
| **1565** | first control comparison | control **14,343.0k** |
| **1600** | the design spec's target, and the stamp cutoff | **1–3M** Mesoamerica |
| **1650** | the floor | New World ~5–6M |
| **1700** | recovery | New World **12–14M** |

## Abort early if

- **`labour_regime = 0` a few years after the Situation starts.** The
  hemisphere-wide fix failed. Stop — no point running further.
- **Mesoamerica tracking the control trend at 1550.** Nothing is applying.
  Check `agri_collapse` and `labour_regime` counts before continuing.
- **Mesoamerica below ~2M at 1550.** Overshooting badly. The collapse is 50
  years ahead of schedule and the 1600 cutoff will not save it.

## What each outcome means at 1600

| Mesoamerica at 1600 | Verdict |
|---|---|
| 1–3M | on target |
| 3–5M | slightly weak — move the stamp cutoff later than 1600 |
| under 1M | too deep — move the cutoff earlier, or ease `nwp_colonial_labour_regime` |
| over 5M | too weak — deepen the modifiers |

The cutoff date is the main tuning dial now, and it is one number.

## What to watch on the way

`::NWPTRACK::` gives a monthly Mesoamerica reading with `cocoliztli_locs` and
`agri_collapse`. Spikes in `cocoliztli_locs` mark natural waves — with
resistance decay now at 0.006/month (~14 years to clear), waves roughly 20
years apart should bite rather than fizzle as they did in the last run.

`::NWPDIS::` reports smallpox, measles and influenza counts alongside
cocoliztli, so a wave that is actually M&T's own disease is distinguishable
from ours.

`::NWPROLL::` prints every time the spawn roll succeeds, carrying the number of
locations that pass the eligibility limit ignoring climate. It exists because
the 1488 run produced zero `::NWPSPAWN::` lines in 77 years and there was no
way to tell "the roll never came up" from "it came up repeatedly and found
nothing to spawn into". `::NWPROLL::` lines with `eligible_ignoring_climate=0`
mean the limit is too strict; `::NWPROLL::` with a nonzero count and no
matching `::NWPSPAWN::` means the climate clause is the blocker; no
`::NWPROLL::` at all means the roll genuinely is not firing.

## If no natural wave has spawned by ~1530

`monthly_spawn_chance = 0.004` implies roughly one per 21 years from contact,
so a first wave around 1510–1520 is expected. If nothing has fired by 1530,
`event nwp.7` once to test the disease path separately — but note it in the
results, because a forced wave at full presence is not the same as a natural
one at 0.75.
