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

## The calibration this gives us

At **full coverage**, the collapse modifiers produce −4.74%/yr against a target
of −1.57%/yr. **Three times stronger than needed.**

That is a good problem. The production design applies these modifiers only to
wave-struck locations, so real coverage will be a fraction of 325 — and the
tuning question becomes what that fraction turns out to be once cocoliztli
actually spreads, rather than whether the levers can move the number.

Half A is solved and then some. Half B has never yet been observed to do
anything, because it died before it could.
