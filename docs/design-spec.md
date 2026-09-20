# M&T-EU5 Submod Spec: New World Demographic Collapse

**Target:** MEIOU and Taxes for EU5, mod id `meiou_and_taxes`, v0.2.9, game version 1.3.*
**Problem:** Central Mexico holds 20M+ pops in 1600. Historically it should be 1–3M.
**Status:** Design spec. Not yet implemented. Written without access to local game files — every section marked ⚠️ needs verification against the actual files before coding.

---

## 1. The problem, precisely

M&T v0.2.7 ("Great Pestilence reworked") removed the abstract `great_pestilence` disease and repointed the Great Pestilence Situation at three real diseases: **smallpox, influenza, measles**. One smallpox outbreak is force-released at Situation start.

The Situation machinery survived. The lethality did not.

- Vanilla's Great Pestilence was explicitly one of the two deadliest diseases in the game — an annihilation event.
- Smallpox/influenza/measles are tuned for an Old World where they circulate endemically. Community figures put EU5 smallpox at roughly 5–30% mortality scaled by density.
- **EU5 grants resistance to pops that survive the mortality roll.** At 90% lethality that hardly matters. At 15%, wave one immunizes the survivors and every later wave bounces off.

Three ordinary diseases do not sum to one apocalyptic one.

## 2. Historical targets

The literature disagrees loudly about the pre-contact number and barely at all about the *direction*.

| Source | 1519 (M) | ~1595–1605 (M) | Decline |
|---|---|---|---|
| Cook & Borah | 25.2 (range 18–30) | ~1.075 (1605) | 78–95% |
| Cook & Simpson | 10.5 | 2.1–3.0 | 71–80% |
| Zambardino | 5–10 | 1.1–1.7 | 64–89% |
| Rosenblat (hard minimalist) | 4.5 | 3.5 | 22% |

Cook & Borah's point series: 25.2M (1519) → 6.3M (1545) → 2.5M (1570) → ~1.2M (1620).

**Design targets:**
- 1337 starting population: **leave alone.** ~20M for the region is defensible under high counts. The mod isn't wrong about where Mexico starts.
- ~1600: **1–3M.** Even minimalists have Mexico well under 4M.
- **Recovery must not begin before ~1700.** Mexico sat at the floor for roughly 150 years. This is the requirement the current mod fails hardest, and the one Section 4 exists to solve.

## 3. Why a deadlier disease alone will not work

EU5's population growth includes two free-land brackets (⚠️ vanilla values — confirm M&T didn't rewrite them, see §6):

- **Available Free Land** — up to +0.25% growth, for locations above 10K pops or above 10% of capacity
- **Abundant Free Land** — up to **+1.5%** growth, only for locations *below* both thresholds
- Baseline growth ≈ 0.2%

Emptying a location drops it through that threshold and hands it a ~7x growth multiplier — **precisely because you emptied it.** At 1.5% compounding, 1M → 20M in about 200 years. Kill 19 million in 1550, have them all back before 1750.

This is not a bug in any one disease. Disease is subtraction; the growth curve is a spring. Every purely-lethal approach pulls against the same spring, and killing pops *arms* it.

So the submod needs two halves:

| Half | Job | Mechanism |
|---|---|---|
| **A. Suppression** | Stop the rebound | Recurring epidemics + carrying-capacity collapse |
| **B. Mortality** | Get the deaths | New World-specific disease with no/low resistance |

Half A is the one that actually fixes the save. Build it first.

## 4. Design

### 4.1 Cocoliztli as a recurring disease (half B, and part of A)

Define a fourth disease and register it with the Great Pestilence Situation alongside the existing three.

Historical waves to hang it on:
- **1520** smallpox — siege of Tenochtitlan
- **1531** measles
- **1545–48** cocoliztli — 5–15M deaths across New Spain, the worst epidemic in Mexican history; 2018 aDNA from Teposcolula-Yucundaa implicates *Salmonella enterica* Paratyphi C
- **1576–80** second cocoliztli

Properties:
- New World locations only
- High mortality (⚠️ tune against §7)
- **Little or no conferred resistance** — historically accurate: the 1576 wave killed people who had survived 1545
- **Recurs every 15–30 years for ~150 years**, with a spawn window that *outlives the Great Pestilence Situation* — persisting through Reformation into Absolutism

That last property is the elegant part. A disease that fires once and kills 90% is both ahistorical and maximally triggers the free-land bonus. A disease that returns every 20 years, each wave killing 30–50% of a partially recovered population, is **self-suppressing** — the population never climbs far enough for long enough to escape. You get the suppression using the game's own disease machinery rather than hacking the growth formula.

Adding a disease rather than resurrecting an "imaginary" one is also more honest to M&T's stated design direction, and being purely additive it survives mod updates better.

### 4.2 Carrying-capacity collapse (half A)

Disease alone can't reach 90–95%, and shouldn't — the historical collapse was disease *compounded by* the colonial labor regime:

- Chinampas, terraces and irrigation stopped being maintained because the maintainers were dead
- *Congregación* uprooted survivors from land they knew how to farm
- Encomienda and repartimiento pulled labor from subsistence agriculture into mines and estates
- Spanish livestock took the rest — Melville's "plague of sheep" on the Valle del Mezquital traces irrigated farmland converting to scrub that couldn't support the old densities

Model this as a long-duration location modifier on New World locations with an Old World top overlord:

- Negative prosperity (scales down to −0.5% growth, which can push a location **net negative**)
- Devastation
- Reduced max population
- Duration measured in a century-plus, not decades

This is the most honest lever available — it literally models "the irrigation is gone and nobody is left to rebuild it" — and it's targeted, with no global blast radius.

### 4.3 Lever menu, in order of trust

1. **Prosperity / devastation modifier** (§4.2) — targeted, can go net negative, no side effects on other regions
2. **Max population** — M&T already rewrote this wholesale in v0.2.9 (climate-driven, lower overall), so hook their system rather than fighting vanilla's. Subtle win: lowering capacity pushes a location *out* of the under-10%-capacity bracket sooner, which is what disqualifies it from Abundant Free Land
3. **Food** — destroying farming villages in depopulated locations suppresses regrowth and models agricultural collapse in one move. ⚠️ M&T's changelog says a scripted system owns farming village generation and nations/estates can't build them — check the regeneration logic doesn't just put them straight back
4. **The free-land static modifiers themselves** — ⚠️ **last resort.** Static modifiers are global; nerfing Abundant Free Land hits every colony everywhere, yours and every AI's. Possibly a feature if you want slower New World colonization generally, but it's a much bigger change than the problem requires

## 5. Submod scaffold

Confirmed from the mod's `.metadata/metadata.json`:

```json
{ "name": "MEIOU and Taxes", "id": "meiou_and_taxes",
  "version": "0.2.9", "game_id": "eu5",
  "supported_game_version": "1.3.*" }
```

The submod must declare a dependency on `meiou_and_taxes` and **load after it**. ⚠️ Confirm EU5's metadata schema for dependency declaration against a known-good example in the local mod folder — `relationships` in the above is an empty array, so it isn't demonstrated here.

## 6. Read these before writing a line ⚠️

This is the part that needs local files. Everything above is mechanism design; none of it is syntax.

**In the vanilla install** (`.../Europa Universalis V/game/`):
1. `in_game/common/diseases/` — the actual disease definition format. Copy a real one. `mortality_rate` is a script value with `scope:disease` available; `character_mortality_chance` has ROOT = location with `scope:disease_outbreak` and `scope:current_presence`
2. The Great Pestilence Situation definition — how diseases register with a Situation, and what governs the Situation's end condition (you need the spawn window to outlive it)
3. `main_menu/common/static_modifiers/province.txt` — the free-land modifiers, to confirm the §3 numbers
4. Pop growth script values — confirm the bracket thresholds (10K pops / 10% capacity)
5. Whatever governs disease resistance gain and resistance decay per disease — the community mod "Disease Resistance Fixed" demonstrates these are per-disease moddable

**In the M&T mod folder:**
6. Their v0.2.7 Situation rework — what they changed, and the registration pattern for the three diseases. Your fourth disease should follow it exactly
7. **Their pop growth file.** v0.2.9 says growth no longer ceases at max population but *tapers against a soft cap* — that's a rewritten curve, not retuned constants. The §3 free-land brackets may not survive in the form the wiki describes. **This single read decides whether lever 2 or lever 4 is even applicable**
8. Their max population system (v0.2.9, climate-driven)
9. Their farming village scripted system

## 7. Validation

**Before building anything:** console `situation great_pestilence` to re-fire it on the current save, and read the actual death toll. You may find the Situation isn't firing at all rather than firing weakly — different bug, different fix. There are reports of the Situation hanging with 2–3 permanently-infected locations and never ending.

**After building:** observer-mode run, checkpoint central Mexico pop at 1550 / 1600 / 1700 / 1800. Success is 1–3M at 1600 **and still under ~4M at 1700.** The second number is the real test — the first is easy to hit and easy to lose again.

**Watch for:** a location with mortality applied but max pop untouched. You'll kill 19 million and watch them regrow, because the soft cap is still up there pulling them back.

## 8. Worth doing first

M&T-EU5 is an active repo with a public issue tracker, and the v0.2.7 rework reads like it shipped before anyone verified the aggregate death toll. A screenshot of a 1600 save with a Mexico pop count is a genuinely useful bug report — possibly more efficient than patching it yourself, and the team would know whether the undershoot is intended-for-now or an oversight.

---

## Sources

- [Diseases — EU5 Wiki](https://eu5.paradoxwikis.com/Diseases)
- [Disease modding — EU5 Wiki](https://eu5.paradoxwikis.com/Disease_modding)
- [Population — EU5 Wiki](https://eu5.paradoxwikis.com/Population)
- [MnT-EU5 changelog](https://github.com/MEIOU-and-Taxes/MnT-EU5/blob/develop/Documentation/Change%20log.md)
- [McCaa, "The Population of Mexico from Origins to Revolution"](https://users.pop.umn.edu/~rmccaa/mxpoprev/cambridg3.htm)
- [Comparative table, Mexican population 1519–1595](https://users.pop.umn.edu/~rmccaa/vircatas/virtab3.htm)
- [Cocoliztli epidemics](https://en.wikipedia.org/wiki/Cocoliztli_epidemics)
