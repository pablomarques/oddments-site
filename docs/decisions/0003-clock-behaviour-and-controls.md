# 0003 — Clock behaviour, controls and transitions

**Status:** Locked
**Date:** 2026-09-08
**Decided by:** Pablo, with SC I
**Supersedes:** nothing

## Decision

### Hand angles

- Hour: `((h % 12) + m / 60) * 30 - 90`
- Minute: `(m + s / 60) * 6 - 90`
- Seconds: `s * 6`

All rotate about `90 90`. Seconds sweep continuously (include milliseconds), they do not tick. Everything runs off a single `requestAnimationFrame` loop.

Default state is the visitor's local time via `Intl.DateTimeFormat().resolvedOptions().timeZone`.

### Controls

Four small, quiet buttons below the clock, in this order: **LDN, NYC, SPO, ODD**. Pablo specified the set and the labels.

- LDN → `Europe/London`
- NYC → `America/New_York`
- SPO → `America/Sao_Paulo`
- ODD → freezes at 3:15:00

Times are read with `Intl.DateTimeFormat` and `hourCycle: 'h23'` so DST is handled without a table. Clicking an already-active button returns to local time. Active button takes an accent border and accent text, and carries `aria-pressed`. A small caption below the clock shows the active zone label and the time.

### The ODD state snaps the hour hand

In the ODD state the hour hand is placed at exactly 0° rather than the 97.5° a real clock shows at 3:15. This is deliberate. With a creeping hour hand the mark never lands truly horizontal: the hands coincide every 65 minutes 27 seconds, and the coincidence nearest 3 o'clock is 3:16:22 with both hands about 8° below horizontal. Snapping is the only way ODD actually spells ODD.

### Transitions wind, they do not jump

Switching zones animates the hands rather than cutting.

- Displayed angles are kept as unwrapped, monotonically increasing numbers, never normalised to 0–360.
- Travel is **always clockwise**. Forward delta is `((target - current) % 360 + 360) % 360`, added to current. The shortest path is explicitly rejected — the long way round is the character of the transition.
- Tween over 1100ms with ease-in-out cubic. Live updates pause during the tween and resume after.

Because all three cities sit on whole-hour offsets, only the hour hand moves between them. That is truthful and is not to be faked with an invented minute difference.

## Consequences

- The city buttons feel quieter than ODD by construction. If more motion is wanted on a city switch, the honest route is a longer wind that overshoots and settles, not fabricated data. Logged in `open-questions.md`.
- `prefers-reduced-motion`: the clock keeps running (it is information), the winding transition drops to an instant set.

## See also

- `0001-holding-page-is-the-mark-as-a-clock.md`
- `0002-mark-geometry-and-spacing.md`
- `open-questions.md`
