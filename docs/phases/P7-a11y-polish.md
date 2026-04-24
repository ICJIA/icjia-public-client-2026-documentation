# Phase P7 — Accessibility and polish

**Status:** SCAFFOLD v0.1
**Companion to:** `04-PHASED-DELIVERABLE-PLAN.md` §3 (Phase 7), `06-ACCESSIBILITY-STRATEGY.md`
**Phase:** P7
**Last updated:** 2026-04-24

---

## 1. Overview

Full accessibility audit, visual polish, and performance tuning across the finished site. Findings often surface changes to the design system; those fixes happen at the token or component level, not per-page.

## 2. Entry criteria

- P6 exit gate met.
- External accessibility reviewer engaged (if using one).

## 3. Scope of work

- Full accessibility audit per `06-ACCESSIBILITY-STRATEGY.md` §3 (automated) and §4 (manual).
- Every audit finding resolved or has a documented acceptance rationale.
- VoiceOver and NVDA passes on all critical paths, in both light and dark modes.
- Keyboard-only pass on every interactive surface.
- 200%-zoom pass on critical reading paths.
- `prefers-reduced-motion` pass.
- Print styles for government content (policies, forms, grant documents).
- Lighthouse mobile ≥95 on five sampled pages.
- Visual polish: spacing consistency, typography rhythm, motion tuning.
- Build-time content validators pass on current production content.

## 4. Screens / routes affected

Every route in the site. Critical reading paths (AAA targets) per `06-ACCESSIBILITY-STRATEGY.md` §1 get the deepest attention: homepage, news detail, research article, grant detail, policy page, about page.

**TBD — filled in design round 2:** finalized list of five Lighthouse sample pages.

## 5. Components needed

No net-new components. Per-component fixes land here — any primitive that fails audit gets fixed in the design-system layer, not wrapped.

**TBD — filled in design round 2:** capture any polish-level component refinements surfaced by the design review.

## 6. Data requirements

Full production content set available in Strapi 5 staging (result of P5 + S5/S7). Build-time validators are run against this data.

## 7. Accessibility gates

This phase **is** the accessibility gate. Per `06-ACCESSIBILITY-STRATEGY.md` §3–5:

- axe-core zero violations across every page.
- Lighthouse accessibility score 100 on every sampled page.
- Link checker clean.
- HTML validator clean.
- CSS contrast linter clean.
- Manual screen-reader, keyboard, zoom, and reduced-motion passes complete.

## 8. Test checklist

- [ ] Full axe-core sweep clean.
- [ ] Lighthouse mobile ≥95 on the five sampled pages.
- [ ] VoiceOver pass recorded on the five critical paths (both themes).
- [ ] NVDA pass recorded on the five critical paths (both themes).
- [ ] Keyboard-only pass on every interactive surface.
- [ ] 200% zoom pass on critical reading paths — no horizontal scroll for body content.
- [ ] `prefers-reduced-motion` honored site-wide.
- [ ] Print styles render cleanly for policies, forms, grant documents.
- [ ] Build-time validators pass on current production content.

## 9. Exit gate

All cutover criteria in `02-MASTER-DESIGN-PLAN.md` §6.2 are met except the two-week parallel-operation criterion.

## 10. Design references

**TBD — filled in design round 2:** link any visual polish reference assets (motion specs, print layouts, typographic rhythm samples).
