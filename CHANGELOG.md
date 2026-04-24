# Changelog

All notable changes to the ICJIA public website project are documented here.

This repository currently holds the planning set for the rebuild of icjia.illinois.gov. Entries below track planning-document work. Once implementation begins, code changes will appear here as well.

Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
Versioning: [Semantic Versioning](https://semver.org/spec/v2.0.0.html). Pre-1.0 minor bumps reflect planning-set revisions; 1.0 will be cut when the planning set is accepted as the basis for implementation. Individual documents carry their own frontmatter version reflecting their internal maturity independently of the changelog's package version.

---

## [0.11.0] — 2026-04-24

Executive summary expanded for non-technical managers: new §6 "What the work actually is" — a plain-language, step-by-step walkthrough of the migration process, written to answer the adversarial question *"what, exactly, are you going to do?"* First-draft homepage render embedded in §2. Timeline-rationale bullet in §7 (formerly §6) reworded to attribute development speed to the developer's demonstrable stack experience rather than methodology framing. Same rewording swept across `04-PHASED-DELIVERABLE-PLAN.md`; `09-SECURITY-REQUIREMENTS.md` SEC-13 supply-chain bullet generalized to broaden the control. Three new glossary entries (content type, migration script, parity).

### Why this revision

User feedback simulating an adversarial manager: the previous exec summary had sections on *what the site will look like* (§2), *what changes for residents* (§4), *what changes for staff* (§5), and *when things happen* (§6 timeline). What was missing was the narrative of *what the work actually is* — the step-by-step process an adversarial reader needs in order to quote it back to their own stakeholders. Secondary direction from the user: leadership-facing planning documents should stand on verifiable, project-specific factors (track record, reused tooling like `hub-migration-tools`, completed planning) rather than industry-wide methodology framing that can invite tangential debate the documents cannot resolve here. The planning set was swept for consistency with this principle.

### Added

- `docs/01-EXECUTIVE-SUMMARY.md` §6 — new section titled "What the work actually is" (~900 words). Opens with the sequencing gate: *the database is migrated first; parity with the old site is confirmed; content-type definitions are adjusted if needed; only then does the visible site get built out section by section.* Acknowledges that some design-system and scaffolding work runs in parallel with the database migration (so the timeline tables in §7 do not look contradictory), but emphasizes that no section is lit up with real content until parity passes. Six subsections: (6.1) current site stays up throughout; (6.2) new publishing tool built alongside the old one on fresh infrastructure; (6.3) content migrated, parity verified, content types adjusted if needed — the critical gate; (6.4) visible website built section by section with real content (homepage → news → events → grants → research → about); (6.5) testing with real people (accessibility reviewer + author UAT); (6.6) cutover is a 2-hour event, old site stays on standby for 30 days.
- `docs/01-EXECUTIVE-SUMMARY.md` §2 — first-draft homepage render (PNG from `ui/`) embedded at the top of the section with a dark-mode / light-mode / draft caption. Gives §2's visual claims something concrete to point at. Caption pre-empts three predictable questions: "why so dark?" (answered: system preference respected, light mode exists), accessibility worry (answered: light mode is part of the same design system), and "is this settled?" (answered: first draft, may evolve).
- `docs/01-EXECUTIVE-SUMMARY.md` §11 glossary — three new entries: "Content type," "Migration script," and "Parity (content parity)." These are the artifacts and concepts the new §6 introduces. Each is also glossed inline in the body.

### Changed

- `docs/01-EXECUTIVE-SUMMARY.md` §7 (formerly §6) — third timeline-compression bullet replaced. Previously attributed project speed to industry methodology; now attributes it to the developer's demonstrable track record on the same technology stack (Chris Schweda: 25+ years at ICJIA, 15+ Vue / JavaScript / TypeScript sites, built the current ICJIA site in 2020). Concrete, verifiable, project-specific — stands on the record rather than on framing that can date or invite tangential debate. Corresponding glossary entry removed; the defined term no longer appears anywhere in the document body.
- `docs/01-EXECUTIVE-SUMMARY.md` — sections 6 through 11 renumbered to 7 through 12 to make room for the new §6. Self-reference in the document intro updated from "(§10)" to "(§11)."
- `docs/01-EXECUTIVE-SUMMARY.md` frontmatter — v2.1 → v2.2.
- `docs/04-PHASED-DELIVERABLE-PLAN.md` §2 — third compression-factor bullet reworded with the same developer-stack-familiarity framing used in the exec summary. Keeps the four concrete targets of the work (page templates, CMS integration code, data-migration scaffolding, component implementation); attributes the compression to experience with those patterns on prior projects. Frontmatter v0.1 → v0.2.
- `docs/09-SECURITY-REQUIREMENTS.md` SEC-13 — supply-chain review bullet generalized. Previously scoped to a specific authoring method; now covers every new external package import (any fresh `import` or `require` of a third-party library). This is a **strict broadening of the security control** — all new dependencies now get human review for typosquatting, regardless of how the code was drafted. No weakening of security posture. Frontmatter v0.1 → v0.2.

### Not changed

- Timeline tables in `01-EXECUTIVE-SUMMARY.md` §7 (formerly §6) — day ranges and durations identical. The new §6 describes the same work in narrative form; the tables remain the operational reference.
- Other planning documents (`02-MASTER-DESIGN-PLAN.md`, `03-STRAPI-UPGRADE-PLAN.md`, `05-DESIGN-SYSTEM.md`, `06-ACCESSIBILITY-STRATEGY.md`, `07-OPEN-QUESTIONS.md`, `08-STRAPI-MIGRATION-RUNBOOK.md`) — no references requiring rework in this pass; left untouched.
- Two-track plan content in `04-PHASED-DELIVERABLE-PLAN.md` (phase entry/exit criteria, critical path, risk flags) — unchanged. Only the §2 compression-factor bullet was edited.
- Security practice in `09-SECURITY-REQUIREMENTS.md` — SEC-13's human-review control is now strictly broader in scope, not weaker.
- Historical `CHANGELOG.md` entries (0.10.1 and earlier) — left intact per convention; the changelog is an append-only audit trail of decisions as they were made.

---

## [0.10.1] — 2026-04-24

Presentation-only update in the executive summary: the §6 Mermaid Gantt chart is replaced by two tables (one per track), so non-technical readers can synthesize the schedule without relying on the Gantt visual.

### Why this revision

The executive summary is written for agency directors and program managers (per 0.6.0). User feedback on the Gantt figure: "looks cool, but a table might be easier to synthesize for non-tech managers." Tables also render identically in every Markdown surface — GitHub view, copy/paste into email, printouts, SharePoint previews — whereas the Mermaid Gantt only renders where a Mermaid plugin is active and falls back to a raw fenced code block everywhere else.

### Changed

- `docs/01-EXECUTIVE-SUMMARY.md` §6 — Mermaid `gantt` block replaced with two Markdown tables. Track 1 (Visible website): 9 activities from Setup (days 1–2) through Switch to new site (days 35–36). Track 2 (Publishing tool upgrade): 10 activities from Inventory (day 1) through Switch to new version (day 17). Each row carries working-day range and duration. The one parallel branch in Track 2 (Move images and files runs alongside Move custom features) is called out inline.
- `docs/01-EXECUTIVE-SUMMARY.md` §10 glossary — entry for "Gantt diagram" removed; the term no longer appears in the document.
- `docs/01-EXECUTIVE-SUMMARY.md` frontmatter — v2.0 → v2.1.

### Not changed

- No durations or dependencies changed. Day ranges in the tables are computed directly from the previous Gantt's `after X, Nd` dependency graph; the schedule is identical. Presentation format is the only change. Days are now 1-indexed (Day 1 = first day of work) rather than the Gantt's 0-indexed ticks, which reads more naturally without the axis labels for context.
- No other planning document was touched.

---

## [0.10.0] — 2026-04-24

UI reference assets committed to `ui/`; README includes a visual of the proposed redesign.

### Why this revision

The planning docs have repeatedly referenced an `ICJIA_Redesign_v2_squared.html` mockup as the source of visual truth. Until now that file lived outside the repository, so anyone reading the planning set had to track it down separately. Committing the HTML mockup and its full-page PNG into `ui/` makes the planning set self-contained: visual direction, strategy, and execution all in one place. The README now embeds the PNG so anyone landing on the repo's GitHub page immediately sees what the new site will look like.

### Added

- `ui/ICJIA_Redesign_v2_squared.html` — working HTML mockup of the redesigned site (42 KB). The reference the planning set and every phase doc points at.
- `ui/ICJIA_Redesign_v2_squared_full_page.png` — full-page PNG render of the mockup (880 KB). Quick visual review without opening the HTML; also used as the README's hero image.

### Changed

- `README.md` — added a hero image at the top (the full-page PNG), with caption and alt text. New `## UI references` section documents the contents of `ui/`.
- `docs/05-DESIGN-SYSTEM.md` §Overview — reference to the HTML mockup updated from the old "project knowledge" phrasing to a real relative link into `ui/`.
- `docs/04-PHASED-DELIVERABLE-PLAN.md` P1 entry criteria — mockup HTML reference updated to link `ui/`.
- `docs/phases/P1-design-system.md` entry criteria — mockup HTML and PNG references updated to link `ui/`.
- Filenames normalized from `ICJIA Redesign v2 squared.html` (spaces) to `ICJIA_Redesign_v2_squared.html` (underscores) to match existing references and avoid space-escaping issues in URLs and shell commands.

### Not changed

- Any other planning document. The mockup itself is unchanged; only its location and filename format are.

---

## [0.9.0] — 2026-04-24

Repository renamed; top-level `README.md` and `LICENSE` added.

### Why this revision

The repository was renamed from `icjia-public-client-2026` to `icjia-public-client-2026-documentation` to reflect its actual scope — this repo is planning documentation only; implementation code will live in separate repositories once the planning set is accepted. A top-level `README.md` makes the scope obvious to anyone landing from GitHub; a `LICENSE` file formalizes the MIT terms used across ICJIA's public repos (matches `ICJIA/hub-migration-tools`).

### Added

- `README.md` — project-level README at the repo root. Covers repository scope (planning-only), current status (planning phase), reading paths by role, author (Chris Schweda / IDS), and contributing conventions.
- `LICENSE` — MIT License. Copyright (c) 2026 Illinois Criminal Justice Information Authority (ICJIA). Mirrors the license used on [`ICJIA/hub-migration-tools`](https://github.com/ICJIA/hub-migration-tools).

### Changed

- Repository renamed on GitHub: `icjia-public-client-2026` → `icjia-public-client-2026-documentation`. Local `origin` remote URL updated to match; GitHub auto-redirects old URL references, but canonical links should use the new name.
- `CHANGELOG.md` release-tag URLs at the bottom of the 0.4.0 and 0.5.0 entries updated from the old repo name to the new one.
- `CHANGELOG.md` versioning preamble rewritten — the previous note claimed the changelog version followed `02-MASTER-DESIGN-PLAN.md`, but the two have drifted (master plan is at v0.5; changelog is at 0.9.0). The preamble now accurately describes the convention actually in use: each document carries its own frontmatter version for its internal maturity; the changelog's package version is independent and reflects accumulated planning-set revisions.

### Not changed

- `docs/*` — all planning documents untouched in this pass.
- Author attribution — Chris Schweda remains project lead and sole implementer per 0.8.1.

---

## [0.8.1] — 2026-04-24

Attribution sweep: Chris Schweda (IDS) named as project lead, implementer, post-launch maintainer, and interim security contact across the planning set. Replaces TBDs and generic "we / backend owner / whoever" phrasing in the documents where ownership is now confirmed. No substantive content changes beyond attribution.

### Why this revision

An adversarial review of the executive summary (hostile-manager lens) identified "no people named" as the single largest trust gap in the planning set. The user confirmed that Chris Schweda — 25+ years at ICJIA, 15+ websites across Vue 2/3, JavaScript, TypeScript, and the builder of the current ICJIA site (April–August 2020) — is the sole implementer, project lead, and post-launch maintainer. This revision applies that attribution everywhere it is load-bearing.

### Changed

- `docs/01-EXECUTIVE-SUMMARY.md` — support-contact line and §11 Questions paragraph now name Chris Schweda with credentials. Staff know who to email; managers know who's accountable.
- `docs/02-MASTER-DESIGN-PLAN.md` — `Author` field set from `TBD` to `Chris Schweda (IDS — Innovation and Digital Services, ICJIA)`. Last-updated date refreshed.
- `docs/07-OPEN-QUESTIONS.md` — Owner fields on all seven TBD questions (Q1, Q4, Q5, Q8, Q9, Q10, Q11) set to Chris Schweda (IDS). Q3 owner specified (`Chris Schweda — implementer and post-launch maintainer`); Q6 specified (`Chris Schweda (design lead) + communications lead`); Q7 specified (`Chris (audit and porting); agency staff confirm which views they use`). Last-updated date refreshed.
- `docs/08-STRAPI-MIGRATION-RUNBOOK.md` — §1 audience line names Chris as sole operator; §3.4 exit gate now reads "Signed by Chris Schweda (implementer and backend owner)."
- `docs/09-SECURITY-REQUIREMENTS.md` — SEC-01 updated: Chris Schweda named as interim security contact for the duration of the project. Resolution requirement expanded to require a formal agency security contact designation in addition (can be the same person if the agency prefers), so Chris is not the sole escalation path.

### Not changed

- `03-STRAPI-UPGRADE-PLAN.md`, `04-PHASED-DELIVERABLE-PLAN.md`, `05-DESIGN-SYSTEM.md`, `06-ACCESSIBILITY-STRATEGY.md`, `00-README.md`, and the `docs/phases/` scaffolds — no ownership phrasing that required updating in this pass. The phase docs are intentionally generic on accountability because the accountable person (Chris) is named in 01 and 08.

---

## [0.8.0] — 2026-04-24

Execution-level companions to the strategy set: a full Strapi 3 → 5 migration runbook and a scaffold for per-phase website-build deep dives.

### Why this revision

Planning so far has been strategic — `03-STRAPI-UPGRADE-PLAN.md` tells you *what* the migration does and *why* the parallel-fresh-v5 approach was chosen; `04-PHASED-DELIVERABLE-PLAN.md` tells you *what* each website phase delivers and *what* gates it. The implementation lead now needs the *how*: exact commands, config edits, go/no-go checkpoints for the migration, and a per-phase reference for frontend work as it starts. This revision adds those execution-level docs without editing the strategy set.

### Added

- `docs/08-STRAPI-MIGRATION-RUNBOOK.md` (DRAFT v0.1) — full operational runbook for running the Strapi 3 → Strapi 5 migration via `ICJIA/hub-migration-tools` v4.1.0. Covers pre-migration audit, fork/config of the tool, ICJIA content-type schema additions, local dry-run of Phases 01–06 against a disposable Strapi 5, local sign-off gate, production run on the pre-provisioned DO server, rollback (including pre-migration SQLite + uploads backups), post-migration sign-off, and an appendix of known gotchas (reserved `legacyId`, hardcoded SSH values in `fix-timestamps-remote.js`, `fix-image-refs.js` assumptions, `allowedStatuses` filter).
- `docs/phases/README.md` — index for the new per-phase subfolder; documents the shared template structure and the round-2 fill-in plan.
- `docs/phases/P0-scaffold.md` through `docs/phases/P8-cutover.md` — nine scaffold files, one per Nuxt-track phase. Structure and generic content drawn from `04-PHASED-DELIVERABLE-PLAN.md`, `05-DESIGN-SYSTEM.md`, `06-ACCESSIBILITY-STRATEGY.md`, and `02-MASTER-DESIGN-PLAN.md`. Each file carries explicit `**TBD — filled in design round 2:**` markers where design input is required.

### Changed

- `docs/00-README.md` — added row `08` to the document status table; added a `### Subfolders` section pointing at `docs/phases/`; updated reading paths (Backend/CMS engineer now includes `08`; Frontend engineer now includes `docs/phases/*`).

### Scaffold note

The per-phase docs (`docs/phases/P*.md`) are intentionally incomplete in this revision. Sections for screens/routes, design-specific components, and design references carry `**TBD — filled in design round 2:**` markers so a later `grep` finds every gap. Round 2 — once the HTML design artifacts are shared — fills those sections in and bumps each file from `SCAFFOLD v0.1` to `DRAFT v0.2`.

### Not changed

- `02-MASTER-DESIGN-PLAN.md`, `03-STRAPI-UPGRADE-PLAN.md`, `04-PHASED-DELIVERABLE-PLAN.md`, `05-DESIGN-SYSTEM.md`, `06-ACCESSIBILITY-STRATEGY.md`, `07-OPEN-QUESTIONS.md`, `09-SECURITY-REQUIREMENTS.md`, `01-EXECUTIVE-SUMMARY.md` — the strategy and security-review sets are untouched. The runbook is the operational companion to 03; the phase scaffolds are companions to 04.

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

[0.5.0]: https://github.com/ICJIA/icjia-public-client-2026-documentation/releases/tag/v0.5.0
[0.4.0]: https://github.com/ICJIA/icjia-public-client-2026-documentation/releases/tag/v0.4.0
