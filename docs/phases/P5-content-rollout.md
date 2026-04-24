# Phase P5 — Content rollout

**Status:** SCAFFOLD v0.1
**Companion to:** `04-PHASED-DELIVERABLE-PLAN.md` §3 (Phase 5)
**Phase:** P5
**Last updated:** 2026-04-24

---

## 1. Overview

Takes the four archetypes from P4 and rolls them across every content type and every URL on the site — the volume phase. Sitemap, robots, and the redirect table are populated here so cutover in P8 doesn't surface orphan URLs.

## 2. Entry criteria

- P4 exit gate met.
- All content types inventoried in Strapi S0 have a v5 equivalent (S2 exit).

## 3. Scope of work

- Every editor-driven content type (news, events, grants, research articles, datasets, apps, biographies, unit pages) rendered on `next.icjia.illinois.gov`.
- Preview URLs configured in Strapi per content type (configuration stored in the Strapi admin, not in code).
- `@nuxt/content` routes for stable content (policies, about pages, navigation config, redirect tables).
- `public/_redirects` populated for every URL that exists on the current site. No orphan URLs.
- Sitemap (`@nuxtjs/sitemap`) includes every public URL.
- Robots (`@nuxtjs/robots`) correct for `next.icjia.illinois.gov` (noindex until cutover) and for production (indexable).

## 4. Screens / routes affected

Every public URL on the current site. Per-type listing and detail routes for:

- `/news`, `/news/[slug]`
- `/press-releases`, `/press-releases/[slug]`
- `/events`, `/events/[slug]`
- `/meetings`, `/meetings/[slug]`
- `/grants`, `/grants/[slug]`
- `/funding-opportunities`, `/funding-opportunities/[slug]`
- `/researchhub`, `/researchhub/[slug]`
- `/apps`, `/apps/[slug]`
- `/datasets`, `/datasets/[slug]`
- `/about/staff/[slug]` (biographies)
- `/about/units/[slug]` (unit pages)
- `/policies`, `/policies/[slug]` (from `@nuxt/content`)
- Catch-all `[...slug].vue` for legacy redirects and 404.

**TBD — filled in design round 2:** confirm listing layouts and per-type variants; record any per-type hero or banner treatments.

## 5. Components needed

Reuses the P4 archetype components. New:

- `SitemapEntry` helpers (internal to `@nuxtjs/sitemap` config).
- Per-type listing filter controls where the content type has author-facing filter fields.

**TBD — filled in design round 2:** per-type visual treatments that differ from the P4 baseline.

## 6. Data requirements

All Strapi 5 content types per `02-MASTER-DESIGN-PLAN.md` §1.2, plus all `@nuxt/content` collections. This is the phase where the full GraphQL query surface stabilizes.

## 7. Accessibility gates

Per `06-ACCESSIBILITY-STRATEGY.md`:

- Build-time validators run across the full content surface. Severe violations block deploy.
- Contrast audit re-runs on any new component variants introduced for specific content types.

## 8. Test checklist

- [ ] URL coverage matches the current site (inventory produced during P3 used here).
- [ ] Sitemap generated at build time and submitted URL list matches the inventory.
- [ ] `_redirects` covers every legacy URL; no orphans.
- [ ] `next.icjia.illinois.gov` is `noindex`; the redirect target for cutover is ready to flip to `index`.
- [ ] At least one author per content type has previewed an entry through the Strapi preview URL.
- [ ] Build-time validators don't regress from P4 baseline.

## 9. Exit gate

URL coverage matches the current site. Every editor-driven content type is previewable by its authors on `next.icjia.illinois.gov`.

## 10. Design references

**TBD — filled in design round 2:** link the mockup sections for each per-type listing and any content-type-specific treatments.
