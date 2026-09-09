# CLAUDE.md — 0018 Oddments Site

## What this project is

The website for **Oddments**, Pablo's side brand/label for his personal design projects, which he intends to productise over time. Oddkit (0012), Oddscale (0013) and Oddfaces (0014) are products under the label and live as their own sibling projects; this project is the label's own web presence.

**Current phase: holding page.** Pablo owns the domain(s) and wants something there now — small, cute, finished. The full site comes later. The project is named for the long-lived thing so nothing is renamed or re-homed when the real site begins.

The holding page is one piece: the Oddments mark (●◗◗) rendered as a working clock. See `docs/decisions/`.

## Scope

**In scope:** the holding page, its deployment, and later the full Oddments site.

**Out of scope:** the products themselves (Oddkit, Oddscale, Oddfaces each have their own project), and Pablo's other brand tiers (pablomarques.design / pabs.studio).

## Boundaries — file scope CC may touch

CC works only inside this project's root. CC does not touch sibling project folders, does not touch `0000-Methodology/`, and does not restructure `__BUILD`. Per the standing rules in `0000-Methodology/working-with-pabs.md` ("Pablo's environment — standing rules"), project structure belongs to Pablo: proposing a structural change is welcome, acting on one without an explicit yes is prohibited.

## Canonical paths and locations

- **Project root:** `/Users/pablomarques/Library/CloudStorage/Dropbox/__BUILD/0018-Oddments Site/`
- **Methodology root:** `/Users/pablomarques/Library/CloudStorage/Dropbox/__BUILD/0000-Methodology/`
- **Decisions:** `docs/decisions/` (index at `docs/decisions/README.md`)
- **Journal:** `docs/journal.md`
- **Acquired understanding:** `understanding.md`
- **Open questions:** `open-questions.md`

### Canonical boot reading list

Any SC or CC booting on this project reads, in order:

1. `0000-Methodology/MASTER-PLAYBOOK.md`
2. `0000-Methodology/working-with-pabs.md` — including "Pablo's environment — standing rules"
3. This file
4. `docs/decisions/README.md`, then the decision files it indexes
5. `understanding.md`
6. `docs/journal.md` (most recent entries)
7. `open-questions.md`

### Current SC

**SC I** — the first SC of this project. A rotating SC increments this line as a standing step (Pattern #8).

## Roles on this project

- **SC** — decisions, scope, architecture, the briefs that drive CC.
- **CC** — implementation and deployment.
- No RC (Pattern #24): the domain is not substantially outside the software.
- No AC (Pattern #26): no generated creative assets. The mark is authored vector geometry, not generated.
