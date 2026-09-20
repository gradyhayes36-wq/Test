# New World Pestilence

A submod for [MEIOU & Taxes](https://github.com/MEIOU-and-Taxes/MnT-EU5) (EU5,
`meiou_and_taxes` 0.2.9, game 1.3.*) that makes post-contact epidemics actually
depopulate the New World, and — the harder half — keeps it depopulated until
roughly 1700.

**Status: scaffold.** The files load-shaped and idiom-matched against M&T's
own source, but nothing here has been run in game. Every number is a guess.

## The target

| Checkpoint | Target for central Mexico |
|---|---|
| 1337 start | ~20M — leave alone, defensible under high counts |
| 1600 | **1–3M** |
| 1700 | **still under ~4M** |

The 1700 number is the real test. The 1600 one is easy to hit and easy to lose
again, because killing pops is what arms EU5's free-land growth brackets.

## What reading M&T's source established

Section 6 of the design spec listed nine things to check before writing a line.
The mod's repo answers most of them. Findings, in order of how much they change
the plan:

1. **Two of the three Great Pestilence diseases are demographically
   irrelevant.** `MnT_measles.txt` has `mortality_rate = { 0.001 0.002 }`;
   `MnT_influenza.txt` has `0.001`. Only smallpox (`{ 0.05 0.3 }`) kills anyone.
   The v0.2.7 rework did not replace one apocalyptic disease with three ordinary
   ones — it replaced it with *one* ordinary one and two rounding errors.

2. **Smallpox resistance effectively never decays.** `monthly_resistance_reduction
   = 0.0007` clears full resistance in ~119 years. Wave one immunises the
   survivors and every wave inside a century bounces off. Compare: influenza and
   measles 0.002, bubonic 0.0002, malaria 0.

3. **The Situation has no disease registry.** `MnT_great_pestilence.txt` only
   force-spawns smallpox in `on_start`; the three diseases gate *their own*
   spawn blocks on the Situation's state. So a fourth disease needs no
   registration — it just needs its own spawn trigger. This deletes the
   integration risk the spec worried about in §4.1.

4. **After the Situation ends, nothing recurs.** Smallpox's spawn block
   restricts to the Old World only while `situation_is_active = no` *and*
   `situation_has_ended = no`. Once the Situation has ended, both `trigger_if`
   guards lapse and America becomes one more ticket in a global 0.08/month
   lottery. There is no post-1600 New World epidemic pressure at all. This is
   the mechanism behind the rebound.

5. **A disease can carry its own suppression modifier.** The `location_modifier`
   block is applied while the disease is present, scaled by presence, by the
   engine. That gets the during-wave half of §4.2 for free, with no on_action.

6. **`malaria` proves the shape is legal**: `mortality_rate = { 0.85 0.95 }`,
   `monthly_resistance_reduction = 0`, spread environmentally rather than by R0.

7. **M&T does not touch the free-land static modifiers.** Zero hits for
   `free_land` anywhere in the repo, so vanilla's brackets presumably stand —
   but they can't be confirmed without the vanilla install. M&T *did* rewrite
   the overpopulation soft cap (`MnT_location.txt`: vanilla's
   `cap_maximum_population_growth_at_zero` commented out, replaced with
   `local_population_growth = -0.005`).

## What's here

| File | Job |
|---|---|
| `in_game/common/diseases/nwp_cocoliztli.txt` | Half B. Recurring, barely-immunising, 30–50% mortality, New World only, gated on its own date window so it outlives the Situation. |
| `main_menu/common/static_modifiers/nwp_collapse.txt` | Half A. Two location modifiers for the persistent carrying-capacity collapse. |
| `in_game/common/script_values/nwp_tuning.txt` | Every tunable number, in one file. Tune here, not in the disease. |

The recurrence is the load-bearing idea: a disease that fires once and kills 90%
is both ahistorical and maximally arms the free-land spring. A disease returning
every ~20 years, each wave killing 30–50% of a partly-recovered population, is
self-suppressing — the population never climbs far enough for long enough to
escape.

## Not done yet

- **`nwp_collapse.txt` is defined but applied by nothing.** It needs an
  `on_action` or event hook to stamp it onto affected locations with a
  century-plus duration. M&T adds its own on_actions in
  `in_game/common/on_action/MnT_pulse.txt` with unprefixed keys; whether a
  submod can append to a list another mod already defines, or needs `INJECT:`,
  is untested.
- **Cocoliztli is invisible to the Situation's plumbing.** M&T's
  `location_has_pestilence_trigger` hardcodes the three diseases, so the
  Great Pestilence map mode, `can_end` check and event targeting won't see
  cocoliztli. Deliberate for v0 — it keeps the Situation's lifecycle
  untouched — but it means no map colour inside the Situation view.
- **No localisation.** Disease name, modifier names, map legend.
- **`relationships` schema in `.metadata/metadata.json` is unverified.** M&T's
  own metadata has an empty array, so it demonstrates nothing. Check a
  known-good submod before trusting it.
- **Script-value references inside disease fields are unverified.** M&T's
  disease files use literals throughout. If `value = nwp_cocoliztli_spawn_strength`
  doesn't resolve, inline the numbers.

## Validating

Before building further: console `situation great_pestilence` on a live save to
re-fire it and read the actual death toll. The Situation may not be firing at
all rather than firing weakly — different bug, different fix. There are reports
of it hanging with 2–3 permanently-infected locations and never ending.

After: observer run, checkpoint central Mexico at 1550 / 1600 / 1700 / 1800.

Watch for a location with mortality applied but max pop untouched — you'll kill
19 million and watch them regrow, because the soft cap is still up there pulling
them back.

## Sources

- [Diseases](https://eu5.paradoxwikis.com/Diseases) / [Disease modding](https://eu5.paradoxwikis.com/Disease_modding) / [Population](https://eu5.paradoxwikis.com/Population) — EU5 Wiki
- [MnT-EU5 changelog](https://github.com/MEIOU-and-Taxes/MnT-EU5/blob/develop/Documentation/Change%20log.md)
- [McCaa, *The Population of Mexico from Origins to Revolution*](https://users.pop.umn.edu/~rmccaa/mxpoprev/cambridg3.htm)
- [Cocoliztli epidemics](https://en.wikipedia.org/wiki/Cocoliztli_epidemics)
