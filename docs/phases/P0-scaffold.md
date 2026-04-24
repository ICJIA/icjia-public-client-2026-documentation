# Phase P0 — Decisions and scaffold

**Status:** SCAFFOLD v0.1
**Companion to:** `04-PHASED-DELIVERABLE-PLAN.md` §3 (Phase 0)
**Phase:** P0
**Last updated:** 2026-04-24

---

## 1. Overview

Initializes the new repo, stands up the toolchain, and deploys an empty Nuxt 4 app to `next.icjia.illinois.gov`. No visual work yet; the goal is that the pipeline is green from day one so every subsequent phase deploys automatically.

## 2. Entry criteria

- `02-MASTER-DESIGN-PLAN.md` signed off at v1.0.
- Git repository `icjia-public-nuxt` (or agreed name) created.
- Hosting account (Netlify) provisioned with a staging environment.

## 3. Scope of work

- Initialize Nuxt 4.4.x with TypeScript strict, Nuxt UI 4.x, Tailwind v4.
- Commit lockfiles.
- `nuxt.config.ts` with base SSG configuration, route rules, prerender settings.
- Netlify deployment: preview deploys per PR; production target `next.icjia.illinois.gov`.
- ESLint + Prettier configured; `typescript.strict: true`, `typescript.typeCheck: true`.
- CI skeleton: install, typecheck, lint, build, deploy preview.
- Environment-variable convention documented in the repo `README.md`.

## 4. Screens / routes affected

**TBD — filled in design round 2:** no user-facing screens in P0, but round 2 records which placeholder routes land here vs. P2.

## 5. Components needed

No components in P0 — scaffold only.

**TBD — filled in design round 2:** confirm whether a single placeholder page is in scope or deferred to P2.

## 6. Data requirements

None. Empty Nuxt app with no data sources wired up.

## 7. Accessibility gates

CI scaffolding for automated gates (`06-ACCESSIBILITY-STRATEGY.md` §3) lands here even though there's no content yet to test:

- axe-core job configured (will run from P2 onward).
- Lighthouse CI configured with the 100-accessibility budget.
- Link checker and HTML validator jobs configured.

## 8. Test checklist

- [ ] `pnpm install` succeeds cleanly.
- [ ] `pnpm typecheck` passes.
- [ ] `pnpm lint` passes.
- [ ] `pnpm build` produces a static output.
- [ ] Netlify preview deploy succeeds on a PR.
- [ ] Production Netlify deploy to `next.icjia.illinois.gov` succeeds.
- [ ] `next.icjia.illinois.gov/` loads in under 200ms (throttled mobile).

## 9. Exit gate

An empty Nuxt 4 app deploys successfully to `next.icjia.illinois.gov`. Clicking a placeholder page loads in under 200ms.

## 10. Design references

**TBD — filled in design round 2:** link to any design-system starter references or baseline mockups consulted here.
