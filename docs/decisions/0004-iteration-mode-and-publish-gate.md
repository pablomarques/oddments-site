# 0004 — Iteration happens with CC before publishing; deploy is gated

**Status:** Locked
**Date:** 2026-09-08
**Decided by:** Pablo
**Supersedes:** nothing

## Decision

The holding page is built, then refined by Pablo working directly with CC, and only published live once Pablo says so. Pablo's words: *"i will iterate on design and refinement with CC for a bit before publishing it live."*

Two consequences, both binding on CC.

### 1. The publish step is gated

CC does not deploy to production, connect a domain, or make the page publicly reachable without an explicit yes from Pablo in the moment. This sits under the existing explicit-yes discipline (Pattern #7) and is called out separately here because "deploy to Vercel" is in the project's stated goal and could otherwise read as pre-authorised. It is not. Building the deploy path, configuring it, and having it ready are fine; firing it is not.

### 2. Mode is iteration, not build, for most of this project's life

Per Pattern #21, this project's shape is a short build phase followed by a long iteration phase.

- **Build mode** — SC briefs, CC implements the locked decisions 0001–0003, SC verifies. Short. Ends when the page matches the decisions and runs locally.
- **Iteration mode** — Pablo works with CC directly on design and refinement. SC is out of the loop by default and re-enters only for architectural questions, Pablo escalations, or a journal-reading checkpoint.

The transition from build to iteration is declared, not assumed. CC states when it believes the build is complete; Pablo confirms the switch.

## What this means for how CC works during iteration

- Refinements are Pablo's calls, made live. CC does not need to route them through SC.
- CC still logs to `docs/notes/claude-code-log.md` as it goes, and writes iteration-session entries to `docs/journal.md`. The log is how SC re-enters without a debrief.
- If iteration surfaces something that contradicts a locked decision, that is an architectural question: CC stops and escalates rather than quietly overriding the decision file.

## Open

Whether iteration happens against a local dev server, a Vercel preview deployment, or both. Preview URLs are the obvious middle ground — shareable, not public, and the deploy path gets exercised before it matters. Not decided; logged in `open-questions.md`.

## See also

- `0003-clock-behaviour-and-controls.md`
- `open-questions.md`
