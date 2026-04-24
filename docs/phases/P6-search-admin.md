# Phase P6 — Search and admin

**Status:** SCAFFOLD v0.1
**Companion to:** `04-PHASED-DELIVERABLE-PLAN.md` §3 (Phase 6), `07-OPEN-QUESTIONS.md` Q1 and Q7
**Phase:** P6
**Last updated:** 2026-04-24

---

## 1. Overview

Adds the two remaining interactive surfaces — client-side search (Cmd+K modal) and the gated admin SPA — on top of the otherwise-static site. Both depend on decisions in `07-OPEN-QUESTIONS.md`: Q1 (search backend) and Q7 (admin scope).

## 2. Entry criteria

- P5 exit gate met.
- Search backend decision resolved (`07-OPEN-QUESTIONS.md` Q1).
- Admin audit complete (`07-OPEN-QUESTIONS.md` Q7).

## 3. Scope of work

- Build-time `searchIndex.json` generation (or Pagefind index per Q1 resolution).
- Search modal (Cmd+K) with Fuse.js runtime or Pagefind per Q1.
- Admin routes (`/admin/**`) configured as `ssr: false` / SPA mode.
- Admin authentication flow: Strapi auth, token exchange via REST.
- Admin views ported per Q7 audit — anything without an identified current user is retired, not ported.

## 4. Screens / routes affected

- Cmd+K search modal — globally available on the shell trigger from P2.
- `/admin` (SPA root)
- `/admin/*` (per Q7 audit — specific views TBD)

**TBD — filled in design round 2:** confirm the search modal layout, result card design, and admin-view list against the design artifacts.

## 5. Components needed

- `SearchModal`
- `SearchResultCard`
- `SearchEmptyState`
- `AdminShell` (SPA container with its own navigation)
- `AdminAuthGate`
- Admin-view components (set depends on Q7 outcome)

**TBD — filled in design round 2:** final admin-view component list per the Q7 audit and the mockup.

## 6. Data requirements

- Search: build-time index generated from Strapi 5 and `@nuxt/content` sources. Index shape varies by Q1 resolution (Fuse.js needs a flat JSON; Pagefind builds its own binary index).
- Admin: Strapi 5 REST endpoints for authentication; per-view GraphQL queries scoped to the authenticated user's role.

## 7. Accessibility gates

Per `06-ACCESSIBILITY-STRATEGY.md` §2–5:

- Search modal is a Reka UI / Nuxt UI dialog — focus-trapped, Esc-closes, focus returns to the opener.
- Search results list is keyboard-navigable (Up/Down, Enter to open).
- `role="dialog"` and accessible name on the modal.
- Admin SPA: same a11y bar as the public site. Don't ship a pre-a11y admin because it's "internal."

## 8. Test checklist

- [ ] Cmd+K opens the modal; Esc closes it; focus returns to the opener.
- [ ] Search against a known query returns expected results.
- [ ] Empty state renders when no results.
- [ ] Admin login flow works end-to-end with a test account.
- [ ] axe-core clean on search modal and admin views.
- [ ] Keyboard-only pass on the search modal.

## 9. Exit gate

Feature parity on search and gated surfaces. Authors log into admin and complete their documented workflows.

## 10. Design references

**TBD — filled in design round 2:** link the mockup sections for the search modal and any admin-view designs; record Q7 audit outcome with per-view notes.
