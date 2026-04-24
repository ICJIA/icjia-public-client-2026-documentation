# ICJIA Public Website — Open Questions

**Status:** LIVING DOCUMENT
**Companion to:** `02-MASTER-DESIGN-PLAN.md`, `03-STRAPI-UPGRADE-PLAN.md`, `05-DESIGN-SYSTEM.md`, `06-ACCESSIBILITY-STRATEGY.md`
**Purpose:** Track design and architectural questions that are deferred, their deadlines, and their resolutions
**Last updated:** 2026-04-24 (owners assigned: Chris Schweda named for all TBD slots; Q3, Q6, Q7 owner fields specified; Q3 resolution retained)

---

## How to use this document

Questions are added here when they surface during planning or implementation but shouldn't block the current work. Each question has:

- A short title and description
- A target phase for resolution (or "standing" if always-open)
- Who owns the decision (or "TBD")
- Current status: **open**, **in discussion**, **decided**, or **obsolete**

When a question is decided, update the status, add the resolution as a new paragraph under the question, and keep it in this document as a record. Do not delete decided items; decisions get referenced later.

---

## Architectural

### Q1: Search backend

**Status:** open
**Target phase:** post-Phase 2 (prototype evaluation), decide before Phase 6
**Owner:** Chris Schweda (IDS)

Fuse.js is committed for v1 as a known-working client-side option against a build-time index. Pagefind is purpose-built for static sites and gives better ranking, smaller indexes, and faster searches on large corpora. We should prototype Pagefind against current content volume and compare. If it wins, the cutover is isolated (search modal only).

### Q2: Bilingual content readiness

**Status:** open, deferred
**Target phase:** post-v1 project
**Owner:** Agency content leadership

The shell will be built i18n-ready — lang switcher in the nav, `hreflang` in `<head>`, `lang` attributes on content regions — but no actual Spanish content will be populated for v1. The translation workflow, QA process, and editor tooling for bilingual content is its own project. Flagged so v1 doesn't block on it and so the shell doesn't have to be retrofitted later.

### Q3: Strapi 3 → 5 upgrade timing

**Status:** planned (see `03-STRAPI-UPGRADE-PLAN.md`)
**Target phase:** Strapi Phase S7 gates Nuxt Phase 4
**Owner:** Chris Schweda (IDS) — implementer and post-launch maintainer

The new Nuxt app targets Strapi 5's REST Document Service API. The existing production backend is Strapi 3. The upgrade has to precede (or at least reach API-stable status before) Phase 4. If the upgrade slips, the Nuxt frontend sits in Phases 1–3 (design system, shell, homepage) and waits. Stubbed mock data can unblock Phase 3 in a pinch but not Phase 4 and beyond.

**Coordination needed:** confirm with backend owner that Strapi 5 upgrade is planned, scoped, and timelined. If not, escalate.

**Resolution (2026-04-23).** The upgrade approach is now captured in `03-STRAPI-UPGRADE-PLAN.md`. The committed approach is a **parallel fresh Strapi 5 instance**, not an in-place stepwise upgrade through v3 → v4 → v5. A new v5 instance is provisioned alongside v3; schemas, custom code, media, and data are migrated into it via a purpose-built migration script; cutover happens in a single window. The v3 instance remains untouched until the final cutover and is retained read-only for 30 days afterward. The critical coupling remains: Nuxt Phase 4 cannot start until Strapi Phase S7 (integration of the v5 instance with the Nuxt staging app) is complete. What remains open is the *start date* for the Strapi track, which depends on backend-owner commitment and hosting decisions.

### Q4: Edge function vs. Nitro server route for form submissions

**Status:** open, deferred
**Target phase:** whenever the first form (LAP request, status lookup, contact) appears
**Owner:** Chris Schweda (IDS)

Both options work on Netlify. Edge Functions have lower latency globally but fewer Node APIs. Nitro server routes have the full Node runtime but higher latency for users far from the function region. Defer until there's a concrete form with concrete requirements; the right answer depends on what the form does.

### Q5: Preview authentication model

**Status:** open, revisit in Phase 4
**Target phase:** Phase 4 (preview infrastructure)
**Owner:** Chris Schweda (IDS)

`02-MASTER-DESIGN-PLAN.md` §4.5 sketches a shared-secret + signed-cookie flow. An alternative is per-author JWT from Strapi, which would give preview mode per-author audit logging (who previewed what, when). The shared-secret approach is simpler and sufficient for the current trust model. Revisit in Phase 4 if audit requirements tighten — if the agency needs to know which specific author previewed which draft at which time.

---

## Product

### Q6: Hero carousel behavior

**Status:** in discussion
**Target phase:** Phase 3
**Owner:** Chris Schweda (design lead) + communications lead (stakeholder)

The current homepage has an auto-advancing hero carousel. Accessibility best practice leans heavily against auto-advance: it interferes with screen-reader navigation and is bad for low-vision users. The mockup design presents a single-image hero with three image variants (skyline, gradient, map). Proposed for v1: static hero that rotates per page-load (no auto-advance within a session), with optional manual rotation controls. Validate with stakeholders in Phase 3.

### Q7: Admin scope

**Status:** open, audit required
**Target phase:** before Phase 6
**Owner:** Chris Schweda (audit and porting); agency staff confirm which views they actually use

The current admin has views that may be unused (stale dashboards, commented-out analytics, legacy status displays). Before Phase 6, audit every admin view and kill rather than port anything without an identifiable current user. Porting code nobody uses is the single most common time sink in migration projects.

---

## Design system

### Q8: Exact primary blue shade

**Status:** open
**Target phase:** Phase 1
**Owner:** Chris Schweda (IDS)

`#2f6bff` is the mockup value. May be nudged toward Tailwind's `blue-600` (`#2563eb`) for closer alignment with the stock palette. Decide after the Phase 1 contrast audit with real content. See `05-DESIGN-SYSTEM.md` §7.

### Q9: Data-visualization palette

**Status:** open, deferred
**Target phase:** Phase 4 (when chart content appears)
**Owner:** Chris Schweda (IDS)

The three-accent system (`blue`, `emerald`, `amber`) is too narrow for multi-series charts. A categorical palette of 6–8 colors — colorblind-safe, reads on both themes — needs to be designed. Defer to Phase 4.

### Q10: Icon library

**Status:** open
**Target phase:** Phase 1
**Owner:** Chris Schweda (IDS)

`@nuxt/icon` via Iconify is the mechanism. The specific set (Heroicons vs. Lucide vs. Tabler vs. MDI) isn't locked. Pick one primary set in Phase 1 and stick with it; allow narrow exceptions for brand icons (Facebook, etc.) via the `fa-brands` collection.

### Q11: Dark/light toggle behavior

**Status:** open
**Target phase:** Phase 1
**Owner:** Chris Schweda (IDS)

The mockup respects `prefers-color-scheme` on first visit and persists user choice. Pending: does the toggle animate smoothly (crossfade), or snap instantly? Smooth is nicer; instant is more robust against flash-of-unstyled-content on first render. Prototype both in Phase 1.

---

## Resolved

_No resolved items yet. Decisions will be recorded here as questions close, with the resolution and date._
