# Playtest protocol

Nothing in this submod except `nwp.9` has ever executed. Do not spend a long
campaign on it. The tests below escalate in cost, and each one is only worth
running if the previous passed.

## The next run (current step)

Everything below was the bootstrapping. Both halves are now demonstrated; what
is untested is **the two of them together at proper coverage**, and **whether
cocoliztli ever spawns on its own**.

One run answers both. Fresh 1488 save, `event nwp.7`, observer to 1565, firing
`event nwp.9` at **1503 / 1530 / 1565**.

### Benchmarks already measured

| At 1503 | Mesoamerica |
|---|---|
| control, no submod (extrapolated) | ~12,230k |
| disease only, 25/325 stamped | **7,482.0k** |
| this run | ? |

| At 1565 | Mesoamerica |
|---|---|
| control, no submod (measured, run A) | **14,343.0k** |
| historical (Cook & Borah, 1570) | ~2,500k |
| this run | ? |

### Reading it

**Coverage** — `agri_collapse` in the status line. It was 25. Anything far
above that means the monthly sweep fixed it; still ~25 means it did not.

**Trajectory at 1565** — this is the verdict:

| Mesoamerica at 1565 | Verdict |
|---|---|
| above 8M | not enough; waves are too rare or too weak |
| **3–6M** | **on track — fine tuning from here** |
| 1.5–3M | matches Cook & Borah almost exactly |
| under 1M | overshooting; weaken the collapse modifiers first |

**Natural respawn** — watch the `::NWPLOG::` lines for `nwp.1 fired` in years
well after 1488. The first wave is forced by `nwp.7`; any firing in, say, the
1510s or 1530s is `monthly_spawn_chance` working on its own. **This is the
thing that decides whether the submod does anything in a real playthrough**,
where nobody fires a debug event. There was a hint of it already — two firings
in 1499 — but nothing conclusive.

If no firings appear after the first wave across 70 years, the spawn gate is
failing and that becomes the next bug, regardless of how good the trajectory
looks.

## Test 0 — did it load? (already done, just needs reading)

You have loaded the mod and fired `nwp.9`, so the files have already been
parsed once. The evidence is sitting in `error.log`:

```powershell
$log = "$env:USERPROFILE\Documents\Paradox Interactive\Europa Universalis V\logs\error.log"
Select-String -Pattern "nwp_|cocoliztli" $log | Where-Object { $_.Line -notmatch "::NWP" }
```

Anything returned here is a parse or reference error in the disease, situation
or modifier files. **Read this before anything else** — it is free and it
catches the three known-unverified constructs.

## Test 1 — do the definitions exist? (2 minutes)

In any save, console:

```
event nwp.9
```

Then read the status line:

```
::NWPSTAT::<year>:virgin_soil=N:agri_collapse=N:labour_regime=N:cocoliztli_locs=N
::NWPSTAT::situation nwp_demographic_collapse ACTIVE | NOT active
```

Each counter isolates one link in the chain:

| Reading | Meaning |
|---|---|
| `cocoliztli_locs = 0` | the disease never spawned |
| `virgin_soil = 0` | the Situation is not sweeping locations |
| `agri_collapse = 0` | `nwp.1` never fired, or its filter is too tight |
| situation `NOT active` | `can_start` never passed, or the Situation failed to load |

## Test 1.5 — the debug harness (5–20 minutes, no campaign at all)

This is the cheapest real test and it should come before any run. Two
console-fireable events bypass all the gating and exercise each half of the
design separately:

```
event nwp.9      # baseline census
event nwp.7      # HALF B: force-spawn cocoliztli in Mesoamerica at full presence
   ... run ~10 years in observer ...
event nwp.9      # did population fall? did cocoliztli_locs grow?
```

```
event nwp.9      # baseline census
event nwp.6      # HALF A: stamp collapse modifiers on every Mesoamerican location
   ... run ~20 years in observer ...
event nwp.9      # did the growth trend bend?
```

**Testing them separately is the point.** A long run with both active cannot
tell you which half worked. If cocoliztli is lethal but the modifiers do
nothing, that is a completely different fix from the reverse.

`nwp.7` skips `monthly_spawn_chance` and every spawn condition, so it isolates
lethality from "does it ever spawn". `nwp.6` skips the Situation, the disease
and the `dominant_culture` test, so it isolates the modifiers' arithmetic from
everything that has to work for them to be applied normally.

Use any save. The 1488 or 1498 ones are convenient because their populations
are already measured.

## Forcing early contact

Console-transferring an American location to an Old World country works as a
compression trick: M&T's `great_pestilence`, this submod's Situation and
cocoliztli's spawn chance **all gate on contact, not on date**, so the whole
chain arms the moment an Old World tag owns American land. A 1350 contact
gives the full epidemic arc in a fraction of the runtime.

Two caveats before relying on it:

- **M&T's `columbian_exchange` situation is age-gated** (`current_age =
  age_5_absolutism`) and will not fire. If part of the post-contact growth
  acceleration comes from the exchange, an early-contact run will not
  reproduce the thing the submod exists to counteract.
- **The control series does not apply.** `docs/measurements/` measures the
  normal timeline. An altered one needs its own control, so the run has to be
  done twice — with and without the submod — to mean anything.

Tech, development and institutions also differ in 1350, so absolute numbers
will not transfer. Treat it as a test of the machinery, not a prediction.

## Test 2 — the cheap A/B (30–60 min, observer)

**Do not start a fresh 1337 run.** Use the saves that already have control
values measured without the submod:

| Save | Control (no submod) | Run forward to | Control at target |
|---|---|---|---|
| run A, 1488 | 11,768.4k | 1565 | **14,343.0k** |
| run B, 1498 | 11,382.5k | 1563 | **12,798.5k** |

Load one with the submod active, switch to observer, run to the target date,
fire `nwp.9`, compare. That is 65–77 simulated years instead of 250, against a
matched control measured in the same save.

**Risk:** adding a mod to an existing save may leave the disease and Situation
uninitialised — a save carries its own disease and situation state. If Test 1's
status line reads all zeros a few years in, that is what happened, and Test 3
is the fallback.

## Test 3 — fresh observer run (hours, not tens of hours)

EU5 has one start date, so this is 1337, observer, max speed, `nwp.9` at
1488 / 1565 / 1600. Only worth it if Test 2 is blocked.

**Compare rates, not absolute populations.** This is the trap. Run A and run B
diverged **42%** in where Mesoamerica ended up (19.8M vs 13.9M), so a fresh
run landing at, say, 16M proves nothing on its own — that is inside ordinary
run-to-run variance. What *did* replicate across the two control runs:

| Quantity | run A | run B | replicates? |
|---|---|---|---|
| pre-contact rate | +0.051%/yr | +0.027%/yr | yes, both ~static |
| epidemic-era rate | +0.257%/yr | +0.181%/yr | yes, both strongly positive |
| epidemic ÷ pre-contact | 5.0x | 6.6x | yes, both large |
| **endpoint population** | **19.8M** | **13.9M** | **no — 42% apart** |

So the success criterion is about the **epidemic-era growth rate**, measured
inside the same run:

1. Fire `nwp.9` at 1488 to get that run's own pre-contact baseline.
2. Fire it again at 1565.
3. Compute the rate between them.

| Epidemic-era rate | Verdict |
|---|---|
| still ~+0.2%/yr | the submod did nothing — go to the debug harness |
| near zero | the chain works, tuning is short of target |
| **−1.2% to −1.7%/yr** | **on target** (1–3M by 1600) |
| below −2.5%/yr | overshooting; ease the starvation coverage |

Using the run's own 1488 figure as the baseline removes the run-variance
problem entirely, because both numbers come from the same playthrough.

## What success looks like

Not "Mesoamerica is 2M". At this stage success is **the epidemic-era growth
rate turning negative at all**. Both control runs put it at +0.18% to +0.26%;
anything meaningfully below zero means the chain works end to end and the job
becomes tuning. The target is ~1.85 points of sustained swing
(see `docs/measurements/`), and the levers bracket it.

## Still unverified going in

- whether `mode = replace` refreshes a 100-year modifier's clock on a later wave
- whether `local_population_capacity_modifier` stacks additively or
  multiplicatively across the two collapse modifiers
- whether `dominant_culture = { is_culture_native_american = yes }` resolves

None of these stop a test run. All three would show in Test 0 or Test 1.
