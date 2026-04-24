# ICJIA Public Website — Per-Phase Deep-Dive Docs

**Status:** SCAFFOLD v0.1
**Companion to:** `04-PHASED-DELIVERABLE-PLAN.md`
**Last updated:** 2026-04-24

---

## Purpose

Each file in this folder is an execution-level deep dive for one Nuxt-track phase (P0–P8) in the website rebuild. `04-PHASED-DELIVERABLE-PLAN.md` tells you what each phase is for and what its entry / exit gates are; these files tell you which screens to build, which components to use, which data shapes to query, and which tests to run.

These docs are the frontend engineer's day-to-day reference during that phase.

---

## Current state: scaffolds

This folder was seeded before the HTML design artifacts were shared. Each P{n} doc is a **scaffold**: structure and generic content drawn from `04-PHASED-DELIVERABLE-PLAN.md`, `05-DESIGN-SYSTEM.md`, `06-ACCESSIBILITY-STRATEGY.md`, and `02-MASTER-DESIGN-PLAN.md`, with explicit `**TBD — filled in design round 2:**` markers where design input is required.

Round 2 planning — run after the HTML designs are shared — fills in:

- Screen / route lists per phase
- Component specs per screen (mapped to Nuxt UI 4 primitives and custom components)
- Per-phase visual fidelity targets and design references
- Any phase-specific design tokens beyond those already in `05-DESIGN-SYSTEM.md`

To find every gap, `grep -rn '\*\*TBD — filled in design round 2:\*\*' docs/phases/` returns one hit per section that needs it.

---

## Files

| File | Phase | Focus |
|---|---|---|
| `P0-scaffold.md` | P0 | Decisions and scaffold (Nuxt 4 app, Tailwind v4, Nuxt UI 4, Netlify staging) |
| `P1-design-system.md` | P1 | Token implementation, primitive storybook, theme toggle |
| `P2-shell.md` | P2 | Global shell, nav, IA, placeholder routes |
| `P3-homepage.md` | P3 | Homepage components, mock data |
| `P4-content-archetypes.md` | P4 | Listing / detail / resource / profile templates; preview-mode end-to-end |
| `P5-content-rollout.md` | P5 | Long tail of content types; sitemap, robots, redirect table |
| `P6-search-admin.md` | P6 | Build-time search index, Cmd+K, admin SPA |
| `P7-a11y-polish.md` | P7 | Full a11y audit, Lighthouse, visual polish |
| `P8-cutover.md` | P8 | A/B split, DNS swap, post-cutover monitoring |

Phase ordering is linear and strictly gated — each phase's entry criteria include the prior phase's exit gate being met (plus specific cross-track dependencies, notably the **S7 → P4 gate** from the Strapi track). See `04-PHASED-DELIVERABLE-PLAN.md` §1 for the dependency diagram.

---

## Template structure

Every `P{n}-*.md` file uses the same section order:

1. **Overview** — 2–3 sentences on what this phase builds.
2. **Entry criteria** — from `04-PHASED-DELIVERABLE-PLAN.md`.
3. **Scope of work** — list of things to build (generic now; expanded in round 2).
4. **Screens / routes affected** — TBD until designs land.
5. **Components needed** — baseline from `05-DESIGN-SYSTEM.md`; design-specific components TBD.
6. **Data requirements** — content types and fields consumed.
7. **Accessibility gates** — from `06-ACCESSIBILITY-STRATEGY.md`.
8. **Test checklist** — automated + manual.
9. **Exit gate** — from `04-PHASED-DELIVERABLE-PLAN.md`.
10. **Design references** — TBD until designs are linked.

---

## How to evolve these docs

- Round 2 (after designs shared): fill every `**TBD — filled in design round 2:**` marker. Bump each file's `Status` from `SCAFFOLD v0.1` to `DRAFT v0.2`.
- During implementation of each phase: update the file with actual component names, query paths, and test outcomes as the phase progresses. Treat the file as a living reference, not a pre-committed spec.
- On phase exit: record the phase's sign-off (reviewer, date) at the top of that file.
