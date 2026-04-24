# Changelog

All notable changes to the ICJIA public website project are documented here.

This repository currently holds the planning set for the rebuild of icjia.illinois.gov. Entries below track planning-document work. Once implementation begins, code changes will appear here as well.

Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
Versioning: [Semantic Versioning](https://semver.org/spec/v2.0.0.html). The package version follows the master design plan's version (currently in `docs/02-MASTER-DESIGN-PLAN.md`). Pre-1.0 minor bumps reflect planning-set revisions; 1.0 will be cut when the planning set is accepted as the basis for implementation.

---

## [0.7.0] — 2026-04-24

Red-team / blue-team security review of the planning set; new pre-implementation security requirements document recording the must-fix items surfaced by the review.

### Why this revision

The planning set (v0.6.0) has strong architecture — parallel-fresh Strapi migration, preview flow with short-lived signed cookies, WCAG 2.1 AAA on core reading paths. But a systematic security review found that operational security detail (authentication acceptance criteria, incident response, logging, secrets management, backup beyond rollback, vendor compliance) was either deferred to `07-OPEN-QUESTIONS.md` with no concrete criteria or missing entirely. For a state-government public site, these cannot reach implementation unaddressed.

### Added

- `docs/09-SECURITY-REQUIREMENTS.md` — 13 security items, each tagged with severity (CRITICAL / HIGH / IMPORTANT), the implementation phase it blocks, and a resolution requirement. Three CRITICAL items block Phase 0 (scaffold): incident-response owner and escalation path (SEC-01); state-government vendor compliance confirmation for Netlify and DigitalOcean (SEC-02); secrets-management strategy (SEC-03). Five HIGH items block S1, P6, and P8. Five IMPORTANT items are implementation-phase requirements. The document is append-only, same convention as `07-OPEN-QUESTIONS.md` — resolved items retain their original text with a resolution paragraph appended.

### Changed

- `docs/00-README.md` — added row 09 to the status table; added a "Security reviewer" reading path; extended the project-sponsor, backend-engineer, and frontend-engineer paths to include 09.

### Review findings summary (captured in `09-SECURITY-REQUIREMENTS.md`)

**Top 3 blockers:**

1. **Admin authentication under-specified.** Plan defers to `07-OPEN-QUESTIONS.md` Q7 with no acceptance criteria. MFA, rate limiting, session timeout, post-cutover password rotation are not in the plan. Tracked as SEC-06.
2. **Incident response absent.** No named security contact, no escalation path, no out-of-band communication plan. Tracked as SEC-01.
3. **Logging and audit trail absent.** Required for state-government compliance; also required for post-incident forensics. Tracked as SEC-04.

**What was preserved as good.** Accessibility-first architecture (`06-ACCESSIBILITY-STRATEGY.md`), preview-flow design (`02-MASTER-DESIGN-PLAN.md` §4.5 — signed cookies, short-lived, author-session-coupled), and parallel-fresh Strapi migration (`03-STRAPI-UPGRADE-PLAN.md` §1 — reduces operational risk).

### Out of scope (flagged for agency coordination)

Data classification, FOIA coordination, Illinois-specific security standards, record retention — these are handled by agency policy, not the project planning set, but are flagged in §Out-of-scope of the new document.

---

## [0.6.0] — 2026-04-23

Executive summary expanded for non-technical readers; project timeline revised to 6–8 weeks.

### Why this revision

Two changes driven by leadership feedback:

1. The previous executive summary was too brief for readers with no technical background. It used terms like "content management system" and "Netlify" without defining them, referenced other documents for detail, and assumed the reader would know what a "static site" was. Leadership asked for a document that stands entirely on its own — a manager can read it cold and understand the whole project without reading anything else.
2. The previous timeline showed 9–12 months of work, drawn from traditional-rebuild estimates. With AI-assisted development in play, the actual schedule compresses substantially — the repetitive scaffolding (page templates, CMS-to-frontend plumbing, data-migration code) is done in days rather than weeks. Human-paced work (design iteration, content authoring, accessibility review, author UAT) is now the schedule-determining factor.

### Changed

- `docs/01-EXECUTIVE-SUMMARY.md` — rewritten and expanded to v2.0 (approximately 2,500 words, up from ~1,200 in v1.0). Now self-contained: no cross-references to other documents. New §10 adds a plain-language glossary defining every technical term used in the document. Emphasizes the two things non-technical leadership actually cares about: the site will **look better and work faster**. New §6 includes leadership-visible milestones by week.
- `docs/04-PHASED-DELIVERABLE-PLAN.md` §1 Gantt — durations revised from weeks to working days to reflect the compressed schedule. Totals: Nuxt track ≈ 7 working weeks, Strapi track ≈ 3.5 working weeks in parallel, critical path ≈ 6 working weeks. Added explanatory paragraph on why the schedule is compressed (AI-assisted development, `hub-migration-tools` as starting point, completed planning). Expanded "phases with schedule uncertainty" to include P5 and S8 alongside P1 and S3.
- `docs/04-PHASED-DELIVERABLE-PLAN.md` — all remaining "durations illustrative" language updated to reference working days and the 6–8 week total.
- `docs/00-README.md` — document status row for 01-EXECUTIVE-SUMMARY updated to v2.0 with a note that it is now self-contained.

### Schedule rationale

The 6–8 week estimate is realistic because:

- **AI-assisted development** handles the bulk of repetitive scaffolding. The developer reviews and refines rather than writes from scratch.
- **Planning is complete** — no discovery phase. Approved visual direction, decided stack, inventoried content.
- **Fixed scope** — rebuilding what exists, not adding features.
- **`hub-migration-tools`** (see separate GitHub repo) provides the Strapi track with a mature starting point; adaptation cost is content-type handling, not infrastructure.

Pacing items that cannot be compressed: design taste iteration (P1), content-volume work (P5), accessibility review by real humans (P7), and author UAT (S8). The schedule is built around these.

### Not changed

- `02-MASTER-DESIGN-PLAN.md` — no duration language to revise.
- `05-DESIGN-SYSTEM.md`, `06-ACCESSIBILITY-STRATEGY.md`, `07-OPEN-QUESTIONS.md` — not affected.
- The phase structure itself (9 Nuxt phases + 10 Strapi phases, S7 → P4 gate) — unchanged.

---

## [0.5.1] — 2026-04-23

Factual correction: ICJIA's Strapi 3 instance runs on SQLite, not MongoDB. The prior revision treated Mongo as a possible (and therefore plan-for-it) case. Now recorded as known fact; the migration becomes SQLite → SQLite.

### Changed

- `docs/03-STRAPI-UPGRADE-PLAN.md` §1 — removed the "MongoDB is removed at v4" bullet's contingent framing. The v3 engine is known (SQLite), and Strapi 4/5 both still support SQLite, so this is a non-issue for this project. Reworded to keep the general point (compatibility narrows at v4) without implying Mongo is in play.
- `docs/03-STRAPI-UPGRADE-PLAN.md` §2 (S0 inventory) — the "Database engine and version" audit item now states the known answer (SQLite) and the implication (no engine translation needed) rather than listing it as open.
- `docs/03-STRAPI-UPGRADE-PLAN.md` §3 — simplified the post-table paragraph. SQLite → SQLite is a straightforward API-to-API migration; the earlier Mongo-branching complexity is removed.
- `docs/04-PHASED-DELIVERABLE-PLAN.md` S0 risk flag — removed MongoDB as a re-scope trigger; replaced with "larger custom-code surface area than expected" and an explicit note that v3 engine is known.

### Implications

- **S5 (data-migration script) scope reduces.** No document-to-relational reshaping, no ObjectId-to-integer ID mapping, no per-engine extractor logic. The script reads v3 REST and writes v5 Document Service — the underlying SQLite engine is incidental.
- **`hub-migration-tools` applicability improves.** That tool does API-to-API transfer, so extraction is DB-agnostic regardless of v3 engine. Mongo-specific transformations inside the tool become dead code paths when the source is SQLite (harmless but worth auditing during adaptation). The real adaptation cost remains expanding `schemas/` for ICJIA's content types, unchanged by this correction.

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
