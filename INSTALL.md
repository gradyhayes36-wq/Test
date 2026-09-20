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

## 3. Set the load order

In the launcher, make a playset containing **both** M&T and this, with this one
**after** M&T. Order matters: `REPLACE:`-style overrides and same-path file
replacement resolve in load order, so a submod loading before its parent does
nothing.

`.metadata/metadata.json` declares a dependency on `meiou_and_taxes`, which
should enforce that automatically. **That schema is unverified** — M&T's own
`relationships` array is empty, so it demonstrates nothing. If the launcher
rejects the mod or complains about the field, delete the whole `relationships`
block and order the two by hand instead.

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
