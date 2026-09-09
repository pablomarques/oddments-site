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

## 2026-09-08 — Git repo initialised, Vercel project created, preview deploy blocked by Vercel's first-deployment rule

**Was:** No repo, no Vercel project.

**Now:**
- Local git repo at project root, branch `main`, first commit `d49ff12`. No remote.
- Vercel project `oddments` under team pabs-studio, linked from this folder (`.vercel/project.json`, gitignored). Framework preset "Other", output directory `.`. `vercel link` also wrote `.env.local` (a Vercel OIDC token) and added `.env*` to `.gitignore`; both are CLI side-effects, not project files, and neither is committed.
- `.vercelignore` restricts every upload to `index.html` only. Verified on the first deploy: only the page was uploaded (`Builds: .`, single file).
- **No deployment exists right now.** Three were created and all three were removed within minutes, see below.

**Why the removals:** every `vercel deploy` — plain, with `--target preview`, and from a non-main branch — landed in the *Production* environment with the public alias `oddments-pi.vercel.app`, which answered 200 with the page. That breaches decision 0004's publish gate (no public reachability without Pablo's yes), so each was removed as soon as it was seen. Vercel's documentation states the cause: *"The first deployment of a new project is always a production deployment, even when you omit `--prod`."* Removing the deployment resets the project to "no first deployment", so the next one is production again. A preview deployment is therefore impossible until a production deployment exists on the project. Escalated to Pablo.

**Deployment protection observed [VERIFIED: curl]:** unique deployment URLs (`oddments-xxxx-pabs-studio.vercel.app`) redirect to Vercel SSO (302), i.e. previews require a Vercel login to view. The production alias (`oddments-pi.vercel.app`) is public.

**Affects:** Vercel account state (project `oddments` now exists, no deployments). Nothing outside this project's own deploy path.

**If reverting:** `vercel project rm oddments`; delete `.vercel/`, `.env.local`; `rm -rf .git`.
