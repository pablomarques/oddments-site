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

## 2026-09-09 — First production deployment, on Pablo's explicit yes

**Was:** Vercel project `oddments` with no deployments (see previous entry).

**Now:** Production deployment `oddments-bdf14n3gt-pabs-studio.vercel.app`, publicly reachable at `https://oddments-pi.vercel.app`. Served HTML is byte-identical to `index.html` [VERIFIED: `curl | diff`]. Project docs return 404 [VERIFIED: four paths]. No custom domain attached.

**Why:** Vercel forces a project's first deployment to Production. Pablo, 2026-09-08: *"its all good if it publicaly reachable no one knows that url and we will have something live soon."* That is a yes for the page at the `vercel.app` alias only. Attaching `oddments.design` / `oddments.studio` remains gated on a separate explicit yes (decision 0004).

**Affects:** Vercel account state only. This project's deploy path is now: `vercel deploy` → Preview (login-gated); `vercel deploy --prod` → Production (public at the alias above).

**If reverting:** `vercel remove <deployment-url> --yes`. The alias goes 404 once no production deployment exists.

## 2026-09-09 — GitHub remote added

**Was:** Local-only git repo.

**Now:** Public repo `github.com/pablomarques/oddments-site`, remote `origin`, `main` pushed and tracking. Created private, then made public on Pablo's instruction the same day (*"it doesnt need to be private"*). Naming follows Pablo's existing repos (lowercase kebab, no project number). Commits are authored with Pablo's GitHub noreply address because his account blocks pushes that expose a private email; the first push was rejected for that reason and all commits were rewritten before any reached GitHub.

**Why:** Pablo, 2026-09-09: *"github is important for us to keep our stuff versioned and safe and portable."*

**Not done, deliberately:** the Vercel project is not connected to the GitHub repo. A git integration would auto-deploy every push to `main` as Production, which would bypass the publish gate in decision 0004. Deploys stay CLI-driven: `vercel deploy` for previews, `vercel deploy --prod` only on Pablo's yes.

**Affects:** Pablo's GitHub account (one new public repo). Nothing outside this project.

**If reverting:** `git remote remove origin`; delete the repo on GitHub.

## 2026-09-09 — Iteration 1: dark palette and caption layout from Pablo's reference screenshot

**Was:** CC defaults — white ground, near-black mark, caption below the buttons in 11px letter-spaced type showing HH:MM:SS.

**Now:** Matches the screenshot Pablo supplied (his original prototype render):
- Ground `#161616`, mark `#ECEAE5` (warm off-white), numerals and caption `#8C8C8C`, button rule `#2E2E2E`. Seconds red unchanged (locked).
- Caption moved between clock and buttons, 17px, shows label + HH:MM (no seconds). Buttons keep CC's design, 40px below the caption.
- Colour values are eyeballed from the screenshot, not sampled; Pablo can correct them.

**Why:** Pablo, 2026-09-09: *"this is a screen shot of the colors to use and the design of the clock, the buttons you can keep the designs you made for now."* First iteration-mode change.

**Affects:** `index.html` only. Deployed as a Vercel preview, production untouched.

**If reverting:** restore the five CSS token values, move `<p class="caption">` back below `<ul class="controls">`, reinstate `:SS` in `updateCaption`.

## 2026-09-09 — Iteration 2: fixed clock size, shorter seconds hand

**Was:** Clock at `min(72vmin, 520px)`; seconds line `y1=70 y2=32` (radius 20→58), whose tip reached the numeral glyphs.

**Now:**
- Clock fixed at **216px** wide on every screen. At 1.2px per viewBox unit the numerals (font-size 10 units) render at exactly 12px, which was Pablo's target. Everything inside the SVG scales with it; the locked geometry is untouched.
- Seconds line now `y2=38` (radius 20→52), flush with the minute hand's outer edge, leaving ~5 units to the numeral glyphs. Measured from Pablo's reference screenshot, where the line ends at about radius 52.
- Caption 13px, 32px below the clock; buttons unchanged, 24px below the caption.

**Why:** Pablo, 2026-09-09: *"red pointer should not touch the numbers and everything needs to be a lot smaller, reduce the whole thing proportionally … until the typeface for the numbers are 12px in size."*

**⚠ Touches a locked number.** Decision 0002 specifies the seconds hand as `y2="32"`. Pablo asked for the change directly, so it is implemented, not quietly overridden — SC should amend 0002 (seconds line 70→38) at the next checkpoint. Also worth noting for SC: `understanding.md` says numerals fail below ~200px; at 216px they hold at 12px, so that figure is confirmed close to the edge.

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** clock width back to `min(72vmin, 520px)`, `y2` back to 32, caption 17px/48px, controls 40px.

## 2026-09-09 — Iteration 3: buttons pinned to the bottom of the viewport

**Was:** Buttons in normal flow, 24px below the caption, the whole stack centred.

**Now:** `.controls` is `position: fixed`, centred horizontally, `bottom: calc(32px + env(safe-area-inset-bottom))`. Clock and caption remain centred in the viewport as a group.

**Why:** Pablo, 2026-09-09: *"align the buttons to the bottom of the page with a certain margin."* Margin value 32px is CC's pick; one number to change.

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** restore the previous `.controls` block (static, `margin: 24px 0 0`).

## 2026-09-09 — Iteration 4: text caption removed

**Was:** Caption between clock and buttons showing zone label and HH:MM.

**Now:** No caption. Element, its CSS, and the `updateCaption` / `LABELS` / `pad` code are gone. The active zone is signalled only by the pressed button (`aria-pressed` plus accent styling). The clock is now the only thing centred in the viewport.

**Why:** Pablo, 2026-09-09: *"remove the text clock."*

**⚠ Touches a locked decision.** Decision 0003 specifies "a small caption below the clock shows the active zone label and the time." Removed on Pablo's direct instruction; SC to amend 0003 alongside the seconds-hand change in 0002 (see Iteration 2).

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** restore from commit `e200038` (last version with the caption).

## 2026-09-09 — Iteration 5: buttons smaller, solid fill, no outline

**Was:** 11px uppercase pills with a 1px `#2E2E2E` outline, transparent fill, accent outline when active.

**Now:** 10px, padding 5px 10px, gap 6px, no border. Solid fill `#1C1C1C` (ground is `#161616`); hover and active fill `#222222`. Active text stays accent red; hover text goes to ink. `--rule` token removed, `--button` and `--button-hover` added.

**Why:** Pablo, 2026-09-09: *"make the buttons a little smaller and more delicate maybe with solid backgrounds with a very subtle change of color from the bg color and without outlines."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** restore the `.controls button` block and `--rule` token from commit `a493df2`.

## 2026-09-09 — Iteration 6: selected button marked by an animated red dot instead of red text

**Was:** Selected button showed its label in accent red.

**Now:** Each button contains an empty `<span class="dot" aria-hidden="true">` before the label. Selected state: text in ink colour, and the dot animates from `width: 0; margin-right: 0; opacity: 0` to `5px / 6px / 1` over 260ms (ease-in-out curve), so the pill grows smoothly to fit. Deselecting reverses it. `prefers-reduced-motion` disables the transition. Decision 0003's "accent border and accent text" for the active button is now superseded by this marker on Pablo's instruction (border already gone in Iteration 5).

**Why:** Pablo, 2026-09-09: *"instead of turning the whole three letters red … use the terminal style circle glyph in front of the name of the selected one. the button should grow to accommodate it and that growth should be animated smoothly."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** remove the four `<span class="dot">` elements and the `.dot` rules; set `[aria-pressed="true"]` colour back to `var(--accent)`.

## 2026-09-09 — Iteration 7: equal spacing around the dot

**Was:** Button side padding 10px, dot-to-label gap 6px.

**Now:** Both 8px. Edge→dot, dot→label, and label→edge are the same measure.

**Why:** Pablo, 2026-09-09: *"the margin between the button edges and the dot and the dot and three letter words should be the same."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** padding back to `5px 10px`, dot `margin-right` back to 6px.

## 2026-09-09 — Iteration 8: YOU button for the visitor's local time

**Was:** Four buttons (LDN, NYC, SPO, ODD). Local time was the unlabelled default state with nothing pressed.

**Now:** Five buttons: **YOU, LDN, NYC, SPO, ODD**. YOU maps to the visitor's own zone and is pressed by default. Clicking an active city still returns to local, which now reads as YOU lighting up. Clicking YOU while it is active does nothing.

Selection is tracked by button key (`'local'`, a zone id, or `'ODD'`) rather than by resolved zone. First cut compared resolved zones and lit both YOU and NYC for a visitor in New York — which is Pablo. Fixed before deploy.

Decision 0003's button set was four; YOU is a Pablo addition. Position (first) is CC's choice.

**Why:** Pablo, 2026-09-09: *"can you add a new button called YOU that has the users local time."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** remove the YOU `<li>`; the key-based selection logic can stay.

## 2026-09-09 — Iteration 9: selection dot vertically centred

**Was:** Dot was an inline-block with `vertical-align: middle` and a -1px nudge, which sat it on the text baseline's middle rather than the button's.

**Now:** Buttons are `inline-flex; align-items: center; line-height: 1`; the dot is a flex item. Measured: dot centre and button centre coincide to the pixel.

**Why:** Pablo, 2026-09-09: *"the circle in the buttons should be vertically centralized too."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** restore the inline-block dot rules from commit `b42ff0a`.

## 2026-09-09 — Iteration 10: the dot is the only active signal

**Was:** Active button also switched text to ink and fill to the hover shade.

**Now:** Active button is identical to an inactive one apart from the red dot. Hover treatment unchanged (it is a hover, not an active signal). `aria-pressed` still carries the state for assistive tech.

**Why:** Pablo, 2026-09-09: *"dont change anything else in the button to say it is the active button, the dot alone suffices."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** re-add the `[aria-pressed="true"]` colour/background rule from commit `e3f6689`.

## 2026-09-09 — Iteration 11: auto-cycle through the buttons with a countdown fill

**Was:** Page opened on YOU and stayed there until clicked.

**Now:** On load the page cycles YOU → LDN → NYC → SPO → ODD → YOU… every 5000ms (`CYCLE_MS`), each flip using the normal clockwise wind. While cycling, the active button's fill sweeps left→right over the 5s as a countdown: a `::before` pseudo-element whose width is `calc(var(--p) * 100%)`, set from the rAF loop; fill colour is the hover shade (`--button-hover`, #222 on #1C1C1C), so it is deliberately subtle. Any button click calls `stopCycling()`: cycling stops for the rest of the visit, the `cycling` class comes off `.controls`, and the fill is cleared. Label text is now wrapped in a `<span>` so it stacks above the fill.

The visitor's click still follows the existing rules (click a city → that city; click the active one → YOU). Note that if the click lands on a button the cycle has just moved onto, that reads as "click the active one" and returns to YOU. Observed once during testing; it is the rule working as designed, but worth knowing.

Decision 0003 has no auto-cycle; this is a Pablo addition.

**Why:** Pablo, 2026-09-09: *"make the time now cycle between the different places every 5 seconds unless the user clicks a button … also use the background of the active button as a little progressbar."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** remove `stepCycle`/`stopCycling`/`activeButton`, the `cycling` state and class, the `::before` rules, and unwrap the label spans. Commit `af51b1f` is the last pre-cycle version.

## 2026-09-09 — Iteration 12: countdown fill more visible

**Was:** Fill used the hover shade `#222222` on a `#1C1C1C` button.

**Now:** New token `--progress: #2C2C2C` for the fill. Hover shade unchanged.

**Why:** Pablo, 2026-09-09: *"can you make the progress bar a little bit more visible."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** point the `::before` background back at `--button-hover` and drop the token.
