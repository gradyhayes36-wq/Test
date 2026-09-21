# Production-path test, 1488 → 1503

Fresh 1488 save with the spread fix, `nwp.7` to seed cocoliztli, **no debug
modifier stamp**. Fifteen observer years. This is the real chain running on its
own for the first time.

## The spread fix worked

`nwp.1 fired` appears **37 times in 1488** against zero country-spreads in the
previous build. Cocoliztli established, spread across Mesoamerica's many small
countries, and did real damage.

| Region | 1488 | 1503 | change |
|---|---|---|---|
| **Mesoamerica** | 11,768.4k | **7,482.0k** | **−36.4%** |
| Caribbean | 149.1k | 124.1k | −16.8% |
| Aridoamerica | 712.1k | 635.9k | −10.7% |
| Central America | 1,805.1k | 1,762.1k | −2.4% |
| Colombia | 3,011.1k | 2,979.6k | −1.0% |
| Andes | 8,248.8k | 8,379.1k | +1.6% |
| East Coast | 1,760.2k | 1,788.9k | +1.6% |

−36.4% over fifteen years is **−2.97%/yr**, and against the control's
+0.257%/yr trend Mesoamerica ends **39% below where it would have been**.

Historically, the 1520 smallpox wave is estimated to have killed 30–40% of
central Mexico. One wave producing −36% is in the right register.

## The kill came from the disease, not the modifiers

`agri_collapse=25`, `labour_regime=1` — only 25 of 325 Mesoamerican locations
were stamped. Implied capacity per location went 79.3k → **79.0k**, i.e.
unchanged.

So this entire decline is **cocoliztli's own mortality**, with the suppression
half barely engaged. That is the opposite of the debug run, where the modifiers
did everything and the disease did nothing. Both halves are now independently
demonstrated to work.

## The flaw: coverage

25 of 325. `nwp.1` fires on `on_spread_to_country` and stamps only the
locations above 0.1 presence **at that instant** — but a wave moves through
locations over months, so a snapshot at the moment it reaches a country catches
almost none of them.

Fixed by moving the stamping into the Situation's `on_monthly`, which already
sweeps every location every month. Each location now gets caught while the
disease is actually in it. `nwp.1` stays as a secondary path.

This matters because the two halves have very different time signatures. The
disease kills hard and briefly; the modifiers suppress gently and for a
century. Low coverage means the population crashes and then has nothing holding
it down.

## Still burning out

`cocoliztli_locs=0` by 1503. The wave swept through in 1488 and was gone. There
were two `nwp.1` firings in 1499, which suggests a natural respawn — the
`monthly_spawn_chance = 0.004` gives roughly one wave per 21 years — but it did
not take hold either.

Whether that matters depends on what happens next. With coverage fixed, each
wave leaves a century of suppression behind it, so the disease does not need to
persist; it needs to recur. That is the next thing to measure.

## Fill ratio is drifting toward the trap

| | 1488 | 1503 |
|---|---|---|
| pop/location | 36.2k | **23.0k** |
| fill | 0.457 | **0.291** |

Capacity did not fall (only 25 locations stamped), so the population fell
*relative to an unchanged ceiling*. `abundant_free_land` needs under 10k
pop/location **and** under 0.10 fill. Still outside on both, but both are moving
toward it — which is precisely why the capacity modifier needs the coverage fix
to keep pace with the mortality.

## Minor

`error_log` with `[GetCurrentYear]` inside a Situation's `on_start` throws
`Tried to localize with localization disabled` — that hook runs during the
gamestate tick. Changed to a plain string; the line still prints.
