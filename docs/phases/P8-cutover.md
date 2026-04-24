# Phase P8 — Cutover

**Status:** SCAFFOLD v0.1
**Companion to:** `04-PHASED-DELIVERABLE-PLAN.md` §3 (Phase 8), `02-MASTER-DESIGN-PLAN.md` §6.2
**Phase:** P8
**Last updated:** 2026-04-24

---

## 1. Overview

The DNS swap. The new site becomes the production site; the old site is archived at an internal URL for one release cycle. Assumes the Strapi cutover (S9) has already happened — Strapi 5 is canonical before the frontend flips.

## 2. Entry criteria

- P7 exit gate met.
- Strapi S9 exit gate met — v5 is authoritative, v3 is frozen read-only.
- Cutover window agreed with leadership (see `01-EXECUTIVE-SUMMARY.md` §6).

## 3. Scope of work

- A/B traffic split at 10% to `next.icjia.illinois.gov` for one week; Plausible analytics verified against expected patterns.
- All six cutover criteria in `02-MASTER-DESIGN-PLAN.md` §6.2 checked off.
- DNS swap: `icjia.illinois.gov` points at the new site.
- Old site at `legacy.icjia.illinois.gov` for one release cycle post-cutover.
- Rollback plan documented and tested (DNS reverse within 15 minutes).
- Post-cutover monitoring cadence: Plausible, Netlify analytics, Strapi admin logs, error tracking.

## 4. Screens / routes affected

Every public URL on the site — the cutover is site-wide.

**TBD — filled in design round 2:** any cutover-visible surfaces (e.g., launch banner, transition copy) if called for by the design.

## 5. Components needed

No new components, unless a launch banner is part of the design.

**TBD — filled in design round 2:** launch banner or transition copy if required.

## 6. Data requirements

Full production content in Strapi 5 (result of S9). No new data shapes.

## 7. Accessibility gates

All previous gates continue to apply. P8 does not relax them — if a cutover-only component is introduced (e.g., launch banner), it's audited to the same bar as any other component.

## 8. Test checklist

- [ ] A/B split data looks sane for one week.
- [ ] All six `02-MASTER-DESIGN-PLAN.md` §6.2 cutover criteria checked off.
- [ ] DNS swap tested end-to-end in a dry-run.
- [ ] Rollback tested: DNS can reverse within 15 minutes.
- [ ] Post-cutover monitoring dashboards in place: Plausible, Netlify, Strapi logs, error tracking.
- [ ] `legacy.icjia.illinois.gov` serves the archived Vue 2 site.
- [ ] No critical regressions in the first 72 hours after DNS swap.

## 9. Exit gate

The new site is the production site. The old site is archived. No critical regressions in the first 72 hours.

## 10. Design references

**TBD — filled in design round 2:** link any launch-specific design artifacts (banner, transitional copy, announcement).
