# ICJIA Public Website — Accessibility Strategy

**Status:** DRAFT v0.1
**Companion to:** `02-MASTER-DESIGN-PLAN.md`, `TEST-PLAN.md` (forthcoming)
**Scope:** Accessibility targets, architectural approach, automated and manual testing gates, CMS-authored content handling
**Last updated:** 2026-04-23

---

## Overview

Accessibility is treated as an architectural property of the new site, not a post-hoc audit target. The bar is higher than for commercial work because this is a state government agency site: public Illinois residents include many with disabilities who depend on accurate, navigable information about criminal justice services, grants, and agency activities.

The prior Vue 2 codebase carried 24 runtime DOM-fixing functions in `src/a11y.js` that patched missing ARIA roles, bad table headers, heading-order skips, and so on — symptoms of components emitting incorrect markup. The new architecture aims to eliminate that entire class of problem by starting with components that emit correct markup.

This document is the single source of truth for the accessibility approach. The actual test procedures, checklists, and pass/fail criteria live in `TEST-PLAN.md`.

---

## 1. Target compliance levels

- **WCAG 2.1 AA** — baseline across every public page, both light and dark modes. Non-negotiable.
- **WCAG 2.1 AAA** — target for core reading paths (homepage, news detail, research article, grant detail, policy page, about page). AAA where achievable means 7:1 body contrast, 4.5:1 large-text contrast, and no justified body text.

The prior codebase's claim of axe-core "57/57 clean" is approximately 30% of WCAG coverage — the automated tools catch structural issues but miss keyboard flow, screen-reader experience, reading order, cognitive load, and contrast on overlays. Pass on automated tests is necessary but not sufficient.

---

## 2. Architectural approach

**Use components that emit correct markup.** Nuxt UI 4 / Reka UI primitives have been audited upstream. Dialogs trap focus. Popovers announce. Buttons are actual `<button>` elements, not clickable `<div>`s. Form labels are associated programmatically. If we find ourselves writing a runtime fix for a Nuxt UI component, we file an upstream issue instead of patching in userland.

**Accessibility belongs in the design system, not layered on top.** Focus rings, color contrast, touch-target sizes, and motion sensitivity are token-level decisions in `05-DESIGN-SYSTEM.md`. Any component that doesn't meet those tokens isn't built correctly; the fix is at the component level, not a post-render wrapper.

**CMS-authored content gets validated at build time.** Strapi authors can produce heading-order issues, tables without proper headers, and missing alt text. The solution is threefold:

1. Author guidance documented directly in Strapi's admin UI (tooltips, preview warnings).
2. A narrow set of content-rendering composables that validate CMS output at build time and surface errors during `nuxi generate`.
3. The build fails on severe content issues (empty `<h1>`, missing alt text on images with non-empty captions, etc.). Warnings are logged; failures block deploy.

This replaces the runtime `fix*` pattern. The prior codebase's `fixBlankTableHeadings`, `fixHeadingOrder`, `fixEmptyTableHeaders`, `fixFootnoteTargetSize`, `fixFigureTabindex`, and `fixAriaRoleAttribute` become build-time validators. If a validator catches something at build time that should have been prevented at authoring time, we improve author guidance until it stops happening.

---

## 3. Automated gates (every deploy)

These run in CI on every pull request and every production deploy. Failures block the merge/deploy.

- **axe-core** across every page. Zero violations required.
- **Lighthouse CI** with per-page accessibility score budget of 100.
- **Link checker** catches broken internal links.
- **HTML validator** catches malformed output (unclosed tags, invalid nesting).
- **CSS contrast linter** validates tokens-as-used against documented contrast floors (see `05-DESIGN-SYSTEM.md` §1.4).
- **Build-time content validators** (see Section 2 above) catch CMS content issues before publish.

---

## 4. Manual gates (per sprint)

Automated tests cover the structural surface. The experience itself requires human review. Per sprint:

- **VoiceOver (macOS)** pass on homepage, one news detail, one research article, one grant detail, one policy page.
- **NVDA (Windows)** pass on the same five pages.
- **Keyboard-only** pass on every interactive surface modified in the sprint. Every control reachable via Tab; focus visible at every step; Esc closes dialogs; focus returns to the opener.
- **200% zoom** pass on critical reading paths. No horizontal scrolling required for reading body content.
- **Reduced-motion** pass. All non-essential motion respects `prefers-reduced-motion: reduce`.
- **Both themes.** Every manual test is performed in both light and dark mode. Contrast regressions in dark mode are as serious as in light.

---

## 5. Start-of-project checklist

Adapted from `docs/NUXT-ARCHITECTURE-RECOMMENDATIONS.md`. To be handed to the accessibility consultant (if engaged) at project kickoff, not at the end.

- [ ] Keyboard-only navigation: every interactive element reachable via Tab
- [ ] Focus visible at every step (no missing or hidden focus rings)
- [ ] Modals trap focus; Esc closes; focus returns to the opener on close
- [ ] Route changes announced via `aria-live` region
- [ ] Headings follow hierarchy (H1 → H2 → H3, no skips)
- [ ] Landmarks: single `<main>`, proper `<header>` / `<nav>` / `<footer>`
- [ ] Forms: programmatic label association; error messages in `role="alert"`; first-invalid-field focus on submit failure
- [ ] Color contrast ≥ 4.5:1 for body text, ≥ 3:1 for UI components (manual check on semi-transparent overlays — automated tools miss these)
- [ ] Touch targets ≥ 44×44 CSS pixels (WCAG 2.5.5)
- [ ] Respects `prefers-reduced-motion`
- [ ] Readable at 200% zoom (WCAG 1.4.4)
- [ ] `<iframe>` titles for embeds (YouTube, Tableau, Google Forms, Vimeo)
- [ ] External links: `rel="noopener noreferrer"` + visible "opens in new window" indicator for screen readers
- [ ] `lang` attribute on `<html>` (and on foreign-language spans once bilingual content exists)
- [ ] Screen-reader pass (VoiceOver + NVDA)
- [ ] Print styles (government content often needs to print readably)
- [ ] Both light and dark modes meet all of the above

---

## 6. What we are not doing

Explicit non-goals, to prevent scope drift:

- **No runtime DOM-fixing.** If a Nuxt UI component emits bad markup, we fix the component (or file upstream). We do not patch after render.
- **No custom focus-trapping.** Reka UI's dialog primitive handles this; it's been audited by paid consultants.
- **No `outline: none` without a paired `:focus-visible` replacement.** A CI rule should catch violations.
- **No color-only state indicators.** Chip status, form validation, required-field markers — all paired with icons or text.
- **No Lighthouse score as the sole perf-or-a11y metric.** Lighthouse runs on one device profile; real users have screen readers, keyboard-only workflows, low-vision zoom, and older devices. RUM via Plausible Web Vitals for real-user signal.
