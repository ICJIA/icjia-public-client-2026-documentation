# Phase P3 — Homepage

**Status:** SCAFFOLD v0.1
**Companion to:** `04-PHASED-DELIVERABLE-PLAN.md` §3 (Phase 3)
**Phase:** P3
**Last updated:** 2026-04-24

---

## 1. Overview

Builds out the homepage against mock data — hero, news ribbon, quick-links tabs, research grid, stats — so the design tokens and primitives from P1 are validated against real editorial content before the content archetypes in P4. Also the place where the `07-OPEN-QUESTIONS.md` Q6 hero-variant decision must close.

## 2. Entry criteria

- P2 exit gate met.
- Stubbed or mock data available for hero content, news ribbon, grants, research.
- Homepage copy approved by communications lead.

## 3. Scope of work

- Homepage hero component — static, with three image variants per `07-OPEN-QUESTIONS.md` Q6.
- News ribbon component with mock data; pagination or "see all" link.
- Quick-links tabs component.
- Research grid component.
- Stats component.
- Responsive behavior verified at 320 / 768 / 1024 / 1440 / 1920 px.
- Homepage passes automated accessibility gates.
- Homepage visible on staging with mock data.

## 4. Screens / routes affected

- `/` (homepage)

**TBD — filled in design round 2:** component inventory per hero/ribbon/grid/stats block, aligned to the HTML mockup.

## 5. Components needed

- `HomepageHero`
- `NewsRibbon`
- `QuickLinksTabs`
- `ResearchGrid`
- `StatsBlock`

Built on primitives from P1 (`UButton`, `UCard` flat, `UBadge`, `UIcon`).

**TBD — filled in design round 2:** final component names and internal variants per the mockup.

## 6. Data requirements

Mock data for this phase; real Strapi data lands in P4 once S7 is clear. Content types consulted (from `02-MASTER-DESIGN-PLAN.md` §1.2):

- `news` — for the news ribbon.
- `research-hub-article` — for the research grid.
- `grant` / `funding-opportunity` — for quick links.

## 7. Accessibility gates

Per `06-ACCESSIBILITY-STRATEGY.md` §3 and §5:

- axe-core clean on the homepage.
- Keyboard-only pass: every link/button reachable; focus visible.
- Screen reader pass on the critical reading path (homepage is listed in `06-ACCESSIBILITY-STRATEGY.md` §4).
- Homepage is a WCAG AAA target path (`06-ACCESSIBILITY-STRATEGY.md` §1): 7:1 body contrast, 4.5:1 large text.
- `prefers-reduced-motion` respected on any hero motion.

## 8. Test checklist

- [ ] Responsive at 320 / 768 / 1024 / 1440 / 1920 px.
- [ ] Lighthouse mobile ≥95 with mock data.
- [ ] axe-core clean.
- [ ] Keyboard-only navigation works across all homepage components.
- [ ] Hero variants (per Q6 resolution) render correctly.
- [ ] No CLS regressions on initial paint.

## 9. Exit gate

Homepage renders end-to-end on staging with mock data. Keyboard- and screen-reader-accessible. Loads instantly on throttled mobile network.

## 10. Design references

**TBD — filled in design round 2:** link the homepage sections of the HTML mockup and any hero-variant assets.
