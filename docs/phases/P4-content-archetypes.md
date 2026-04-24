# Phase P4 — Core content archetypes

**Status:** SCAFFOLD v0.1
**Companion to:** `04-PHASED-DELIVERABLE-PLAN.md` §3 (Phase 4), `02-MASTER-DESIGN-PLAN.md` §3.3 and §4.5
**Phase:** P4
**Last updated:** 2026-04-24

---

## 1. Overview

Builds the four page archetypes the site relies on — listing, detail, resource, profile — and wires Strapi 5 preview-mode end-to-end for the first time. **This is the phase where the Nuxt track converges with the Strapi track**: P4 cannot start until S7 has opened the gate (Nuxt staging successfully queries Strapi 5 via GraphQL).

## 2. Entry criteria

- P3 exit gate met.
- **Strapi S7 exit gate met** — Nuxt staging app queries v5 successfully via GraphQL.

## 3. Scope of work

- Listing page template (news/index pattern): paginated or filtered list of cards.
- Detail page template (news/[slug] pattern): Markdown body rendering, metadata, related items.
- Resource page template (policies, required-forms): table with download links.
- Profile page template (unit, biography): person/team information layout.
- Preview-mode end-to-end on at least one content type: Strapi "Preview" button → signed URL → Nuxt Function → draft rendered with preview banner → "Leave preview" clears cookie and returns to static site.
- Data composables: `useStrapi` (GraphQL client) and `useContent` (for `@nuxt/content`), with typed query outputs.
- Build-time content validators for heading order, missing alt text, empty H1s (per `06-ACCESSIBILITY-STRATEGY.md` §2).
- One route per archetype live on staging with real Strapi 5 data.

## 4. Screens / routes affected

One representative route per archetype:

- `/news` (listing)
- `/news/[slug]` (detail)
- `/policies` or equivalent (resource)
- `/about/staff/[slug]` or equivalent (profile)

**TBD — filled in design round 2:** confirm the representative route per archetype against the design artifacts; record filter / pagination layouts; list the "related items" variants.

## 5. Components needed

- `ListingLayout`
- `ArticleCard`
- `Pagination` / filter controls (Nuxt UI baseline)
- `DetailLayout`
- `MarkdownRenderer` (wraps `@nuxt/content` / Strapi body rendering)
- `RelatedItems`
- `ResourceTable`
- `DownloadLink`
- `ProfileLayout`
- `ProfileHeader`
- `PreviewBanner` (dismissible; visible only inside preview cookie)

**TBD — filled in design round 2:** final component inventory and the exact listing / filter / pagination patterns per the design.

## 6. Data requirements

Per `02-MASTER-DESIGN-PLAN.md` §1.2:

- Strapi: `news`, `press-release`, `event`, `meeting`, `grant`, `funding-opportunity`, `research-hub-article`, `app`, `dataset`, `biography`, `unit-page`.
- `@nuxt/content`: policies, about pages, navigation config.

GraphQL queries shaped in this phase; used as the starting point for P5.

## 7. Accessibility gates

Per `06-ACCESSIBILITY-STRATEGY.md` §2–5:

- Build-time validators block deploy on: empty `<h1>`, missing alt on images with non-empty captions, heading-order skips.
- Detail pages are WCAG AAA paths: 7:1 body contrast; no justified body text.
- Resource tables have proper `<th>` / `scope` markup.
- Profile pages announce the subject's name as the first heading.
- Preview banner does not break focus order or trap focus.

## 8. Test checklist

- [ ] Each archetype has one live route on staging backed by real Strapi 5 data.
- [ ] Preview-mode flow tested end-to-end with at least one author.
- [ ] Build-time validators fire on a deliberate heading-order violation in a test record.
- [ ] `useStrapi` / `useContent` composables are fully typed.
- [ ] axe-core clean on each representative route.

## 9. Exit gate

One route per archetype renders from real Strapi 5 data. At least one author successfully previews an unpublished draft end-to-end.

## 10. Design references

**TBD — filled in design round 2:** link the mockup sections for each of the four archetypes and any preview-banner design notes.
