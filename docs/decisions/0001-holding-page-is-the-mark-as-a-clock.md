# 0001 — The holding page is the Oddments mark as a working clock

**Status:** Locked
**Date:** 2026-09-08
**Decided by:** Pablo, with SC I
**Supersedes:** nothing

## Decision

The Oddments holding page is a single object: the mark ●◗◗ rendered as a live, working clock.

- The filled circle (●) is the fixed hub at the centre.
- The two right half-discs (◗◗) orbit the hub as the hour and minute hands. The inner one is the hour hand, the outer one the minute hand.
- A thin red line is the seconds hand.
- Hour numerals sit in a ring around the whole thing.

The page shows the visitor's local time by default.

## Why this and not a logo animation

The starting question was "animate the Oddments logo for a holding page." The first round of answers — fade-in reveal, rotating half-discs, staggered breathing pulse, ripple wave — were rejected by Pablo as boring. His words: *"these are so meh. boring."* That rejection is the origin of this decision and is load-bearing, not incidental: those four are the default motion-graphics vocabulary, technique rather than idea, and a mark this reductive gives technique nowhere to hide.

The clock came from Pablo directly. It works because it is not a loop — it is a functional object that happens to be made of the logo. It also earns its keep on a holding page, which is a surface people leave open in a tab.

## The payoff that makes it specific to this mark

Because the two hands are stacked at different orbital radii rather than overlaid, when both point in the same direction the hub, inner hand and outer hand line up along one ray and the mark resolves into the actual logotype ●◗◗, rotated.

Landing that horizontally — actually spelling ODD left to right — happens at 3:15. See decision 0003 for how the hour hand is handled so it lands exactly.

## Consequences

- The mark must be authored as separate SVG paths rotating about a shared centre, never as a compound path or a raster.
- The piece is information, not decoration. This affects reduced-motion handling: the clock keeps telling time; only the winding transition is dropped.
- Numerals do not survive small sizes, so the clock is the page rather than a mark in a corner.

## See also

- `0002-mark-geometry-and-spacing.md`
- `0003-clock-behaviour-and-controls.md`
- `understanding.md`
