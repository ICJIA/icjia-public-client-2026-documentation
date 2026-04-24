# Phase P1 — Design system

**Status:** SCAFFOLD v0.1
**Companion to:** `04-PHASED-DELIVERABLE-PLAN.md` §3 (Phase 1), `05-DESIGN-SYSTEM.md`
**Phase:** P1
**Last updated:** 2026-04-24

---

## 1. Overview

The hinge phase for the whole Nuxt track. Translates the tokens in `05-DESIGN-SYSTEM.md` into actual Tailwind v4 `@theme` layers and Nuxt UI 4 config, stands up a primitives storybook, and closes any remaining design-system open decisions. Compromises here cascade into every page built later.

## 2. Entry criteria

- P0 exit gate met.
- `05-DESIGN-SYSTEM.md` v0.1 is current.
- Mockup HTML (`ICJIA_Redesign_v2_squared.html`) available as a reference.

## 3. Scope of work

- Tailwind v4 `@theme` layer with every color, spacing, typography, and shadow token from `05-DESIGN-SYSTEM.md`.
- `app.config.ts` Nuxt UI theme configuration committed.
- Self-hosted Inter and JetBrains Mono via `@nuxt/fonts`; automatic subsetting verified.
- Theme toggle component (dark / light / system) with `prefers-color-scheme` respect and persistence.
- First-pass contrast audit for all documented token combinations; results recorded in `05-DESIGN-SYSTEM.md` §1.4.
- Storybook or Histoire build with a page per primitive: Button, Card (flat variant), Input, Select, Badge, Alert, Skeleton, Kbd, Icon.
- Close open decisions in `05-DESIGN-SYSTEM.md` §7 (exact primary shade, icon set, toggle behavior).

## 4. Screens / routes affected

**TBD — filled in design round 2:** Storybook/Histoire page list finalized against the design artifacts.

## 5. Components needed

Baseline primitives from `05-DESIGN-SYSTEM.md` §1–6:

- `UButton`
- `UCard` (flat variant — see `05-DESIGN-SYSTEM.md` on retired bordered variant)
- `UInput`
- `USelect`
- `UBadge`
- `UAlert`
- `USkeleton`
- `UKbd`
- `UIcon`
- Custom `ThemeToggle` component (dark / light / system)

**TBD — filled in design round 2:** any additional primitives surfaced by the HTML mockup that aren't covered above.

## 6. Data requirements

None — primitives are content-free.

## 7. Accessibility gates

Per `06-ACCESSIBILITY-STRATEGY.md` §3 and §4:

- Every primitive passes axe-core with zero violations.
- Focus rings are visible at the token level, not page-by-page.
- Contrast audit passes WCAG AA baseline; core primitives aim for AAA on text-on-surface combinations where the design permits.
- Both themes (light and dark) audited.

## 8. Test checklist

- [ ] Every primitive renders in Storybook/Histoire in both themes.
- [ ] axe-core clean on every primitive page.
- [ ] Contrast audit recorded in `05-DESIGN-SYSTEM.md` §1.4.
- [ ] Theme toggle persists across reloads and respects `prefers-color-scheme` on first visit.
- [ ] Fonts subset correctly (no FOUT; no unused glyph ranges shipped).

## 9. Exit gate

Every primitive listed above renders correctly in both themes, passes contrast checks, and has a Storybook/Histoire page. A design reviewer signs off.

## 10. Design references

**TBD — filled in design round 2:** link the exact HTML mockup sections consulted per primitive, and any component-level design notes provided alongside them.
