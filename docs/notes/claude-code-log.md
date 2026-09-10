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

## 2026-09-09 — Iteration 13: seconds hand keeps sweeping through a zone wind

**Was:** Per decision 0003 as written, live updates paused during the 1100ms wind. The seconds hand held still for 1.1s and then caught up with a ~6.6° forward jump. Flagged at build (see the first log entry) and reported to Pablo.

**Now:** The wind still carries the captured clockwise delta over 1100ms with the same easing, but each frame also adds how far live time has moved since the wind began (`forward(live, base)` per hand). The seconds hand therefore sweeps normally throughout and all hands land exactly on the live target with no catch-up. ODD is unaffected (its target does not move).

**Verified [VERIFIED: Node harness with stubbed DOM and fake clock, 50ms frames]:** seconds hand advances 0.300° every frame during and after the wind; hour hand eases 0→150° monotonically. The browser pane throttles timers when unfocused, so the live-browser check was inconclusive and the harness stands as the evidence. Harness is in CC's scratchpad, not the project.

**⚠ Touches a locked decision.** Decision 0003: "Live updates pause during the tween and resume after." Superseded by Pablo's instruction; SC to amend alongside the earlier 0002/0003 changes.

**Why:** Pablo, 2026-09-09: *"when the timezone changes the seconds pointer is doing a little stuttering, it should continue to move normally."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** in `frame()`, drop the three `forward(live.*, tween.base.*)` terms and the `live` read; `base` on the tween object becomes unused.

## 2026-09-09 — Promoted to production (vercel.app alias)

**Was:** Production alias `oddments-pi.vercel.app` served the original white build from 2026-09-09 morning.

**Now:** Production is deployment `oddments-fwjjbfpm3-pabs-studio.vercel.app`, i.e. the current `index.html` at commit `4cb2083` (iterations 1–13). Served HTML is byte-identical to the file [VERIFIED: `curl | diff`].

**Why:** Pablo, 2026-09-09: *"promote."* Explicit yes for the vercel.app alias. Custom domains `oddments.design` / `oddments.studio` remain unattached and still gated on a separate yes.

**Affects:** Vercel production state only.

**If reverting:** `vercel rollback` to the previous production deployment, or `vercel promote <previous-url>`.

## 2026-09-09 — oddments.design attached to production

**Was:** Production reachable only at `oddments-pi.vercel.app`. Both Oddments domains registered on Vercel but unattached.

**Now:**
- `oddments.design` → project `oddments` production. HTTPS 200, served HTML byte-identical to `index.html` [VERIFIED: `curl | diff`]. HTTP → HTTPS 308.
- `www.oddments.design` → 308 redirect to `https://oddments.design/`, set via the Vercel API (`PATCH /v9/projects/oddments/domains/www.oddments.design`, `redirect: oddments.design`, `redirectStatusCode: 308`). Not a config file; nothing to redeploy.
- `oddments.studio` **not** attached. Pablo's instruction named `.design` only; `.studio` (and its redirect to `.design`, which Pablo implied on 2026-09-08) waits for a separate yes.

**Why:** Pablo, 2026-09-09: *"promote to oddments.design."* Explicit yes for that domain. The `www` attachment is CC's judgement as part of the same domain.

**Affects:** Public web. The holding page is now live at its official address.

**If reverting:** `vercel domains rm oddments.design` and `vercel domains rm www.oddments.design` (detaches from the project; the registrations stay).

## 2026-09-09 — Iteration 14: larger clock and buttons on phones

**Was:** One size everywhere: clock 216px, buttons 10px type.

**Now:** `@media (max-width: 600px)`: clock 252px (numerals 14px), buttons 12px type with 7px/10px padding, dot 6px with a 10px gap, 8px between buttons. Bottom margin unchanged. Checked at 375×812: five buttons span ~290px, clock centred.

**Why:** Pablo, 2026-09-09: *"clock and button could be a little bigger on mobile."*

**Affects:** `index.html` only. Preview deployed; production (oddments.design) untouched.

**If reverting:** delete the `@media (max-width: 600px)` block.

## 2026-09-09 — Promoted iteration 14 to production

**Now:** oddments.design serves commit `651eb5f` (mobile breakpoint included). Deployment `oddments-3oiavogzm-pabs-studio.vercel.app`. Byte-identical to `index.html` [VERIFIED: `curl | diff`].

**Why:** Pablo, 2026-09-09: *"push to live so i can see it on my phone."*

**If reverting:** `vercel rollback`.

## 2026-09-09 — Iteration 15: day/night toggle

**Was:** Night palette only.

**Now:** A pill in the top-right corner (32px from top and right, same styling as the zone buttons, no countdown fill) labelled NIGHT or DAY for the current mode. Clicking flips `data-theme="day"` on `<html>`, which swaps the six colour tokens; the red accent is shared. Background and text colours cross-fade over 300ms. Choice persists in `localStorage` (`oddments-theme`); default is night. Day palette is the night one mirrored: ground `#ECEAE5`, ink `#161616`, muted `#8C8C8C`, buttons `#E3E1DC` / hover `#DAD8D2` / progress `#CFCDC6`.

Built as a comparison tool for Pablo. Whether it ships, or whether the site follows `prefers-color-scheme` instead, is open.

**Why:** Pablo, 2026-09-09: *"can you give me a toggle so i can see a night mode vs day mode on this."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** remove the `.theme` list, its CSS, the `:root[data-theme="day"]` block, and the Day/night section of the script.

## 2026-09-09 — Iteration 16: toggle reads AM / PM

**Was:** Toggle labelled NIGHT / DAY.

**Now:** PM for the dark palette, AM for the light one. Behaviour unchanged; `localStorage` key and `data-theme="day"` value unchanged.

**Why:** Pablo, 2026-09-09: *"can you just make it say AM or PM."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** two string changes in the toggle markup and `applyTheme`.

## 2026-09-09 — Iteration 17: AM/PM switch as a vertical capsule with a sliding pill

**Was:** Single pill toggle reading AM or PM.

**Now:** A vertical capsule (`.theme`, fixed top-right at the 32px margins, `--button` fill, 14px radius, 3px inner padding) holding two buttons, AM above PM. A `.theme-pill` (`--progress` fill, 11px radius) sits behind the selected one and slides between them over 260ms with the same easing as the selection dot; reduced-motion disables the slide. AM = light palette, PM = dark. Selected label goes to ink. Both buttons carry `aria-pressed`; the group has an `aria-label`. Persistence and default (PM) unchanged. Mobile breakpoint bumps the capsule's type to 12px like the zone buttons.

**Why:** Pablo, 2026-09-09: *"make it work like an am pm clock complication … two modes inside a vertical squircle AM on top PM on the bottom and a little pill that gets behind the one you click."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** restore the single-toggle markup, CSS and script from commit `fc796cb`.

## 2026-09-09 — Promoted iteration 17 to production

**Now:** oddments.design serves commit `1776342` (AM/PM capsule). Deployment `oddments-1hx4wt6kq-pabs-studio.vercel.app`. Byte-identical to `index.html` [VERIFIED: `curl | diff`].

**Why:** Pablo, 2026-09-09: *"can you push live so i can see on my mobile?"*

**If reverting:** `vercel rollback`.

## 2026-09-09 — Iteration 18: wordmark top-left

**Was:** No wordmark on the page.

**Now:** `<p class="wordmark">●◗◗ments</p>` fixed top-left at the 32px margins (mirrors the AM/PM capsule top-right). Inter Medium 12px, ink colour, loaded from Google Fonts (weight 500 only, `display=swap`, with preconnects). This is the page's first external request. Inter has no ◗ glyph, so the two half-discs render in the system fallback; ● and "ments" are Inter. Verified Inter loads [VERIFIED: `document.fonts.check`].

**Why:** Pablo, 2026-09-09: *"can you add this, in inter, medium 12px to the top left? ●◗◗ments"*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** remove the three `<link>` tags, the `.wordmark` rule and the `<p class="wordmark">`.

## 2026-09-09 — Iteration 19: wordmark glyphs set in Noto Sans Symbols 2

**Was:** ●◗◗ in Inter, with ◗ falling back to the system font. The circle rendered visibly larger than the half-discs and the mark differed between the site and Figma because each picked a different fallback.

**Now:** The three glyphs are wrapped in `<span class="mark">` set in Noto Sans Symbols 2, loaded from Google Fonts subset to just ● and ◗ (`text=` parameter, `display=block`), with Apple Symbols → DejaVu Sans as fallbacks. Scaled to 72% so the shapes sit on Inter's x-height and the wordmark reads as one lowercase word. "ments" unchanged (Inter Medium 12px).

**Research behind the choice [VERIFIED: cmap inspection with fontTools on downloaded files]:** fonts carrying both U+25CF and U+25D7 with matched proportions are Noto Sans Symbols 2, Noto Sans Math, DejaVu Sans, and on macOS Apple Symbols, Menlo, Arial Unicode. Inter, Roboto, Source Sans 3, JetBrains Mono have ● only; Fira Sans and IBM Plex Sans have neither. Pablo chose Noto Sans Symbols 2 from a rendered side-by-side; row 1 (100%) or row 8 (72%) were both acceptable to him; CC took 72%.

**Why:** Pablo, 2026-09-09: *"notosans is so good for it either 1 or 8 are great."*

**Affects:** `index.html` only. Second external font request (a few hundred bytes). Preview deployed; production untouched.

**If reverting:** drop the second `<link>`, the `.wordmark .mark` rule and the span.

## 2026-09-09 — Promoted iteration 19 to production

**Now:** oddments.design serves commit `b0d2d6c` (wordmark in Inter + Noto Sans Symbols 2). Deployment `oddments-8zrv1twmo-pabs-studio.vercel.app`. Byte-identical to `index.html` [VERIFIED: `curl | diff`].

**Why:** Pablo, 2026-09-09: *"push live."*

**If reverting:** `vercel rollback`.

## 2026-09-09 — Iteration 20: wordmark removed for now

**Was:** `●◗◗ments` top-left, with Inter and Noto Sans Symbols 2 loaded from Google Fonts.

**Now:** Wordmark element, its CSS, and all four font-related `<link>` tags removed. The page makes no external requests again. The AM/PM capsule is now the only thing in the top corners.

**Why:** Pablo, 2026-09-09: *"you can remove the logo for now."* "For now" — the font research (Iteration 19) stands and the markup is in commit `b0d2d6c` when it returns.

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** restore the links, `.wordmark` rules and `<p class="wordmark">` from `b0d2d6c`.

## 2026-09-09 — oddments.studio attached, redirecting to oddments.design

**Was:** `oddments.studio` registered on Vercel, unattached.

**Now:** `oddments.studio` and `www.oddments.studio` attached to project `oddments`, both set to 308 → `https://oddments.design/` via the Vercel API (same call as the `www.oddments.design` redirect). HTTP on `.studio` upgrades to HTTPS first, then redirects [VERIFIED: curl on all three forms]. TLS issued for both. Both Oddments domains are now live; `.design` is canonical, `.studio` is a redirect.

**Why:** Pablo, 2026-09-09: *"can you point oddments.studio there too."* Redirect rather than serve-directly is CC's reading of Pablo's 2026-09-08 "the official one is .design"; one API call flips it to serving the page directly if wanted.

**Affects:** Public web.

**If reverting:** `vercel domains rm oddments.studio` and `vercel domains rm www.oddments.studio`.

## 2026-09-09 — Iteration 21: favicon

**Was:** No favicon (browsers requested `/favicon.ico` and got 404).

**Now:** Two inline data-URI icons, no extra files, no external requests:
- `rel="icon"`: SVG of the linear mark per decision 0002 (hub 0–32, ◗ 34–50, ◗ 52–68, gap 2), centred in a 72-unit square. Fill `#161616`, switching to `#ECEAE5` under `prefers-color-scheme: dark`, so it reads on both light and dark tab bars. 363 characters.
- `rel="apple-touch-icon"`: 180×180 PNG (4.7KB) of the same geometry, off-white on the `#161616` night ground, for iOS home-screen tiles, which need a bitmap and a solid ground. Drawn on a canvas in the browser from the same numbers; not a hand-made asset.

Safari on macOS ignores SVG favicons and shows the touch icon instead. Open question 9 (favicon and title) is partly answered; the animated-in-the-tab idea from that question is not done.

**Why:** Pablo, 2026-09-09: *"can you generate a little favicon with the logo?"*

**Affects:** `index.html` only (now ~20KB, mostly the PNG). Preview deployed; production untouched.

**If reverting:** delete the two `<link rel="icon"…>` / `<link rel="apple-touch-icon"…>` lines.

## 2026-09-09 — Promoted iteration 21 to production

**Now:** oddments.design serves commit `0229201` (wordmark removed, favicon and touch icon added). Deployment `oddments-ay7qs27iu-pabs-studio.vercel.app`. Byte-identical to `index.html` [VERIFIED: `curl | diff`].

**Why:** Pablo, 2026-09-09: *"promote."*

**If reverting:** `vercel rollback`.

## 2026-09-09 — Iteration 22: rollover tooltips on the zone buttons

**Was:** No tooltips.

**Now:** Each `<li>` carries a `<span class="tip" role="tooltip">` after its button: an inverted pill (ink fill, paper text, 10px uppercase letter-spaced like the buttons) 10px above the button, centred, with a 4px CSS-border triangle tail pointing down. Fades in and rises 4px over 150ms on `li:hover` and on keyboard focus of the button; only inside `@media (hover: hover)` so touch devices never get a stuck tooltip after a tap. Texts: London, New York, São Paulo, 3:15 for ODD, and for YOU the visitor's own city derived from the IANA zone id (`America/New_York` → "New York"), falling back to "You" when the id has no city part.

**Why:** Pablo, 2026-09-09: *"add a nice tooltip on rollover on each of the buttons that say the name of the city … a small floating pill with a kind of point built into the shape pointing down."* "3:15" for ODD and the visitor's-city for YOU are CC's choices.

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** remove the five `.tip` spans, the tooltip CSS block, and the `tip-local` IIFE.

## 2026-09-09 — Iteration 23: AM/PM switch crossfades every surface

**Was:** Only `body` background and text faded (300ms); the clock, numerals, buttons, capsule, pill and tooltips snapped to the new palette.

**Now:** A `--tms` token (0ms at rest) drives colour transitions on body, hub and hands (`fill`), numerals, zone buttons (colour and background), their countdown fill, the capsule, the theme pill (background only; its 260ms slide is untouched), and the tooltips (background, text and tail). `applyTheme(theme, animate)` adds `html.theming` for 750ms on a user click, which sets `--tms: 700ms` and bumps the buttons' hover timing to match for that window. Initial load calls it without `animate`, so a remembered theme applies instantly with no flash. Easing is ease-in-out throughout.

Verified [VERIFIED: computed `transition-duration` per element with and without the class]: 0s at rest, 0.7s during; pill 0.26s/0.7s; tooltip 0.15s/0.15s/0.7s/0.7s. The browser pane cannot play transitions while backgrounded, so no mid-fade capture.

**Why:** Pablo, 2026-09-09: *"make the transitions from day to night a bit more animated and not so abrupt."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** remove the `--tms` / `html.theming` rules and the `animate` branch in `applyTheme`; restore `body`'s 300ms transition.

## 2026-09-09 — Iteration 24: tooltips pop in

**Was:** Tooltip faded in over 150ms while rising 4px.

**Now:** Rest state is `translate(-50%, 6px) scale(0.8)` with the transform origin at the tail tip. On hover/focus it goes to full size over 320ms on an overshooting curve (`cubic-bezier(0.34, 1.56, 0.64, 1)`) while fading in over 160ms, so it pops up out of the button. Leaving uses a separate 120ms plain fade, so there is no bounce on the way out. Reduced-motion still disables all of it.

**Why:** Pablo, 2026-09-09: *"on the little hover tooltips can you make them animate in."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** restore the 150ms transition and the `translate(-50%, 4px)` rest transform from commit `31bfc48`.

## 2026-09-09 — Iteration 25: night ground 10% darker

**Was:** `--paper: #161616` (value 22).

**Now:** `--paper: #141414` (value 20). Button fills unchanged (`#1C1C1C` / `#222222` / `#2C2C2C`), so they sit slightly higher off the ground. Day palette untouched. The Apple touch icon tile still uses `#161616`; not worth regenerating for two levels.

**Why:** Pablo, 2026-09-09: *"can you increase the darkness of the background a little bit? like 10%."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** `--paper` back to `#161616`.

## 2026-09-09 — Iteration 26: night ground another 10% darker

**Was:** `--paper: #141414`.

**Now:** `--paper: #121212` (value 18). Two steps down from the original `#161616` in total. Buttons unchanged.

**Why:** Pablo, 2026-09-09: *"another 10% darker."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** `--paper` back to `#141414`.

## 2026-09-09 — Promoted iteration 26 to production

**Now:** oddments.design serves commit `1f18ea3` (tooltips with pop-in, AM/PM crossfade, night ground `#121212`). Deployment `oddments-cgus8ng48-pabs-studio.vercel.app`. Byte-identical to `index.html` [VERIFIED: `curl | diff`].

**Why:** Pablo, 2026-09-09: *"push to live."*

**If reverting:** `vercel rollback`.

## 2026-09-09 — Iteration 27: tooltips made subtle

**Was:** Inverted pill (ink fill, paper text), 4px tail, 0.8→1 scale pop with a strong overshoot, immediate.

**Now:** Pill uses the countdown-fill grey (`--progress`) with muted text, so it reads as a quieter cousin of the buttons rather than a label shouting over them. 3px tail in the same grey. Rest state 0.94 scale, 4px low; enters over 300ms on a soft-settle curve with almost no overshoot, opacity 220ms, both after a 180ms delay so a cursor passing across the row does not trigger a chain of tooltips. Padding tightened to 4px/7px. Exit unchanged (120ms plain fade, no delay).

**Why:** Pablo, 2026-09-09: *"can we make the tool tips way more subtle."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** restore the `.tip` block from commit `1f18ea3`.

## 2026-09-09 — Iteration 28: tooltip fill black

**Was:** Tooltip pill and tail in the countdown-fill grey.

**Now:** New token `--tip: #000000`, used for the pill fill and the tail, in both palettes. Text stays `--muted`. Page ground unchanged at `#121212`.

**Note:** CC first read "black background maybe" as the page ground and started that change; Pablo stopped it. The rejected command had already written `#000000` to `--paper` and pushed a preview of it (`oddments-fbkgns97c`) before the stop landed. That file change was reverted with `git checkout` before the tooltip edit; nothing from it was committed or promoted.

**Why:** Pablo, 2026-09-09: *"black background for the tooltips not the page."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** point the two `var(--tip)` uses back at `--progress` and drop the token.

## 2026-09-09 — Iteration 29: tooltips removed; wordmark back at 18px

**Was:** Tooltips on the zone buttons (Iterations 22–28). No wordmark since Iteration 20.

**Now:**
- Tooltips gone entirely: the five `.tip` spans, the tooltip CSS, the YOU-city IIFE and the `--tip` token.
- Wordmark restored exactly as Iteration 19 left it (Inter Medium for "ments", Noto Sans Symbols 2 subset for ●◗◗ at 72%, top-left at the 32px corner), with the base size raised from 12px to 18px — 50% bigger as asked; the mark glyphs scale with it (12.96px). It also now takes the theme crossfade. The four font `<link>`s return, so the page makes two external font requests again.

**Why:** Pablo, 2026-09-09: *"i think we can remove the tool tips, when you do that can you bring back the mark where it was yesterday and just make it 50% bigger than it was."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** commit `e25df67` is the last version with tooltips and no wordmark.

## 2026-09-09 — Iteration 30: wordmark 20% smaller

**Was:** Wordmark base size 18px.

**Now:** 14.4px. The mark glyphs follow at 72% of that (10.4px).

**Why:** Pablo, 2026-09-09: *"can you reduce the mark size by 20%."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** `font-size` back to 18px.

## 2026-09-09 — Iteration 31: SPO → SAO

**Was:** Third city button labelled SPO.

**Now:** Labelled SAO (the IATA metropolitan code for São Paulo). Zone id, order and behaviour unchanged.

**⚠ Touches a locked decision.** Decision 0003 specifies the labels as "LDN, NYC, SPO, ODD" and says Pablo specified them. Changed on Pablo's direct instruction; SC to amend 0003 together with the YOU addition and the other flagged items.

**Why:** Pablo, 2026-09-09: *"change SPO to SAO which is the actual code for sao paulo."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** one string.

## 2026-09-09 — Promoted iteration 31 to production

**Now:** oddments.design serves commit `c7e3363` (no tooltips, wordmark at 14.4px, SAO label, black-tooltip token gone). Deployment `oddments-hssispke1-pabs-studio.vercel.app`. Byte-identical to `index.html` [VERIFIED: `curl | diff`].

**Why:** Pablo, 2026-09-09: *"push to live."*

**If reverting:** `vercel rollback`.

## 2026-09-09 — Iteration 32: day palette on Apple's light system colours

**Was:** Day palette was the night one mirrored (warm off-white `#ECEAE5` ground, `#161616` ink, warm greys).

**Now:** Apple's light system palette: ground `#F2F2F7` (systemGroupedBackground), ink `#000000` (label), muted `#8E8E93` (systemGray), button `#E5E5EA` (systemGray5), hover `#D1D1D6` (systemGray4), countdown `#C7C7CC` (systemGray3). Accent stays the Oddments red, not Apple blue. Night palette untouched.

**Why:** Pablo, 2026-09-09: *"for the light version use apple usual interface colours."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** restore the six day tokens from commit `1701470`.

## 2026-09-09 — Iteration 33: no true blacks

**Was:** Day ink `#000000`.

**Now:** Day ink `#1C1C1E`. No `#000` anywhere in the page [VERIFIED: grep]. Night ground remains `#121212`.

**Why:** Pablo, 2026-09-09: *"tone the blacks down a bit so there are no true blacks."*

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** `--ink` in the day block back to `#000000`.

## 2026-09-09 — Promoted iteration 33 to production

**Now:** oddments.design serves commit `70a3d99` (Apple light palette with near-black ink). Deployment `oddments-5y3bfoapk-pabs-studio.vercel.app`. Byte-identical to `index.html` [VERIFIED: `curl | diff`].

**Why:** Pablo, 2026-09-09: *"good push to live."*

**If reverting:** `vercel rollback`.

## 2026-09-10 — Iteration 34: weather tooltips on the city buttons

**Was:** No tooltips (removed in Iteration 29).

**Now:** LDN, NYC and SAO carry an empty `.tip` span that fills with current weather: temperature and one word, e.g. "18° Overcast". Source is Open-Meteo (`api.open-meteo.com/v1/forecast`, no key, CORS-open), one request for all three cities on load and every 15 minutes. WMO weather codes map to Clear / Cloudy / Overcast / Fog / Drizzle / Rain / Snow / Showers / Thunder. Units follow local convention: °F for New York, °C for London and São Paulo (`data-unit` on the button). The tooltip is the quiet design from Iteration 27 (pill on `--tip`, muted text, 3px tail, soft pop after a 180ms delay, hover-only); `--tip` is `#0A0A0A` at night and `#1C1C1E` by day, so no true black. `:empty` tooltips are hidden, so nothing shows until data arrives or if the request fails. YOU and ODD have no tooltip.

This is the page's first runtime data request (fonts aside). Coordinates are city centres, hard-coded on the buttons.

Verified [VERIFIED: API probe with curl; page fetch populates all three spans; no console errors].

**Why:** Pablo, 2026-09-10: *"add weather of each city on roll over the city button? temperature and one word weather description?"* Local-units and no-YOU-tooltip are CC's choices.

**Affects:** `index.html` only. Preview deployed; production untouched.

**If reverting:** commit `2e45921` is the last version without weather.

## 2026-09-10 — Iteration 35: local weather on YOU; Celsius everywhere

**Was:** Weather on LDN/NYC/SAO only; New York in °F.

**Now:**
- All temperatures in °C; the per-button `data-unit` is gone.
- YOU has a weather tooltip. No geolocation prompt: the city is taken from the IANA zone id (`America/New_York` → "New York"), geocoded once via Open-Meteo's geocoding endpoint (`geocoding-api.open-meteo.com/v1/search`), and its coordinates are written onto the YOU button, which then joins the single weather request. If the zone id has no city part, or geocoding fails, YOU simply has no tooltip. Limitation: it is the zone's reference city, not the visitor's actual location — a visitor in Manchester gets London's weather.

Verified [VERIFIED: page fetch populates YOU/LDN/NYC/SAO; geocode probe returns New York 40.714, -74.006; no console errors].

**Why:** Pablo, 2026-09-10: *"can you add the users local weather? and lets show the temps always in Celsius."* Zone-city rather than a location prompt is CC's choice.

**Affects:** `index.html` only. Second Open-Meteo endpoint in use. Preview deployed; production untouched.

**If reverting:** commit `09a0788` (three-city weather, mixed units).
