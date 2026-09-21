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
| 1337 start | **10.9M measured** — leave alone; a mid-range count, not the ~20M the design spec assumed |
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

7. **The free-land brackets are confirmed, and the spring is weaker than the
   spec assumed.** From vanilla `location.txt`:

   ```
   available_free_land  = { local_population_growth = 0.0025 }
   # when its <10% capacity & <10k
   abundant_free_land   = { local_population_growth = 0.015  }
   ```

   The design spec's §3 figures were right. But the vanilla comment spells out
   that `abundant_free_land` requires **both** conditions — under 10% of
   capacity **and** under 10,000 pops. A central Mexican location falling from
   200K to 20K is still over 10K, so it does *not* get the +1.5%. It gets
   `available_free_land`'s +0.25%.

   That cuts the rebound problem down substantially, and it introduces a trap
   worth naming: **over-killing is what arms the spring.** Drive a location
   below 10K and it drops into the +1.5% bracket and rockets back. One
   apocalyptic wave is therefore worse than several moderate ones not only
   historically but mechanically — which is an argument for the recurring
   design in finding 3, arrived at from a different direction.

   M&T does not override either bracket. It *did* rewrite the overpopulation
   soft cap (vanilla's `cap_maximum_population_growth_at_zero` commented out,
   replaced with `local_population_growth = -0.005`).

10. **`local_disease_resistance` exists as a modifier type, and vanilla already
    uses it for this exact scenario.** `spa_severe_smallpox_outbreak` carries
    `local_disease_resistance = -0.75`; `spa_mild_smallpox_outbreak` carries
    -0.25. M&T uses the positive direction on town buildings (+0.1 to +0.33).

    This is a cleaner statement of "virgin soil" than anything in the design
    spec: applying it to American locations makes the *existing* three diseases
    far deadlier there without editing their global definitions and breaking the
    Old World balance M&T tuned them for. Added as `nwp_virgin_soil`.

8. **Starvation is a bigger hammer than anything we could write.** Vanilla's
   `province_starving` (`docs/vanilla-reference/province.txt`) carries
   `local_population_growth = -0.025`, plus `local_life_expectancy = -10` and
   `local_upper_class_capacity_modifier = -0.5`. M&T does not override it.
   That is roughly 17x Abundant Free Land's +1.5%, in the opposite direction.

   This reorders the lever menu. We do not have to out-tune the growth spring
   with hand-written penalties; we have to stop a collapsed location feeding
   itself and let vanilla's own starvation code do the work. Food moves from
   third-choice lever to primary mechanism.

9. **The farming-village system makes that historically exact.** M&T's
   `mnt_food.1` builds one `farming_village` per 2,500 peasants **at startup
   only**, each granting `local_food_capacity = 20`. The regeneration path
   (`mnt_food.2`) is gated on `is_ai = yes`, `monthly_income_total > 15` and
   ≥800 unemployed pops for the building — gates a depopulated colonial
   backwater plausibly fails. `mnt_food.3` actively *destroys* surplus
   villages.

   So the spec's §4.3 worry — that the scripted system would just rebuild them
   — is answered: probably not, for exactly the locations we care about. And
   destroying farming villages in cocoliztli-struck locations *is* the
   historical mechanism, not a proxy for it: chinampas, terraces and irrigation
   going unmaintained because the maintainers were dead. It also
   self-reinforces — fewer villages, less food capacity, starvation, negative
   growth, fewer peasants.

   Caveat: that regeneration path is AI-only, so a human-played Spain will
   behave differently from an observer run.

## Installing

See **[INSTALL.md](INSTALL.md)**. It is a separate mod loading after M&T, not
an overlay onto M&T's folder.

## What's here

| File | Job |
|---|---|
| `in_game/common/diseases/nwp_cocoliztli.txt` | Half B. Recurring, barely-immunising, 30–50% mortality, New World only, gated on its own date window so it outlives the Situation. |
| `main_menu/common/static_modifiers/nwp_collapse.txt` | Half A. Two location modifiers for the persistent carrying-capacity collapse. |
| `in_game/common/script_values/nwp_tuning.txt` | Every tunable number, in one file. Tune here, not in the disease. |
| `in_game/events/nwp_collapse_events.txt` | `nwp.1`, fired from the disease's own `on_spread_to_country`. Stamps the collapse modifiers and destroys farming villages. |
| `in_game/common/situations/nwp_demographic_collapse.txt` | A new Situation whose `on_monthly` applies `nwp_virgin_soil`. Its end date is when recovery begins. |
| `main_menu/localization/english/nwp_l_english.yml` | Names for the disease, situation and modifiers. |

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
- **Destroying farming villages is designed but not implemented.** This is
  now the primary suppression mechanism (finding 9) and needs the same event
  hook the collapse modifiers need.
- **`relationships` schema in `.metadata/metadata.json` is unverified.** M&T's
  own metadata has an empty array, so it demonstrates nothing. Check a
  known-good submod before trusting it.
- **Script-value references inside disease fields are unverified.** M&T's
  disease files use literals throughout, and so do vanilla's. If
  `value = nwp_cocoliztli_spawn_strength` doesn't resolve, inline the numbers.
- **The 100-year modifier duration is a guess**, as is whether `mode = replace`
  refreshes the clock on a later wave. If it doesn't, a second wave won't
  extend the first wave's collapse.

## Status: first live test passed, one bug found

`docs/measurements/first-live-test-1494.md`. The Situation starts on contact,
`dominant_culture` works, the collapse modifiers cut Mesoamerica **−25.3% in
six years** (−4.74%/yr, three times the target) and capacity **−37.6%**, with
essentially all the loss being deaths rather than migration.

Cocoliztli went extinct without spreading — its r0, spread threshold and calc
interval had all been set below M&T's smallpox. Fixed by adopting smallpox's
proven spread profile and keeping only the lethality and resistance changes.

## Before playtesting

See **[PLAYTEST.md](PLAYTEST.md)**. Nothing but `nwp.9` has ever executed, so
the tests there escalate from free (read `error.log`) to a 30–60 minute
observer A/B against control values already measured in the same saves.

## Validating

See **[VALIDATION.md](VALIDATION.md)**. Short version: M&T ships a yearly census
logger (`SYS-CENSUS.txt`) that writes `::POP::<year>:<region>:<population>` to
`error.log`. Enable it with `event LOGGER_CHARTS.01` from the console, then grep
for `mesoamerica`. No save inspection or manual counting needed.

Success is 1–3M at 1600 **and still under ~4M at 1700**. The second number is
the real test.

Watch for a location with mortality applied but max pop untouched — you'll kill
19 million and watch them regrow, because the soft cap is still up there pulling
them back.

11. **Vanilla's `great_pestilence` disease still exists, fully written, and
    M&T deletes it with a 3-byte file.** `in_game/common/diseases/great_pestilence.txt`
    in the M&T repo contains nothing but a BOM — a same-path file-level
    override. The vanilla definition it suppresses
    (`docs/vanilla-reference/diseases/great_pestilence.txt`) is the apocalyptic
    disease the design spec was trying to reconstruct:

    ```
    mortality_rate = { 0.75 0.9 }
    percentage_to_meet_their_fate_on_calc = 0.10
    location_spread_threshold = 0.60   # 0.85 in hard terrain
    location_modifier = { local_population_growth = -0.1 ... }
    r0 multiply = 0.0 outside the Americas
    spawn requires is_culture_native_american and disease_resistance < 0.5
    ```

    Two things follow. First, restoring it is a three-line submod for anyone
    who wants the blunt fix — worth knowing, though it's the ahistorical
    single-disease model M&T deliberately left behind. Second, and more useful
    here, it's a calibration table.

12. **Lethality is not mortality_rate — it's mortality × time at saturation.**
    This is the finding that most changed our numbers. Vanilla
    `great_pestilence` pairs a 0.75–0.9 mortality with a fate rate of only
    **0.10**, so presence builds and *stays* built. M&T's smallpox burns
    through at 0.25 and collapses. A low `percentage_to_meet_their_fate_on_calc`
    is what makes a disease devastating.

    Our first draft had this exactly backwards: fate 0.4 with mortality
    0.3–0.5, which reads as aggressive and behaves as a disease that burns out
    before it saturates. Recalibrated to fate **0.12**, mortality
    **{ 0.4 0.6 }**, spread threshold **0.40** — great_pestilence's shape at
    roughly half its severity, which is where a recurring disease belongs.

13. **Our location penalties were timid by a factor of ten.** Vanilla
    `great_pestilence`'s `location_modifier` carries
    `local_population_growth = -0.1`. Ours carried −0.008. The engine plainly
    tolerates penalties far past anything in the static-modifier files. Raised
    to −0.05, plus the attrition and pop-demotion terms vanilla and M&T's
    bubonic plague both use.

14. **`disease_resistance(scope:disease)` is readable in script**, and vanilla
    uses it three ways: gating spawn (`< 0.5`), suppressing `r0` above 0.5, and
    escalating stagnation chance above 0.95 and 0.98. Bubonic plague's
    post-Black-Death recurrence at **0.005/month** — "should happen around once
    per 20 years" — is direct vanilla precedent for our 0.004 cocoliztli
    cadence.

    We deliberately do *not* gate cocoliztli's spawn on low resistance the way
    vanilla does. The 1576 wave killed survivors of 1545; that is the entire
    point of the disease.

## The growth arithmetic

Per-year `local_population_growth`, for a depopulated central Mexican location
that is still above 10K pops:

| Source | Value |
|---|---|
| `available_free_land` | **+0.0025** |
| `abundant_free_land` (only if also under 10K pops) | **+0.015** |
| `devastation` at maximum | −0.005 |
| `looted` | −0.005 |
| `expensive_food_in_location` | −0.001 |
| `nwp_colonial_labour_regime` (ours) | −0.008 |
| **`province_starving`** | **−0.025** |

Two things fall out of this table.

**Prosperity and devastation cannot do the job.** The design spec ranked them
lever 1, most-trusted. But vanilla `devastation` is written as positive values
multiplied by a devastation scalar of 0 to −1, so its floor is −0.005; and
`prosperity` ranges 0 to 1, so losing all of it costs +0.002, not a negative.
Devastation plus our own heaviest hand-written modifier is −0.013 against
+0.015 — it loses to a bracket we didn't even want to be in.

**Starvation is the only lever that decisively wins**, at −0.025 on its own.
Which is why the food mechanism in findings 8 and 9 is the design, and the
rest is support.

**Measured 1676 (see `docs/measurements/`):** Mesoamerica sits at 0.610 of
capacity with ~100k capacity per location across 325 locations. At any target
in the 1–3M band, pop/location falls under 10k *and* under 10% of capacity —
both conditions for `abundant_free_land`. So every historically correct
outcome lands in the +1.5%/yr bracket, and a 2M Mesoamerica doubles in 46
years. Capacity reduction is what fails the fill condition and keeps the
collapsed population out of it. That makes
`local_population_capacity_modifier` the load-bearing field in
`nwp_collapse.txt`, with starvation the thing that gets you down there.

## Still unknown

**Baseline pop growth.** `location_base_values` in vanilla `location.txt`
contains no `local_population_growth` at all, so the ~0.2% baseline the design
spec assumes comes from somewhere else — defines or script values. The table
above is therefore a table of *modifiers*, not of net growth. Finding the
baseline would pin the arithmetic down completely.

**How resistance is acquired.** Still not found. `local_disease_resistance`
scales it, `monthly_resistance_reduction` decays it, and
`disease_resistance(scope:disease)` reads it — but the gain on surviving a
mortality roll is none of those. Likely a define.

**Whether vanilla `great_pestilence` can be un-deleted from a submod.** M&T
suppresses it with an empty same-path file. Whether a submod loading after
M&T can restore the entry by shipping the original content at that path
depends on override semantics we haven't tested.

## Schema reference

`docs/vanilla-reference/situations/readme.txt` is vanilla's own field-by-field
documentation for situations, including the root scope of every block. The
`diseases/` folder alongside it holds the five vanilla disease definitions,
including the `great_pestilence` one M&T deletes.

## Sources

- [Diseases](https://eu5.paradoxwikis.com/Diseases) / [Disease modding](https://eu5.paradoxwikis.com/Disease_modding) / [Population](https://eu5.paradoxwikis.com/Population) — EU5 Wiki
- [MnT-EU5 changelog](https://github.com/MEIOU-and-Taxes/MnT-EU5/blob/develop/Documentation/Change%20log.md)
- [McCaa, *The Population of Mexico from Origins to Revolution*](https://users.pop.umn.edu/~rmccaa/mxpoprev/cambridg3.htm)
- [Cocoliztli epidemics](https://en.wikipedia.org/wiki/Cocoliztli_epidemics)
