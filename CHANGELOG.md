# Changelog

All notable changes to the ICJIA public website project are documented here.

This repository currently holds the planning set for the rebuild of icjia.illinois.gov. Entries below track planning-document work. Once implementation begins, code changes will appear here as well.

Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
Versioning: [Semantic Versioning](https://semver.org/spec/v2.0.0.html). The package version follows the master design plan's version (currently in `docs/02-MASTER-DESIGN-PLAN.md`). Pre-1.0 minor bumps reflect planning-set revisions; 1.0 will be cut when the planning set is accepted as the basis for implementation.

---

## [0.5.0] — 2026-04-23

Planning-set enhancement: added Strapi upgrade plan, phased deliverable plan, and a non-technical executive summary; renumbered documents for explicit reading order.

### Why this revision

Three gaps in v0.4 blocked productive use of the planning set:

- **No Strapi 3 → 5 upgrade plan existed.** `02-MASTER-DESIGN-PLAN.md` treated the upgrade as "a precondition tracked separately," but nothing downstream of Nuxt Phase 4 could start without it.
- **No per-phase deliverable plan existed.** The master plan's Section 7 had a one-line-per-phase outline and repeatedly deferred to `PHASED-DELIVERABLE-PLAN.md` (forthcoming). That document did not exist.
- **The executive summary was too technical for non-technical managers.** It opened with "Nuxt 4 + Nuxt UI 4 + Strapi 5" and cited LCP p75, axe-core, bundle KB, GraphQL, SSG. Leadership deciding whether to fund or sequence this work needs plain language.

Plus: files were not numbered, so the reading order wasn't self-evident to a new reader.

### Approach decisions

Three decisions were made as part of this revision and are now load-bearing for downstream work:

- **Strapi upgrade: parallel fresh v5 instance, not in-place stepwise 3 → 4 → 5.** Stand up a new Strapi 5 instance alongside v3; migrate schemas, custom code, media, and data via a purpose-built migration script; cut over in a single window. The v3 instance is never touched until cutover and is retained read-only for 30 days afterward. Rationale: v3 → v4 is effectively a rewrite (new DB schema, new response shape, plugin API change), v4 → v5 is a second rewrite (Document Service, `documentId`/status draft model), and stepwise means two migration windows with two rollback plans on production. Parallel collapses to one migration script, one cutover, and trivial rollback. See `docs/03-STRAPI-UPGRADE-PLAN.md` §1.
- **Strapi 5 database engine: SQLite.** ICJIA is an agency site with fewer than 500 hits per day, which is well inside SQLite's comfortable operating range. Strapi 5 supports SQLite as a first-class option. Postgres and MySQL would introduce operational overhead — a database server to run, back up, patch, monitor — without measurable benefit at this traffic level. SQLite keeps the entire CMS in a single file that agency IT can back up, copy, or restore with standard file-system tools. See `docs/03-STRAPI-UPGRADE-PLAN.md` §3.
- **Two-track phasing with one cross-track gate.** The Nuxt rebuild (P0–P8) and the Strapi upgrade (S0–S9) run largely in parallel. The single hard coupling: **Nuxt Phase 4 cannot start until Strapi Phase S7 is complete** (Nuxt staging app querying v5 via GraphQL). Nuxt P0–P3 proceed against mock data. The critical path runs through S0 → S1 → S2 → S3 → S5 → S7 → P4 → P5 → P8. See `docs/04-PHASED-DELIVERABLE-PLAN.md` §1–§2.

### Added

- `docs/00-README.md` — planning-set index with reading paths by role (non-technical manager; sponsor/PM; backend engineer; frontend engineer; accessibility reviewer).
- `docs/03-STRAPI-UPGRADE-PLAN.md` — parallel-fresh Strapi 5 upgrade plan. 10 sections covering context and recommendation, scope inventory (S0), target environment, phases S0–S9, data-migration script requirements, media migration, breaking-changes cheat sheet, three-gate testing strategy, rollback, out-of-scope items.
- `docs/04-PHASED-DELIVERABLE-PLAN.md` — per-phase entry/exit criteria, deliverables, dependencies, and risk flags for all 19 phases (9 Nuxt + 10 Strapi). Includes cross-track dependency Gantt and critical-path analysis.
- `CHANGELOG.md` — this file.

### Changed

- `docs/01-EXECUTIVE-SUMMARY.md` — **fully rewritten for a non-technical audience.** Removed all references to SSG/SSR/GraphQL/REST/Lighthouse/LCP/axe-core/Tailwind/Pinia/Vuetify/Apollo/bundle KB. New structure: what we're doing, why now, what changes for authors, what changes for visitors, timeline shape (Gantt), what we need from leadership, risks, how progress is tracked, where to read more. Version bumped to v1.0.
- `docs/02-MASTER-DESIGN-PLAN.md` — revised to v0.5. Companion-documents list updated; §1.2 now points to `03-STRAPI-UPGRADE-PLAN.md` instead of Q3 in Open Questions; new §6.4 documents the two-cutover sequencing (Strapi S9 first, then Nuxt P8); §7 Phase table gains a cross-track gate note; Appendix A shortened.
- `docs/07-OPEN-QUESTIONS.md` — Q3 (Strapi upgrade timing) status changed from `open, blocking Phase 4` to `planned (see 03-STRAPI-UPGRADE-PLAN.md)` with a resolution paragraph. Q5 cross-reference updated to `02-MASTER-DESIGN-PLAN.md §4.5`. Frontmatter updated.
- Files renumbered via `git mv` to establish explicit reading order:
  - `EXECUTIVE-SUMMARY.md` → `01-EXECUTIVE-SUMMARY.md`
  - `MASTER-DESIGN-PLAN.md` → `02-MASTER-DESIGN-PLAN.md`
  - `DESIGN-SYSTEM.md` → `05-DESIGN-SYSTEM.md`
  - `ACCESSIBILITY-STRATEGY.md` → `06-ACCESSIBILITY-STRATEGY.md`
  - `OPEN-QUESTIONS.md` → `07-OPEN-QUESTIONS.md`
- Cross-references throughout `05-DESIGN-SYSTEM.md`, `06-ACCESSIBILITY-STRATEGY.md`, `07-OPEN-QUESTIONS.md`, and `02-MASTER-DESIGN-PLAN.md` updated to the new numbered filenames.

### Conventions established

- **Document numbering.** Planning-set documents use a two-digit numeric prefix that reflects reading order, not chronology. New documents slot in at the numeric position that matches their place in the reading path; renumbering is acceptable and expected when a document's role shifts.
- **Cross-references.** Always use the numbered filename (e.g. `02-MASTER-DESIGN-PLAN.md §4.5`). A rename requires a sweep of all references.
- **Versioning.** The master design plan (currently `02-MASTER-DESIGN-PLAN.md`) carries the authoritative planning-set version. The changelog's package version matches it. Individual documents carry their own document-version in frontmatter that reflects their own maturity independently.
- **Living documents.** `07-OPEN-QUESTIONS.md` is append-only: decided items retain their question and get a resolution paragraph appended. They are not deleted.

### Not changed in this revision

- `docs/05-DESIGN-SYSTEM.md` — content unchanged; only cross-reference filenames updated.
- `docs/06-ACCESSIBILITY-STRATEGY.md` — content unchanged; only cross-reference filenames updated.
- No code changes; this repository is planning-only at this point.
- `TEST-PLAN.md` remains forthcoming and is out of scope for this revision.

---

## [0.4.0] — 2026-04-23

Initial commit of the planning set. This entry retroactively documents the state of the repository before the 0.5.0 revision above.

### Added

- `docs/EXECUTIVE-SUMMARY.md` — one-page technical project overview.
- `docs/MASTER-DESIGN-PLAN.md` v0.4 — orchestration document: locked decisions, information architecture, rendering and data architecture, migration and rollout, phase outline, success criteria.
- `docs/DESIGN-SYSTEM.md` v0.1 — color tokens, typography, geometry, elevation, motion, component mapping.
- `docs/ACCESSIBILITY-STRATEGY.md` v0.1 — compliance targets, architectural approach, CI and manual test gates, start-of-project checklist.
- `docs/OPEN-QUESTIONS.md` — living list of architectural, product, and design-system decisions still pending.

### Known gaps (addressed in 0.5.0)

- No plan for the Strapi 3 → 5 upgrade, despite it being a precondition.
- No per-phase deliverable plan, despite being referenced by the master plan.
- Executive summary written for a technical reader.
- No reading-order signal in filenames.

---

[0.5.0]: https://github.com/ICJIA/icjia-public-client-2026/releases/tag/v0.5.0
[0.4.0]: https://github.com/ICJIA/icjia-public-client-2026/releases/tag/v0.4.0
