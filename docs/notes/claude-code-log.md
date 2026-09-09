# Claude Code log — 0018 Oddments Site

Per-change historical trail. One entry per significant implementation change: Was / Now / Why / Affects / If-reverting. Newest at the bottom.

---

## 2026-09-08 — Holding page built as a single static `index.html`

**Was:** Nothing on disk. Decisions 0001–0003 locked; prototype existed only as a widget in the originating SC chat.

**Now:** `index.html` at project root. No framework, no build step, everything inline. Implements:
- Geometry exactly per decision 0002 (viewBox 0 0 180 180, hub r16, hands at x=108/126 with radius-16 arcs, seconds line 70→32 at 1.2 stroke `#E24B4A`, numerals at radius 62, font-size 10, baseline +3.5).
- Behaviour per decision 0003: angle formulas as written; single rAF loop; seconds sweep with milliseconds; `Intl.DateTimeFormat` with `hourCycle: 'h23'`; buttons LDN/NYC/SPO/ODD with `aria-pressed`; ODD snaps hour hand to rotation 0; clicking the active button returns to local; unwrapped monotonically increasing displayed angles; always-clockwise forward delta; 1100ms ease-in-out cubic tween with live updates paused during it; `prefers-reduced-motion` drops the tween to an instant set.
- Caption below the controls showing active label (LOCAL / LDN / NYC / SPO / ODD) and HH:MM:SS.

**Why:** Build mode, implementing the locked decisions. Stack confirmed by Pablo 2026-09-08 ("keep it light").

**Choices made by CC where the decisions were silent (defaults for iteration):**
- Colours: ink `#111`, paper `#fff`, numeral/button grey `#9a9a9a`, resting button border `#e2e2e2`. Accent for active buttons is the seconds-hand red.
- Draw order (open question 8): numerals → hub → hour → minute → seconds. Seconds on top. Chosen so the red line is never hidden by a hand.
- Clock size: `min(72vmin, 520px)`, so the clock is the page and numerals stay above the ~200px failure point on any phone in portrait.
- Buttons: 11px uppercase, letter-spaced, pill border. Caption in the same style.
- Minute hand uses fractional seconds so it sweeps with the seconds hand rather than stepping. The formula in 0003 is unchanged; only `s` carries milliseconds, which 0003 already requires for the seconds hand.

**Known consequence of implementing 0003 as written:** live updates pause during the 1100ms wind, so on a city switch the seconds hand holds for 1.1s and then catches up with a small forward jump (about 6.6°). Flagged for iteration; fixing it would mean sweeping the seconds hand through the tween, which is a refinement of the decision text, not a contradiction. Left as written pending Pablo.

**Frame note for SC:** decision 0003 compares the ODD snap (0°) with "the 97.5° a real clock shows at 3:15". Those are in different frames: in the `rotate()` frame used by the formulas, real 3:15 gives the hour hand 7.5°. Intent unambiguous; built as intended. SC may want to tidy the wording.

**Affects:** This project only.

**If reverting:** delete `index.html`. No other files depend on it.
