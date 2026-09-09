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

Open from CC's side: exact colour values are estimated from the screenshot; the seconds-hand hold during a city wind is still as decision 0003 specifies.
