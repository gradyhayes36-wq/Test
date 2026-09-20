# Installing

**Do not copy these files into M&T's folder.** This is a separate mod that
loads after M&T. Overwriting M&T's files would break their mod, make your
changes impossible to tell apart from theirs, and get wiped by their next
update.

## 1. Find the mod folder

It sits next to the `logs` folder you pulled `error.log` from:

```
Documents\Paradox Interactive\Europa Universalis V\
    logs\          <- error.log is here
    mod\           <- this is where the mod goes
    save games\
```

If `mod\` doesn't exist, create it.

## 2. Get the files

Clone the branch, or download it as a ZIP from GitHub and extract it:

```
git clone -b claude/eu5-disease-submod-u56pa6 <repo-url> new_world_pestilence
```

Put the folder in `mod\`, so you end up with:

```
mod\new_world_pestilence\
    .metadata\metadata.json
    in_game\
    main_menu\
```

**`.metadata` is a hidden folder.** Windows Explorer may not show it — turn on
"Hidden items" in the View tab to confirm it survived the copy. Without it the
launcher will not see the mod at all. This is the single most common way this
step fails.

`README.md`, `VALIDATION.md`, `INSTALL.md` and `docs/` are documentation. The
game ignores them, so copying them is harmless; leaving them out is tidier.

## If the launcher doesn't list the mod at all

Two causes account for almost all of it.

**The metadata file must have a UTF-8 BOM.** M&T's `metadata.json` starts with
`EF BB BF`; their CI enforces BOM on every file in the repo. A metadata file
the parser cannot read means the mod is silently absent — no error, it simply
is not there. Check:

```powershell
$p = "$env:USERPROFILE\Documents\Paradox Interactive\Europa Universalis V\mod\New-World-Pestilence\.metadata\metadata.json"
Test-Path $p
Format-Hex $p | Select-Object -First 1
```

`Test-Path` must print `True`, and the first three bytes must be `EF BB BF`.

**The folder must not be nested.** A GitHub ZIP extracts to a wrapper folder,
so it is easy to end up with
`mod\New-World-Pestilence\Test-claude-eu5-.../.metadata\`, one level too
deep. `.metadata` has to sit directly inside the folder you put in `mod\`:

```powershell
Get-ChildItem "$env:USERPROFILE\Documents\Paradox Interactive\Europa Universalis V\mod\New-World-Pestilence" -Force
```

`-Force` is needed to show `.metadata`, which is hidden. You should see
`.metadata`, `in_game` and `main_menu` at that level. If instead you see a
single subfolder, move everything up one level.

Compare against `MnT-EU5` in the same directory — it works, so it is the
reference for what the launcher expects.

## 3. Set the load order

In the launcher, make a playset containing **both** M&T and this, with this one
**after** M&T. Order matters: `REPLACE:`-style overrides and same-path file
replacement resolve in load order, so a submod loading before its parent does
nothing.

`metadata.json` ships with an **empty** `relationships` array, matching M&T's
own file. An earlier version declared a dependency on `meiou_and_taxes` using
an invented schema; since M&T's array is empty there was nothing to copy from,
and a malformed entry is a plausible way to make the launcher drop the mod. So
the load order is yours to set manually in the playset — drag this below M&T.

## 4. Expect it to fail the first time

Nothing in this submod has ever been run. Load the game and check `error.log`
immediately — parse errors appear at load, before you touch a save.

Known unverified points, each of which would show up here:

- Script-value references inside disease fields (`value = nwp_cocoliztli_spawn_strength`).
  Vanilla and M&T both use literals throughout. If these don't resolve, inline
  the numbers from `nwp_tuning.txt`.
- `monthly_spawn_chance_unique` in the Situation — used by both vanilla
  situations, assumed to be a vanilla script value.
- `mode = replace` on a 100-year location modifier, and whether a later wave
  refreshes the clock.

## 5. Testing on an existing save

Back the save up first.

The census event `nwp.9` should work on an existing save — it is
`orphan = yes` and reads current state, nothing more:

```
event nwp.9
```
```powershell
Select-String "::NWPPOP::" error.log
```

The disease and the Situation are a different matter. A save already has its
disease and situation state baked in, so `cocoliztli` and
`nwp_demographic_collapse` may not initialise mid-save, or may behave oddly.
Judging whether the submod actually works needs a fresh run — but reading the
population number does not, which is the point of doing the census first.
