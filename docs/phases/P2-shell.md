# Phase P2 — Shell and information architecture

**Status:** SCAFFOLD v0.1
**Companion to:** `04-PHASED-DELIVERABLE-PLAN.md` §3 (Phase 2), `02-MASTER-DESIGN-PLAN.md` §3
**Phase:** P2
**Last updated:** 2026-04-24

---

## 1. Overview

Builds the global shell — header, footer, primary nav, mobile drawer, skip link, route announcer — and stubs in placeholder routes for every top-level nav item. The whole site's structural accessibility baseline is established here; no page content yet.

## 2. Entry criteria

- P1 exit gate met.
- Navigation structure from `02-MASTER-DESIGN-PLAN.md` §3.1 confirmed with stakeholders.

## 3. Scope of work

- Global shell: header, footer, primary nav, mobile drawer, search-modal trigger.
- Skip link as first tab-focusable element on every page.
- Site banner (dismissible, `sessionStorage`-backed).
- Route announcer (`aria-live="polite"`) component.
- Breadcrumb component (used by detail and resource pages in P4).
- Mobile drawer with focus trap and Esc-to-close.
- Placeholder routes for every top-level nav item: About, Research, Grant Resources, Partners, News.
- `public/_redirects` skeleton with the first handful of legacy URL entries.
- URL structure locked: `/news`, `/events`, `/grants`, `/researchhub`, `/irb`, `/admin`, etc.

## 4. Screens / routes affected

Placeholder routes:

- `/` (placeholder homepage, content lands in P3)
- `/about`
- `/researchhub`
- `/grants`
- `/partners`
- `/news`
- `/irb`
- `/admin` (SPA stub, wired in P6)

**TBD — filled in design round 2:** exact layout of the header / footer / mobile drawer per the HTML mockup.

## 5. Components needed

- `SiteHeader`
- `SiteFooter`
- `PrimaryNav`
- `MobileNavDrawer`
- `SkipLink`
- `SiteBanner`
- `RouteAnnouncer`
- `Breadcrumb`
- `SearchModalTrigger` (button only; modal itself lands in P6)

**TBD — filled in design round 2:** confirm component names and variants against the design artifacts; add any layout-level components (e.g., container wrappers, grid helpers) that the mockup requires.

## 6. Data requirements

- Navigation config: sourced from `@nuxt/content` (YAML/JSON) per `02-MASTER-DESIGN-PLAN.md` §1.2.
- Footer links: sourced from `@nuxt/content`.
- Site banner content: sourced from `@nuxt/content`.
- Redirects: `public/_redirects`.

No Strapi data consumed in P2.

## 7. Accessibility gates

Per `06-ACCESSIBILITY-STRATEGY.md` §3–5:

- Skip link is the first tab stop on every route.
- `<main>`, `<header>`, `<nav>`, `<footer>` landmarks exactly once.
- Focus visible at every interactive element in both themes.
- Mobile drawer traps focus; Esc closes; focus returns to the opener.
- Route announcer fires on every SPA navigation.
- Breadcrumb is keyboard-navigable and labelled.

## 8. Test checklist

- [ ] Full shell navigable by keyboard only.
- [ ] Screen reader (VoiceOver or NVDA) announces each route change.
- [ ] axe-core clean on every placeholder route.
- [ ] Mobile drawer passes focus-trap and Esc tests.
- [ ] `_redirects` file parses correctly on Netlify preview.

## 9. Exit gate

A user can navigate the full shell structure using keyboard only. Screen reader announces each route change. No content on any page — just shell.

## 10. Design references

**TBD — filled in design round 2:** link the mockup sections for header, footer, mobile nav, site banner.
