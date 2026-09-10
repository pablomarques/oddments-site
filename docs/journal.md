# Journal — 0018 Oddments Site

Phase-altitude chronological record. Newest entries at the bottom.

---

## 2026-09-08 — Project created. SC I boots.

**SC I**

Project scaffolded at `__BUILD/0018-Oddments Site/`. Named for the long-lived thing (the Oddments site) rather than the current phase (the holding page), so nothing needs re-homing when the real site starts. Number confirmed as next free after 0017-Mofushi; name confirmed by Pablo directly.

### Where this project came from

Unusual origin worth recording. The design work happened in an ordinary chat *before* that chat was booted as an SC. Pablo and the chat worked through logo animation options, rejected the first round, landed on the mark-as-clock concept, iterated it through spacing, numerals, a seconds hand, timezone buttons and winding transitions, and produced a working prototype. Pablo then booted that chat as SC I on the strength of it.

Consequence: the decisions in `docs/decisions/0001`–`0003` were all made and verified with Pablo in live conversation before the documentation system existed. They have been captured retroactively here rather than at the moment of decision. This is a deviation from Pattern #7's capture-as-you-go discipline, forced by the ordering, and it is flagged rather than hidden. Everything recorded was explicitly stated or explicitly approved by Pablo; nothing has been inferred from momentum.

An earlier CC brief was written in that chat but landed in the Claude sandbox rather than on Pablo's machine, so it is not project state. It is being superseded by a proper brief authored from these decision files.

### State at boot

- Concept locked, geometry locked, behaviour locked (decisions 0001–0003).
- Working prototype exists as a rendered widget in the originating chat. Not a project artifact.
- Nothing built on disk. No repo yet.
- Deploy target Vercel, confirmed by Pablo. Stack not yet confirmed — see `open-questions.md`.

### Next

SC I authors CC's boot prompt and the build brief, pointing at this project's `CLAUDE.md` canonical section rather than restating paths. Blocking questions in `open-questions.md` items 1–3 need answers from Pablo first.

---

## 2026-09-08 — CC I boots. Build done locally; deploy path blocked on a Vercel rule.

**CC I**

### Pablo's answers to the blocking questions

1. Domains: both `oddments.design` and `oddments.studio`; `.design` is official.
2. Stack: single static `index.html`, no framework, no build step. Confirmed ("keep it light").
3. Repo: local git repo, yes. Initialised.
4. Iteration surface: Vercel preview deployments.
5. Colours and other unspecified details: CC defaults, iterate later.

Answers annotated inline in `open-questions.md` items 1–3 for SC to turn into decision files.

### Build

`index.html` written and verified against decisions 0001–0003 in a browser: live local time correct to the formulas, ODD lands all three hands at 0° via clockwise travel, LDN wind lands on London time with unwrapped angles still increasing, `aria-pressed` and caption behave. Detail and CC's default choices in `docs/notes/claude-code-log.md`.

Two things for SC, both recorded in the log: a reference-frame wording wrinkle in decision 0003 (0° vs 97.5°), and a visible consequence of "live updates pause during the tween" — the seconds hand holds for 1.1s on a city switch, then catches up.

### Deploy path

Vercel project `oddments` created and linked. Preview deployment is blocked: Vercel forces a project's first deployment to Production, and the production alias is publicly reachable. Three deployments were created and removed within minutes to keep the publish gate intact. Pablo's call (2026-09-08, late): fine for the page to be publicly reachable at the anonymous `vercel.app` alias. First production deployment made 2026-09-09; live at `https://oddments-pi.vercel.app`. Custom domains still unattached and still gated. Subsequent plain deploys land in Preview, so the iteration surface Pablo chose now works.

### State

Build believed complete from CC's side, pending SC verification and Pablo's confirmation of the switch to iteration. Deploy path built and exercised: production at the vercel.app alias, previews for iteration, domains not yet connected.

---

## 2026-09-09 — Iteration begins. Session 1: palette and caption.

**CC I**

Pablo moved straight into design direction after the GitHub setup, so iteration mode is in effect by conduct rather than by a declared switch. Recording that here so SC can formalise it or object.

Pablo supplied a screenshot of the prototype render as the colour and layout reference: dark ground, warm off-white mark, grey numerals, caption between clock and buttons with hours and minutes only. Applied; his instruction was to keep CC's button design for now. Detail in the CC log.

Also this session: GitHub repo `pablomarques/oddments-site` created (public, on Pablo's instruction), `main` pushed. Vercel deliberately not git-connected so production stays behind the publish gate.

Session 1 continued: Pablo asked for the clock much smaller (numerals at 12px → clock fixed at 216px) and the seconds hand shortened so it clears the numerals. The second one changes a number locked in decision 0002; done on Pablo's direct instruction and flagged in the CC log for SC to amend the decision file.

Later in the session: buttons pinned to the bottom of the viewport (32px margin), then the text caption removed entirely on Pablo's instruction — a second locked item (decision 0003) changed by Pablo directly, flagged in the CC log for SC.

Then a run of button refinements: pinned to the bottom, solid quiet fills with no outline, an animated red dot marking the selected one, equal spacing around the dot, and a new YOU button for the visitor's own zone (decision 0003's button set is now five, not four). All in the CC log.

Later still: the dot became the sole active signal, and Pablo added an auto-cycle — the page now walks through the five buttons every 5s with a subtle countdown fill in the active button, until the visitor clicks. This changes the page's default behaviour from "local time, static" to "touring the zones"; it is Pablo's call and is not in decision 0003.

Pablo then noticed the seconds-hand hold during a wind (the consequence CC flagged at build) and asked for it to keep moving. Done: live time now runs through the wind. Third locked-decision item changed by Pablo directly (0003, "live updates pause during the tween"); all three flagged in the CC log for SC.

End of session: Pablo promoted the current build to production and then to `oddments.design` (with `www` redirecting to the apex). **The holding page is live at its official address as of 2026-09-09.** `oddments.studio` remains unattached pending Pablo's word.

Then two more: a mobile breakpoint (promoted to production so Pablo could check it on his phone), and a day/night toggle in the top-right so Pablo can compare palettes. The toggle is a comparison tool; whether it stays is undecided.

The AM/PM toggle became a vertical capsule with a sliding pill (Pablo's design, "like a clock complication"), promoted to production. Then a wordmark: `●◗◗ments` top-left in Inter Medium 12px. That surfaced a real finding for `understanding.md`: the typed mark is portable as a string but not as a rendering, because no common text font carries both ● and ◗ — Inter has the circle only, so the half-discs fall back to whatever symbol font the OS or app has, and the proportions drift. CC inspected font tables and rendered the candidates side by side; Pablo chose Noto Sans Symbols 2, which draws both glyphs to matching proportions and is on Google Fonts. Worth carrying into the products' own wordmarks.

Later the same evening: favicon and Apple touch icon from the locked linear geometry; rollover tooltips (inverted pill, tail, pop-in); the AM/PM switch now crossfades every surface over 700ms; night ground darkened twice to `#121212`. All promoted; oddments.design is current as of the last entry in the CC log.

Late: tooltips tried, made subtle, then removed; wordmark back at 14.4px; SPO renamed SAO (a locked label in 0003 — flagged). Locked items Pablo has changed directly today, for SC to fold into the decision files: seconds-hand length (0002), caption removed, live updates through the wind, button set (YOU added), SPO→SAO, active-state styling (all 0003).

Open from CC's side: exact colour values are estimated from the screenshot; the countdown fill grey is a single token; fate of the AM/PM toggle on the public page. `oddments.studio` now redirects to `.design`; the wordmark was removed again "for now" at the end of the session.
