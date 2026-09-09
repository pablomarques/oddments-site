# 0002 — Mark geometry and spacing

**Status:** Locked
**Date:** 2026-09-08
**Decided by:** Pablo, with SC I
**Supersedes:** nothing

## Decision

The mark's proportions and spacing are locked at the values below. They are not to be re-proportioned, re-tracked, or "cleaned up" in implementation.

### Clock layout

Coordinate space `viewBox="0 0 180 180"`. Centre of rotation `90 90`.

| Element | Geometry |
|---|---|
| Hub (●) | `<circle cx="90" cy="90" r="16">` — fixed, never rotates |
| Hour hand (◗) | `<path d="M108 74 A16 16 0 0 1 108 106 Z">` — right half-disc, flat edge at x=108, bulge to x=124 |
| Minute hand (◗) | `<path d="M126 74 A16 16 0 0 1 126 106 Z">` — flat edge at x=126, bulge to x=142 |
| Seconds hand | `<line x1="90" y1="70" x2="90" y2="32">`, stroke-width 1.2, round cap, `#E24B4A` |
| Numerals 1–12 | ring at radius 62 from centre, font-size 10, `text-anchor: middle`, muted grey, baselines offset ~3.5 units below geometric position for optical centring |

### The gaps are the load-bearing part

Hub edge at 106 → hour hand starts at 108. Hour hand ends at 124 → minute hand starts at 126. **Two units of gap in both places.**

Pablo set this directly, choosing gap 2 from a live control after rejecting a looser default. He also set optical trim to 0 — at this tightness the difference between the convex-to-flat join and the flat-to-flat join is small enough not to need correcting. Trim exists as a concept for looser tracking; here it is zero.

### The two hands never occlude each other

They are stacked at different radii (18–34 and 36–52 from centre), not overlaid on a shared axis like a conventional clock. When they coincide in angle they form the logotype rather than hiding one another. SC I stated the opposite at one point in the originating conversation and was wrong; the correction is recorded here so it is not re-inherited.

## Prior bug, for the record

The first version gave each ◗ a full circle's advance width, leaving 16 units of dead space to the left of each half-disc. Pablo caught it: *"the half circles should only occupy half space not the whole space of a circle."* Any future re-layout of the mark in linear (non-clock) form must give a half-disc a half-disc's advance, not a circle's.

## Consequences

- Linear form of the mark at gap 2: hub 0–32, first ◗ at 34–50, second ◗ at 52–68.
- Because the mark is dense and tight, any separation in motion is loud. This is a feature and was chosen knowingly.

## See also

- `0001-holding-page-is-the-mark-as-a-clock.md`
- `0003-clock-behaviour-and-controls.md`
