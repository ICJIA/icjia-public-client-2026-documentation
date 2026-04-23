# ICJIA Public Website — Master Design Plan

**Status:** DRAFT v0.5
**Target repo:** `icjia-public-nuxt` (new — separate from `icjia-public-client-2021`)
**Author(s):** TBD
**Last updated:** 2026-04-23 (v0.5: planning set renumbered for reading order; Strapi upgrade and phased deliverable plans added as companions; Q3 resolved)
**Companion documents:** `00-README.md`, `01-EXECUTIVE-SUMMARY.md`, `03-STRAPI-UPGRADE-PLAN.md`, `04-PHASED-DELIVERABLE-PLAN.md`, `05-DESIGN-SYSTEM.md`, `06-ACCESSIBILITY-STRATEGY.md`, `07-OPEN-QUESTIONS.md`
**Supersedes (in part):** `docs/NUXT-ARCHITECTURE-RECOMMENDATIONS.md`, `docs/NUXT-REWRITE-PLAN.md` — see [Appendix B](#appendix-b-relationship-to-prior-documents)

---

## Purpose

This document is the orchestration point for the redesign and rebuild of the ICJIA public website. It records the decisions that have been locked, the information architecture, the rendering and data model, the migration and rollout strategy, the high-level phasing, and the success criteria.

Companion documents cover the detail work:

- **`00-README.md`** — planning-set index and reading paths by role
- **`01-EXECUTIVE-SUMMARY.md`** — plain-language summary for non-technical leadership
- **`03-STRAPI-UPGRADE-PLAN.md`** — parallel fresh Strapi 5 instance, phase S0–S9 (resolves Q3)
- **`04-PHASED-DELIVERABLE-PLAN.md`** — per-phase task lists, entry/exit criteria, cross-track dependencies
- **`05-DESIGN-SYSTEM.md`** — visual tokens, typography, geometry, motion, component mapping
- **`06-ACCESSIBILITY-STRATEGY.md`** — a11y targets, automated/manual test gates, architectural approach
- **`07-OPEN-QUESTIONS.md`** — living record of decisions still to make
- **`TEST-PLAN.md`** (forthcoming) — test strategy, coverage, and procedures

The prior rewrite planning in the existing repo (see Appendix B) did a lot of the structural thinking — component inventories, URL structures, Nuxt UI component mapping — and much of that work is preserved. What's new in this iteration: (a) a design-system-first approach rather than a Vuetify-to-Nuxt-UI port; (b) a firm commitment to static generation; (c) a reconciled content-source strategy across Strapi 5 and `@nuxt/content`; (d) first-class live preview for Strapi 5 authors; and (e) a dark-first visual language grounded in the approved mockup.

---

## 1. Locked decisions

These are committed. Revisiting them costs weeks of work and should happen only in response to new information, not preference drift.

### 1.1 Rendering

**Static site generation with selective on-demand revalidation.** The public site is pre-rendered to HTML at build time via Nuxt's `nitro.prerender` pipeline. Content updates in the CMS trigger a Netlify build hook, which regenerates affected routes. Routes that need fresher data than a full rebuild can provide (e.g., the homepage news ribbon) use Netlify's hybrid ISR to revalidate on a schedule without full redeploys. Interactive surfaces — client-side search, the admin interface, any future LAP-request flows — are implemented as islands on top of the static shell, loaded on demand.

This supersedes the SSR approach specified in the prior rewrite plan. The rationale is in `NUXT-ARCHITECTURE-RECOMMENDATIONS.md` and is not relitigated here: a content publication should not boot a framework to render a page.

### 1.2 Content source (hybrid)

**Strapi 5 is the CMS for editor-driven content. `@nuxt/content` is adopted for stable, code-adjacent content.** Strapi 5 is the committed target version; a future upgrade to Strapi 6 is anticipated but not in scope for v1. The new Nuxt app is written against Strapi 5 from day one — no Strapi 3 compatibility is carried forward. Query layer (GraphQL primary, REST for specific cases) is detailed in Section 4.3. The Strapi 3 → 5 upgrade is the subject of `03-STRAPI-UPGRADE-PLAN.md`, which commits to a parallel fresh Strapi 5 instance rather than an in-place stepwise upgrade. Its phases run on a second track; the critical coupling is that Nuxt Phase 4 (content archetypes) gates on Strapi Phase S7 (Nuxt/v5 integration).

Editor-driven content types on Strapi 5 support **live preview of unpublished drafts** rendered by the Nuxt app — see Sections 1.8 and 4.5.

The content split:

| Content type | Source | Rationale |
|---|---|---|
| News posts, press releases | Strapi | High editorial velocity; needs non-technical author workflow |
| Events, meetings | Strapi | Same |
| Grants, funding opportunities | Strapi | Frequent edits; structured relations to programs |
| Research Hub articles, apps, datasets | Strapi | Authors need rich media upload |
| Biographies, unit pages | Strapi | Changes with staffing |
| Policies, required-forms index | `@nuxt/content` | Legally reviewed, low velocity, benefits from git history |
| About pages, unit descriptions, static marketing pages | `@nuxt/content` | Rarely change; review workflow is PR-based |
| Navigation, footer, disclaimers, redirect tables | `@nuxt/content` (YAML/JSON) | Config, not content |

This is a **revisable** decision in the sense that the boundary can shift — specific content types can migrate from Strapi to `@nuxt/content` over time if editor workflows allow. It is **not** revisable in the sense of "let's just use Strapi for everything": the goal is to reduce CMS surface area, not preserve it.

**Validation required before this decision is final:** a conversation with current Strapi authors about which content they actually edit frequently vs. which has been static for 18+ months.

### 1.3 Stack

| Layer | Choice | Notes |
|---|---|---|
| Framework | Nuxt 4.4.x | Not Nuxt 5; not production-stable as of April 2026 |
| CMS | Strapi 5 | GraphQL primary (lightweight client, no Apollo); REST for uploads/auth; draft/publish; preview-capable |
| Component library | Nuxt UI 4.x | Built on Reka UI; Tailwind v4 under the hood |
| Styling | Tailwind CSS v4 | Via Nuxt UI; no custom CSS framework |
| Language | TypeScript (strict) | `typescript.strict: true`, `typeCheck: true` |
| State | Pinia | Auth only; content state in composables |
| Data fetching | `useFetch` / `useAsyncData` + `graphql-request` | No Apollo; see Section 4.3 |
| Content (code-adjacent) | `@nuxt/content` v3 | Markdown with frontmatter |
| Images | `@nuxt/image` | Netlify provider; no Thumbor |
| Icons | `@nuxt/icon` | Iconify; no webfont icons |
| Fonts | `@nuxt/fonts` | Self-hosted, auto-subsetted |
| Utilities | `@vueuse/nuxt` | Replaces most lodash usage |
| Sitemap | `@nuxtjs/sitemap` | Declarative |
| Robots | `@nuxtjs/robots` | Declarative |
| Analytics | Plausible (self-hosted) | No Google Analytics |
| Search | Fuse.js + build-time index | Optional upgrade path to Pagefind |

### 1.4 Browser support

Modern evergreen browsers: last two major versions of Chrome, Firefox, Safari, Edge. **No IE11, no legacy Edge.** The current repo's `public/ie11/index.html` is not ported. Server targets ES2022; no `regenerator-runtime` shipped.

### 1.5 Accessibility target

**WCAG 2.1 AA baseline, AAA for core reading paths.** This is a state-government agency site; the bar is higher than for commercial work. Core reading paths (homepage, news detail, research article, grant detail, about pages) target AAA contrast (7:1 body, 4.5:1 large). All interactive surfaces are keyboard-operable and screen-reader-tested; axe-core runs in CI on every deploy.

Manual testing cadence: VoiceOver (macOS) and NVDA (Windows) on the critical paths every sprint, not just pre-release. The prior codebase's `src/a11y.js` with 24 runtime DOM-fix functions is not ported — the components don't need those fixes because they emit correct markup.

### 1.6 Hosting and rollout

Netlify remains the production host. The new site deploys to `next.icjia.illinois.gov` throughout build. Cutover is atomic (DNS swap) after the cutover criteria in Section 6 are met. The old site is archived but remains viewable at an internal URL for one release cycle post-cutover.

### 1.7 Preview workflow

**Live preview of unpublished Strapi content is a first-class, committed feature.** Multiple agency authors need to see their in-progress edits rendered by the actual Nuxt app before publishing — not just in Strapi's form-based admin view. The "Preview" button in Strapi 5 opens a URL served by the Nuxt app that fetches drafts directly from Strapi and renders them server-side, outside the static build cache. Preview traffic does not flow through the CDN edge; it hits a Netlify Function. See Section 4.5 for the implementation architecture.

This requirement rules out any architecture that would make previewing cumbersome — specifically, it rules out "build a separate preview environment that requires a full Nuxt rebuild to reflect a draft." Preview must be fast enough that authors actually use it: under a few seconds from click to rendered page, not the minutes a rebuild would take.

### 1.8 Out of scope for v1

These are not in scope for the initial redesign launch and should be tracked as post-launch work:

- Bilingual (Spanish) content — structurally accommodated (lang switcher in shell, `hreflang` ready) but not populated
- Personalization / account-based dashboards beyond existing admin
- Native mobile apps
- Content versioning UI beyond what Strapi and git already provide

---

## 2. Design system

The design system — color tokens (dark and light themes), typography, geometry, elevation, motion, and component mapping — is documented in **`05-DESIGN-SYSTEM.md`**.

Summary: dark-first, editorial, deliberately angular. Zero border-radius on primary surfaces as a signature. Three-accent system (blue `#2f6bff`, emerald `#10b981`, amber `#f59e0b`). Inter + JetBrains Mono, no other fonts. Three card variants in the mockup collapse to one production default (`flat`), with `glass` reserved for overlays and `bordered` retired. Motion is short and functional; `prefers-reduced-motion` respected everywhere.

The full token tables, contrast verification, Nuxt UI 4 theme config sketch, and component mapping reference live in `05-DESIGN-SYSTEM.md`. That document is expected to be the most-frequently-referenced file during implementation.

---

## 3. Information architecture

The top-level structure follows what the current site has already validated with users over multiple years, with two adjustments.

### 3.1 Top-level navigation

From the mockup, the primary nav is five items: **About, Research, Grant Resources, Partners, News**. This is a tightening from the current site's deeper structure. Utility links (search, theme toggle, potentially login) live in the nav-actions region on the right.

### 3.2 Content taxonomy

The existing content types are preserved. The URL structure from `NUXT-REWRITE-PLAN.md` Section 4 ("Routing: File-Based") is largely adopted, with these clarifications:

- `researchhub/` is the existing URL convention and is retained for continuity with existing inbound links; it's presented in the UI as "Research."
- `irb/` (Institutional Review Board) is retained as a distinct section.
- `admin/` routes are the only `ssr: false` / island routes in the site.
- The catch-all `[...slug].vue` at the root is reserved for legacy-URL redirects (from `public/_redirects`) and the 404 page.

### 3.3 Page types

Every page falls into one of these archetypes. The page templates are built once per archetype, not once per route.

1. **Landing pages** — homepage, section landings (news, grants, research). Hero + curated content blocks.
2. **Listing pages** — news/index, events/index, meetings/index. Paginated or filtered lists of cards.
3. **Detail pages** — news/[slug], event/[slug], article/[slug]. Single item with Markdown body.
4. **Resource pages** — policies, required-forms. Tables with download links.
5. **Profile pages** — unit/[slug], biography/[slug]. Person or team information.
6. **Utility pages** — search, status, 404, admin. Functional surfaces.

### 3.4 Global shell elements

- **Skip link** — first tab-focusable element; target is `<main>`
- **Site banner** — persistent alert for agency announcements; dismissible, stored in `sessionStorage`
- **Route announcer** — screen-reader-only `aria-live="polite"` region that announces page title on navigation
- **Breadcrumbs** — on all detail and resource pages; never on landings
- **Footer** — four-column structure matching the mockup: agency / resources / engage / legal+meta

---

## 4. Rendering and data architecture

### 4.1 Build pipeline

```
Strapi 5 (published) ─┐
                      ├──→  Nuxt build (SSG)  ──→  Static HTML  ──→  Netlify CDN  ──→  User (instant TTFB)
@nuxt/content        ─┘

Strapi 5 (drafts)  ──────→  Nuxt SSR (Netlify Function, preview cookie required)  ──→  Author (slower, fresh)
```

Every public page is pre-rendered at build time. Build is triggered by:

1. **Netlify build hook** fired by Strapi on publish/update events
2. **Scheduled rebuild** nightly for time-sensitive content (grant deadline freshness)
3. **Manual trigger** for emergency content updates

### 4.2 Hybrid rendering for dynamic surfaces

Five route patterns deviate from pure SSG:

| Route | Mode | Rationale |
|---|---|---|
| `/search` | Client-island | Fuse.js index loaded on demand |
| `/admin/**` | SPA (`ssr: false`) | Auth-gated, no SEO value |
| `/status` | ISR, revalidate 60s | Live status from Strapi health endpoint |
| `/api/**` | Server routes | GraphQL/REST proxy to Strapi 5, form submissions, preview-cookie handling |
| Any route, with preview cookie | SSR, no cache | Author preview of unpublished Strapi drafts — see Section 4.5 |

### 4.3 Data access patterns

Build-time queries to Strapi 5 are issued from `server/` during Nitro's prerender phase.

**GraphQL is the primary query layer,** via Strapi 5's GraphQL plugin. Access is through a lightweight client — `graphql-request` (~4 KB gzipped) or raw `$fetch` with POST bodies. **Apollo is not installed.** REST (via Strapi 5's Document Service API) is used only for surfaces where GraphQL is a poor fit: file uploads, authentication token exchange, and admin-side mutations that benefit from REST's simpler error semantics.

Rationale:

1. **The existing Vue 2 codebase's queries are already GraphQL** — porting is near-mechanical, rewriting them as REST `fields`/`populate` strings is a full migration.
2. **Nested relation queries read cleanly in GraphQL.** A single query for "posts with authors with tags" is one line; the REST equivalent is 60+ characters of `populate[...][...]` nesting.
3. **Build-time query frequency makes runtime overhead concerns moot.** GraphQL introspection and resolver overhead matter per-request; we query per-build.
4. **Typed schema + codegen** (`graphql-codegen`) gives compile-time safety for query shapes without a client-side cache layer.

The prior architecture-recommendations doc's caution was specifically about heavyweight Apollo clients with normalized caches that the site never used — that concern does not apply to light GraphQL at build time.

The composables `useStrapi` (GraphQL, primary) and `useContent` (`@nuxt/content`) are the only sanctioned entry points for page-level data. A narrow `useStrapiRest` exists for the handful of REST-only endpoints. Direct `fetch()` calls from components are forbidden and caught in code review (ideally an ESLint rule enforces it).

### 4.4 Search

Build-time generation of `searchIndex.json` containing all indexed content (titles, slugs, excerpts, tags). Loaded on demand when the user opens the search modal (Cmd+K). Fuse.js runs entirely client-side after the index loads. Investigate Pagefind as a post-v1 replacement — it's purpose-built for static sites and gives better ranking out of the box.

### 4.5 Preview architecture

Authors editing in Strapi need to preview unpublished changes before publishing. Because the site is statically generated, the preview flow deliberately takes a different path than the public site.

**User flow.** An author edits a draft in Strapi 5. The Strapi content-type admin shows a "Preview" button (configured via Strapi 5's preview feature with a URL pattern per content type). Clicking the button opens a signed preview URL that points at the Nuxt app. The Nuxt app validates the signature, sets a short-lived preview cookie, and redirects the author to the canonical content URL (e.g. `/news/my-draft-slug`). The cookie causes that request to bypass the static edge cache and be served by a Netlify Function, which fetches the draft (not the published version) from Strapi and renders it server-side. The author sees exactly what the page will look like when published — layout, images, responsive behavior, the lot — before any publish action.

**Implementation sketch.**

1. A shared preview secret is held by both Strapi and the Nuxt runtime (env var, not committed).
2. Strapi 5 is configured with a per-content-type preview URL pattern, e.g. `https://icjia.illinois.gov/api/preview?contentType=news&documentId={documentId}&token={STRAPI_PREVIEW_SECRET}`.
3. A Nuxt server route (`server/api/preview.get.ts`) validates the token, resolves the `documentId` to a canonical route path, sets a signed HTTP-only preview cookie, and 302-redirects to the canonical path.
4. A server middleware reads the preview cookie on every request. When present, it bypasses the prerendered HTML and forces the route through the SSR path (Netlify serves these via Functions rather than from the edge cache).
5. Data composables (`useStrapi`) consult the preview flag and, when set, request drafts from Strapi 5's GraphQL API with `status: DRAFT`. Otherwise they request `status: PUBLISHED`, which is what the static build also uses.
6. A persistent preview banner renders on any page served in preview mode, with a "Leave preview" button that clears the cookie and returns the author to the static site.

**What this costs.** Preview requests hit Netlify Functions, not the CDN edge. Expect 500ms–2s for warm-function requests and occasional cold-start spikes up to a few seconds for the first preview after idle. Both are acceptable for a handful of authors previewing drafts; neither would be acceptable for public production traffic. That's the tradeoff: previews need draft-freshness, production needs speed. Preview traffic volume is small, so function-invocation cost is negligible.

**What this doesn't do.** It doesn't attempt incremental regeneration for author changes — drafts only appear on production after publish + build hook fires. An author who publishes without preview will still see a 1–3 minute lag before the production site reflects the change. This is intentional; a live-editing CMS against a static site would defeat the architecture.

**Security notes.** The preview token is a shared secret, rotated on a schedule. The preview cookie is signed (not just opaque), short-lived (max 8 hours), and HTTP-only. Preview mode does not expose any content the author couldn't already see in Strapi's admin UI — the Nuxt app proxies the author's existing Strapi permissions; it does not elevate them.

---

## 5. Accessibility strategy

The accessibility approach is documented in **`06-ACCESSIBILITY-STRATEGY.md`**.

Summary: WCAG 2.1 AA as the baseline across the whole site, AAA for core reading paths (homepage, news detail, research article, grant detail, policy page). Accessibility is treated as an architectural property — built into the components (Nuxt UI 4 / Reka UI) and the design tokens, not patched in afterward. The prior codebase's 24 runtime DOM-fix functions are not ported; their role is replaced by (a) components that emit correct markup to begin with, and (b) build-time validators for CMS-authored content. Automated gates (axe-core, Lighthouse CI, HTML validator, link checker) run on every deploy. Manual gates (VoiceOver, NVDA, keyboard-only, 200% zoom, reduced-motion) run per sprint in both themes.

The full test procedures live in `TEST-PLAN.md` (forthcoming); the strategic approach and start-of-project checklist live in `06-ACCESSIBILITY-STRATEGY.md`.

---

## 6. Migration and rollout

### 6.1 Parallel operation

The existing Vue 2 site (`icjia-public-client-2021`) remains in production throughout the rebuild. The new site deploys to `next.icjia.illinois.gov` from week one. Both sites point at the same Strapi instance.

### 6.2 Cutover criteria

The new site goes to production when all of the following are true:

1. All page archetypes from Section 3.3 are implemented and content-complete for production data.
2. All current-site URLs either render correctly on the new site or are handled by `_redirects` entries with no orphans.
3. Automated test suite passes (see `TEST-PLAN.md` exit criteria).
4. Accessibility audit passes on all critical paths in both light and dark modes.
5. Lighthouse mobile ≥95 across five sampled pages.
6. Two full weeks of parallel operation with CMS authors testing the new site against live Strapi data.
7. Plausible analytics on the new site show expected traffic patterns for a 10% A/B split over one week.

### 6.3 Cutover mechanics

DNS swap during a pre-announced low-traffic window. Old site remains at `legacy.icjia.illinois.gov` for one release cycle. `_redirects` entries cover any URL that changed. Rollback path is DNS-reverse within 15 minutes.

### 6.4 Two cutovers, not one

The Strapi upgrade cutover (Strapi Phase S9 in `03-STRAPI-UPGRADE-PLAN.md`) is sequenced with the frontend cutover (Nuxt Phase 8) but is a separate event — **the Strapi cutover happens first, not simultaneously**. At S9, the v5 Strapi instance becomes canonical; the v3 instance is frozen read-only. The existing Vue 2 production site continues to point at the (same, preserved) Strapi admin URL, which is now served by v5. After a few days of stable v5 operation, Phase 8 executes the DNS swap for the public site. This two-step cutover isolates the two risks: a Strapi regression surfaces before the public-site swap; a frontend regression surfaces with a known-stable backend behind it.

---

## 7. Phase outline

This is the coarse outline for the Nuxt rebuild track. Each phase has its detailed entry/exit criteria, deliverables, dependencies, and risk flags in `04-PHASED-DELIVERABLE-PLAN.md`, which also documents the parallel Strapi upgrade track (phases S0–S9) and the cross-track gate between them.

| Phase | Focus | Exit gate |
|---|---|---|
| 0. Decisions and scaffold | Lock this doc; create repo; empty Nuxt 4 app deploys | Skeleton deploys to staging |
| 1. Design system | Tokens, typography, core primitives, theme toggle | Storybook/Histoire page for every primitive |
| 2. Shell and IA | Header, footer, nav, mobile drawer, search modal, skip link, route announcer | Navigable shell, no content pages |
| 3. Homepage | Hero, news ribbon, quick-links tabs, research grid, stats | Homepage live with real data on staging |
| 4. Core content archetypes | Listing, detail, resource, profile templates; preview mode wired end-to-end on one content type | One route per archetype live; author previews drafts successfully |
| 5. Content rollout | All remaining routes filled; preview URLs configured per content type in Strapi 5 | URL coverage matches current site; all editor-driven content types previewable |
| 6. Search and admin | Search index, admin auth, admin UI | Feature parity on gated surfaces |
| 7. Accessibility and polish | A11y audit, visual polish, motion tuning, print styles | All cutover criteria met except A/B duration |
| 8. Cutover | A/B traffic split, observe, swap DNS | Production |

Phases 3–5 are the content-heavy work and will overlap in practice. Phase 1 ("design system") is the hinge — if it's half-done when we start building pages, every page rework cascades.

**Cross-track gate:** Phase 4 cannot start until Strapi Phase S7 (Nuxt/v5 integration) is complete. Phases 0–3 can proceed against mock data and are not blocked by the Strapi track. See `04-PHASED-DELIVERABLE-PLAN.md` §3 for the full dependency diagram.

---

## 8. Success criteria

Measurable targets the finished site must hit, with a source of measurement:

| Metric | Target | Source |
|---|---|---|
| Lighthouse mobile performance | ≥95 | Lighthouse CI, p50 of 5 sampled pages |
| Lighthouse accessibility | 100 | Lighthouse CI, all pages |
| LCP (p75, real users) | <1.5s | Plausible Web Vitals |
| CLS (p75, real users) | <0.05 | Plausible Web Vitals |
| INP (p75, real users) | <200ms | Plausible Web Vitals |
| axe-core violations | 0 | CI on every merge |
| JavaScript bundle (homepage, gzipped) | <100KB | Build report |
| Time-to-first-byte (static route, edge) | <50ms | Netlify analytics |
| CMS publish → visible on prod | <3 minutes | Manual timing, weekly |

Qualitative criteria (review with stakeholders before cutover):

- Content authors can complete their full publish workflow in Strapi without developer assistance.
- A first-time visitor can find "current open funding opportunities" within three clicks from the homepage.
- The site looks deliberate and contemporary in both light and dark modes — not a re-skin.

---

## Appendix A: Open questions

Open questions are tracked in **`07-OPEN-QUESTIONS.md`** as a living document, updated as questions close. That file is the authoritative list; this appendix points to it rather than duplicating the content (which would otherwise drift out of sync).

Q3 (Strapi 3 → 5 upgrade timing) now has a dedicated plan — see `03-STRAPI-UPGRADE-PLAN.md`. The remaining open questions span search backend, preview authentication model, form-submission runtime, admin scope, and several design-system decisions.

## Appendix B: Relationship to prior documents

Two documents in the existing `docs/` folder do much of the groundwork this plan builds on:

**`NUXT-ARCHITECTURE-RECOMMENDATIONS.md`** — the strategic posture. This document's Sections 1.1 (SSG), 1.3 (stack), 2 (design system), and 5 (accessibility) align with it and extend it. Where this plan and that document conflict, this plan wins; the recommendations document is a companion, not a replacement.

**`NUXT-REWRITE-PLAN.md`** — the tactical breakdown. This plan adopts its URL structure (Section 3.2), its Vuetify → Nuxt UI component mapping (Section 2.6), and much of its phase structure (Section 7), but **disagrees on rendering mode** (this plan is SSG-first; the rewrite plan specified SSR) and on the hybrid content-source decision (this plan introduces `@nuxt/content` for stable content). The rewrite plan's 8-phase structure is reorganized in Section 7 here to be design-system-first rather than component-port-first.

The detailed per-phase work in the rewrite plan (Phases 0–7 in that document) is not discarded; it's re-anchored to this plan's phase outline and expanded in `04-PHASED-DELIVERABLE-PLAN.md`.

## Appendix C: Glossary

- **ISR** — Incremental Static Regeneration. Static pages that revalidate on a schedule or on-demand without a full rebuild.
- **Island** — A hydrated interactive component embedded in an otherwise static HTML page.
- **Reka UI** — The headless accessibility-focused primitive library underlying Nuxt UI 4 (successor to Radix Vue).
- **Nitro** — Nuxt's server engine, responsible for SSR, prerender, and edge deployment.
