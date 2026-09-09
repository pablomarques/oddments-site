# Open questions — 0018 Oddments Site

Active questions awaiting SC or Pablo. Resolved items move into a decision file and are struck from this list.

## Blocking the CC brief

1. **Which domain(s)?** Pablo referred to "the domain(s)" plural. `oddments.design` is the known primary label address, but the set has not been confirmed, nor whether others redirect to it.
2. **Stack.** SC I's working recommendation is a single static `index.html` with no framework and no build step, on the grounds that it is the fastest thing to iterate on and the page is one object. Not yet put to Pablo as a direct question. Vercel as the deploy target *is* confirmed.
3. **Repo.** Does this project get its own git repo, and is it connected to Vercel via git or deployed directly? Unverified — check before asserting either way (Pattern #23).

## Design, non-blocking

4. **The red.** Only colour in a monochrome system. Either it becomes the Oddments accent across the label, or it is a foreign object here. Worth building a grey-seconds variant to see what is lost.
5. **Numerals at small sizes.** They fail below roughly 200px. Either the clock is always large, or numerals reduce to 12/3/6/9 below a breakpoint.
6. **Sweep vs tick on the seconds hand.** Currently sweeps. A discrete tick would give the piece a pulse. Both are cheap to build behind a flag.
7. **Overshoot on city switches.** Only the hour hand moves between whole-hour zones, so city switches feel quieter than ODD. A wind that overshoots and settles is the honest way to add weight; fabricating a minute difference is not.
8. **Draw order** of the seconds hand relative to the two hands and the numeral ring. Currently inherited from build order rather than chosen.
9. **Favicon and title.** Same geometry animated in the tab, and possibly the current time in `<title>`. Unscoped.

## Later

10. **What the holding page becomes.** When the real site starts, does the clock persist as a fixture, become the 404, or retire? Not urgent, but it changes how much the holding page should invest in structure.
