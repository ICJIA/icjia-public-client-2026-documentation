# ICJIA Public Website — Design System

**Status:** DRAFT v0.1
**Companion to:** `02-MASTER-DESIGN-PLAN.md`
**Scope:** Visual tokens, typography, geometry, elevation, motion, and component mapping for the redesigned ICJIA site
**Last updated:** 2026-04-23

---

## Overview

The approved visual direction (see `ICJIA_Redesign_v2_squared.html` in project knowledge) establishes a dark-first, editorial, deliberately angular visual language. This is a meaningful break from the current Material-derived palette. This document is the single source of truth for every visual decision in the new site. Components and pages reference it; they do not override it.

This document translates the HTML mockup's design tokens into the Nuxt UI 4 theming system. It is expected to grow during Phase 1 as tokens are validated against real content and contrast audits.

---

## 1. Color tokens

The mockup uses CSS custom properties mapped through a `[data-theme]` attribute. In Nuxt UI 4, we express these through the `ui` config's semantic color aliases and Tailwind v4's `@theme` layer.

### 1.1 Dark theme (default)

| Token | Value | Usage |
|---|---|---|
| `--bg` | `#0a0f1a` | Page background |
| `--bg-2` | `#0f1626` | Footer, secondary surfaces |
| `--bg-3` | `#131c30` | Modal backgrounds, card default |
| `--surface` | `#182238` | Hover states, nav link hover |
| `--surface-2` | `#1e2a44` | Keyboard shortcut badges, nested surfaces |
| `--text` | `#e8edf7` | Primary text |
| `--text-2` | `#a8b2c8` | Secondary text, nav links |
| `--text-3` | `#6e7a92` | Tertiary text, metadata, captions |
| `--border` | `rgba(255,255,255,0.08)` | Default borders, card outlines |
| `--border-2` | `rgba(255,255,255,0.14)` | Hover borders, emphasized dividers |
| `--accent` | `#2f6bff` | Primary brand, CTAs, active states |
| `--accent-2` | `#10b981` | Success, secondary CTA |
| `--accent-warn` | `#f59e0b` | Warning, notice states |

### 1.2 Light theme

| Token | Value |
|---|---|
| `--bg` | `#f6f7fb` |
| `--bg-2` | `#ffffff` |
| `--bg-3` | `#eef1f7` |
| `--surface` | `#ffffff` |
| `--surface-2` | `#f1f4fa` |
| `--text` | `#0b1324` |
| `--text-2` | `#475069` |
| `--text-3` | `#7a8296` |
| `--border` | `rgba(10,15,26,0.08)` |
| `--border-2` | `rgba(10,15,26,0.14)` |

Accents (`--accent`, `--accent-2`, `--accent-warn`) stay the same across themes; they are already chosen to read well on both backgrounds.

### 1.3 Nuxt UI 4 theme config

Sketch for `app.config.ts` — to be formalized during Phase 1:

```ts
export default defineAppConfig({
  ui: {
    colors: {
      primary: 'blue',    // base for --accent; tuned to #2f6bff
      secondary: 'emerald',
      neutral: 'slate',
      success: 'emerald',
      warning: 'amber',
      error: 'rose',
    }
  }
})
```

The exact shade for `primary` (`#2f6bff`) sits between Tailwind's `blue-500` (`#3b82f6`) and `blue-600` (`#2563eb`). We define it in a custom Tailwind theme layer rather than bending a stock palette.

### 1.4 Contrast verification

Must pass before tokens are considered final. Numbers listed below are from initial calculation; formal audit is a Phase 1 deliverable.

- Dark: `--text` (#e8edf7) on `--bg` (#0a0f1a) → 16.5:1 ✓ AAA
- Dark: `--text-2` (#a8b2c8) on `--bg` → 9.1:1 ✓ AAA
- Dark: `--accent` (#2f6bff) on `--bg` → 5.3:1 ✓ AA, not AAA — links need underline + icon where semantically load-bearing
- Dark: `--text-3` (#6e7a92) on `--bg` → 4.9:1 ✓ AA only — **not for body text; metadata only**
- Light: all combinations pending formal audit

---

## 2. Typography

Two families, no exceptions:

- **Inter** (400, 500, 600, 700, 800) — headings, body, UI
- **JetBrains Mono** (400, 500) — keyboard shortcuts (`kbd` elements), code, data

Both self-hosted via `@nuxt/fonts` with automatic subsetting. No webfont icons anywhere. The prior codebase's six-font stack (Lato + Oswald + Roboto + Raleway + Gentium + Material Icons + MDI) is not carried forward.

Type scale uses Tailwind's defaults. Headline scale uses `clamp()` for fluid sizing: `h1` on hero is `clamp(38px, 5.6vw, 64px)`, weight 700, letter-spacing `-0.03em`, line-height 1.05. Body is 15–17px depending on context, line-height 1.55–1.6.

Font features enabled globally: `cv11` (disambiguated `l`), `ss01` (alternate stylistic set). These give Inter a slightly more distinct feel without being precious about it.

---

## 3. Geometry

**Zero border-radius on primary surfaces.** Cards, buttons, hero elements, news items, and quick-link tiles are squared. This is the signature of the new design language and should not drift back toward rounded corners during implementation.

Exceptions:

- Icon buttons in the nav: 9px (touch-target rounding, not aesthetic)
- Chips and pill-style tags (the "NEW" badge, filter chips): 999px (fully rounded, by intent — contrast with the squared grid)
- Focus rings: 2px solid `--accent`, offset 2px, never removed
- Keyboard shortcut `kbd` elements: 5px (slight softening for legibility)

Spacing follows Tailwind's default scale. Section vertical rhythm is 80px between primary sections on desktop, 48px on mobile.

---

## 4. Elevation and card variants

The mockup exposes three card styles via `[data-cardstyle]`: `flat` (default), `glass` (translucent with backdrop-blur), `bordered` (transparent with visible border only). These were useful for design iteration. For v1 production we commit to:

- **`flat`** — default card treatment for all content cards (news, events, quick-links, research)
- **`glass`** — reserved for hero overlays and modal contexts only
- **`bordered`** — retired from production (it was exploratory)

Shadow is restrained and purposeful:

```css
--card-shadow: 0 1px 0 rgba(255,255,255,0.04) inset, 0 12px 40px -12px rgba(0,0,0,0.6);
```

The inner highlight + outer drop is the distinctive card treatment. It should be defined as a Tailwind v4 utility and used consistently, not reinvented per-component.

---

## 5. Motion

Transitions are short (150–250ms) and functional. Hover lifts are 1–3px translate-y. No bouncing, no spring physics, no decorative scroll animations. `prefers-reduced-motion: reduce` disables all non-essential motion globally via a single CSS query.

The prior codebase's AOS (animate-on-scroll) library is not ported; scroll-driven entrance animations, if used at all, are CSS `@starting-style` based.

---

## 6. Component library mapping

The component inventory in `NUXT-REWRITE-PLAN.md` ("Component Mapping: Vuetify → Nuxt UI") is carried forward without significant change. The mapping is largely mechanical; the creative work is in the design tokens above, not the component swap.

Reference that table for the concrete `v-card` → `UCard`, `v-btn` → `UButton` correspondences. A consolidated, authoritative version will be produced during Phase 1 as part of the Storybook/Histoire buildout; this document will then reference that version instead.

---

## 7. Open design decisions

Decisions that belong in this document but are not yet made. Tracked here rather than in `07-OPEN-QUESTIONS.md` because they're design-system-local.

1. **Exact primary blue shade.** `#2f6bff` is the mockup value. We may want to nudge toward Tailwind's `blue-600` (`#2563eb`) for closer alignment with the stock palette. Decide in Phase 1 after contrast audit with real content.
2. **Data-visualization palette.** The site has charts (research datasets, grant statistics). The three-accent system (`blue`, `emerald`, `amber`) is too narrow for multi-series charts. A categorical palette of 6–8 colors — colorblind-safe — needs to be designed. Defer to Phase 4 when actual chart content appears.
3. **Icon library.** `@nuxt/icon` via Iconify is the mechanism. The specific set (Heroicons vs. Lucide vs. Tabler vs. MDI) isn't locked. Pick one primary set in Phase 1 and stick with it; allow narrow exceptions for brand icons (Facebook, etc.) via the `fa-brands` collection.
4. **Dark/light toggle behavior.** The mockup respects `prefers-color-scheme` on first visit and persists user choice. Decision pending: does the toggle animate smoothly (crossfade), or snap instantly? Smooth is nicer; instant is more robust against FOUC.
