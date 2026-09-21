# First live test, 1488 → 1494 (observer, run A save)

Setup: `nwp.6` stamped all three collapse modifiers on all 325 Mesoamerican
locations, `nwp.7` force-spawned cocoliztli in one. Then six years of observer.
**This is maximum modifier coverage, not the production path** — in the real
design only wave-struck, native-majority locations are stamped.

## What worked

**The Situation starts on contact.** `::NWPLOG::1493:situation
nwp_demographic_collapse STARTED` — the year the first Spanish colony appeared
on Hispaniola. `can_start` fires correctly, and it fires **mid-save**, which
means no fresh 1337 run is needed to test.

**`dominant_culture` works, and the sweep is broad.** `virgin_soil=4378` — the
Situation's `on_monthly` applied virgin soil to 4,378 American locations with
native-majority populations.

**Suppression works, hard.**

| | 1488 | 1494 | change |
|---|---|---|---|
| Mesoamerica | 11,768.4k | **8,795.2k** | **−25.3% in six years** |
| rate | | | **−4.74%/yr** |
| fill | 0.457 | 0.547 | +20% |
| implied cap/loc | 79.3k | **49.4k** | **−37.6%** |

**The loss is deaths, not migration.** Mesoamerica lost 2,973k; the New World
as a whole lost 3,000k. Neighbouring regions moved by under 1% (Andes +0.6%,
Colombia +0.2%, Aridoamerica +0.7%). Essentially all of it is real mortality.

**Capacity reduction works**, at −37.6% against the −0.25 and −0.40 modifiers.
That is between the additive prediction (−65%) and the multiplicative one
(−55%), which suggests some capacity comes from sources the modifier does not
scale. Either way the mechanism functions.

**No parse errors.** Nothing matching `nwp_` or `cocoliztli` in the log outside
our own markers.

## What did not work

**Cocoliztli went extinct.** `cocoliztli_locs` went from 1 to **0** over six
years. Seeded at full presence in one location, it never reached a neighbour.

The cause was three parameters set below M&T's smallpox — r0 `{ 2 4 }` rural
against smallpox's `{ 3 6 }`, a spread threshold of 0.40 against smallpox's
0.15, and a slower calc interval — all justified by the reasoning that enteric
fever is less contagious than smallpox. Epidemiologically true, and it left the
disease unable to establish at all.

Fixed by adopting smallpox's exact spread profile, which demonstrably spreads
across the Americas in this mod, while keeping the lethality and resistance
numbers that are the actual point of cocoliztli.

## Carried to 1501: the rate is on target, not 3x over

| Year | Mesoamerica | fill | implied cap/loc |
|---|---|---|---|
| 1488 | 11,768.4k | 0.457 | 79.3k |
| 1494 | 8,795.2k | 0.547 | 49.4k |
| 1501 | 7,960.6k | 0.494 | **49.6k** |

| Period | Rate |
|---|---|
| 1488–1494 | **−4.74%/yr** |
| 1494–1501 | **−1.41%/yr** |

**Capacity has stopped falling** — 49.4k then 49.6k. So the −4.74%/yr was a
one-off adjustment as the population dropped toward a suddenly lower ceiling,
not the ongoing rate. The earlier "three times too strong" reading was the
transient, and is withdrawn.

**The steady-state rate is −1.41%/yr**, against a target band of −1.21%/yr
(for 3M at 1600) to −1.57%/yr (for 2M). It sits inside the band.

Projecting from 7,960.6k at 1501:

| | Projected | Design target |
|---|---|---|
| 1550 | 3.96M | — |
| **1600** | **1.94M** | **1–3M** ✓ |
| 1700 | 0.47M | under ~4M (but this is too low) |

1600 lands almost exactly on target. **1700 is the problem** — 0.47M against a
historical ~1.5–2M. But that projection holds the modifiers at full strength
forever, which production does not: they carry `years = 100`, so a stamp in
1500 expires around 1600 unless a later wave refreshes it. The taper is built
in; this projection just does not model it.

## Virgin soil alone does nothing to population

Every American region carries `nwp_virgin_soil`. Only Mesoamerica carries the
two collapse modifiers. Over the same 1494–1501 window:

| Region | Rate |
|---|---|
| **Mesoamerica** (collapse modifiers) | **−1.41%/yr** |
| Central America | +0.08%/yr |
| Andes | −0.00%/yr |
| Colombia | +0.04%/yr |
| Aridoamerica | +0.08%/yr |
| Brazil | +0.08%/yr |
| Caribbean | +1.59%/yr |
| East Coast | −1.79%/yr |

Virgin soil is **demographically neutral on its own** — it lowers disease
resistance and life expectancy, but with no disease circulating it changes
nothing. All the movement comes from the two collapse modifiers.

That makes cocoliztli load-bearing rather than optional. The Situation
establishes susceptibility; the disease delivers coverage; the modifiers do the
demographic work. Break the middle link and nothing happens — which is exactly
what the extinct disease produced everywhere outside the debug-stamped region.

East Coast at −1.79%/yr is an outlier with no collapse modifiers on it, worth
a look later.

## The calibration this gives us

The modifiers are **correctly sized for full coverage**, not oversized. That
inverts the tuning problem: cocoliztli does not need to be restrained, it needs
to spread widely enough and recur often enough to keep most of Mesoamerica
stamped. Coverage is the target, not a thing to limit.
