# Playtest protocol

Nothing in this submod except `nwp.9` has ever executed. Do not spend a long
campaign on it. The tests below escalate in cost, and each one is only worth
running if the previous passed.

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

1337 start, observer, max speed, `nwp.9` at 1488 / 1565 / 1600. Compare
against run A's control series. Only worth it if Test 2 is blocked.

## What success looks like

Not "Mesoamerica is 2M". At this stage success is **any measurable divergence
from the control in the right direction**. The control at 1565 is 14,343.0k;
anything meaningfully below that means the chain works end to end and the job
becomes tuning. The target is ~1.85 points of sustained swing
(see `docs/measurements/`), and the levers bracket it.

## Still unverified going in

- whether `mode = replace` refreshes a 100-year modifier's clock on a later wave
- whether `local_population_capacity_modifier` stacks additively or
  multiplicatively across the two collapse modifiers
- whether `dominant_culture = { is_culture_native_american = yes }` resolves

None of these stop a test run. All three would show in Test 0 or Test 1.
