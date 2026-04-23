# ICJIA Public Website — Strapi 3 → 5 Upgrade Plan

**Status:** DRAFT v0.1
**Companion to:** `02-MASTER-DESIGN-PLAN.md`, `04-PHASED-DELIVERABLE-PLAN.md`
**Resolves:** `07-OPEN-QUESTIONS.md` Q3 (Strapi upgrade scope and approach)
**Last updated:** 2026-04-23

---

## 1. Context and recommendation

The current production backend is Strapi 3. The new Nuxt app is written against Strapi 5's Document Service API. Some kind of upgrade is unavoidable. The question is *how*.

**Recommendation: stand up a parallel fresh Strapi 5 instance.** Do not upgrade the production database in place through 3 → 4 → 5.

### Why not in-place

The stepwise upgrade is effectively two rewrites on top of live data:

- **3 → 4 is a rewrite.** Strapi 4 introduced a new database schema (components are stored in separate `components_*` tables with polymorphic joins), a new REST response shape (everything wrapped in `{ data, attributes, meta }`), a new plugin API, and the Entity Service layer that replaced direct model access. All custom controllers, services, and lifecycle hooks have to be rewritten. Plugins either have v4 equivalents or have to be replaced.
- **4 → 5 is a second rewrite.** Strapi 5 introduced the Document Service (supersedes the Entity Service), replaced the draft/publish numeric `publishedAt` field with a `status` + `documentId` model, unwrapped REST responses (back to plain objects — reversing the v4 shape), and changed the GraphQL schema. Custom code written against v4's Entity Service is ported again.
- **MongoDB is removed.** If the v3 instance uses MongoDB (not confirmed; see S0), v4 does not support it at all. That forces a database-engine migration as a prerequisite to even starting the v3 → v4 step.
- **Two migration windows, two rollback plans.** Doing both stepwise on production means two scheduled outages, two sets of rollback scripts, and live data that has to survive both conversions.

### Why parallel

- **The v3 instance keeps running.** The existing Vue 2 frontend continues to serve the public site during the rebuild. Nothing in production changes until cutover.
- **The v5 instance is built in peace.** No pressure from live traffic, no data conversion mid-build, no coupling to v3's state.
- **One data migration, one cutover.** A single migration script reads from v3 and writes to v5. Run it repeatedly in dry-run against staging. The final production run happens at cutover, not in two phases months apart.
- **Rollback is trivial.** The v3 instance is never touched. If cutover fails, point DNS back; v3 is still authoritative.

The cost is that we run two Strapi instances (and two databases) for the duration. For a site with modest content volume, this is an inexpensive operating line. For a multi-month rebuild, it is the safer architecture.

### What this plan does not cover

- **Schema redesign.** The migration is intentionally like-for-like: every v3 content type has a v5 equivalent with the same fields. Redesigning the schema is a separate project after v5 is stable.
- **Strapi 6.** Out of scope for v1.
- **Bilingual content fields.** Tracked in `07-OPEN-QUESTIONS.md` Q2. The v5 instance will be built in a way that doesn't preclude adding Spanish content later, but no Spanish fields are added now.

---

## 2. Scope inventory (Phase S0 output)

Before any build work starts, produce a signed inventory of the current v3 instance. The backend owner confirms it matches production; any discrepancy found later in the project comes back to this document.

Audit items:

- **Content types.** Name, collection vs. single type, field list, field types, relations, component references, dynamic zones.
- **Components and dynamic zones.** Every component schema used by any content type, every dynamic zone and its permitted component list.
- **Custom controllers, services, policies, lifecycle hooks.** File-by-file inventory. For each, note what it does and whether it's still needed (some custom code may be solving problems that v5 solves natively).
- **Installed plugins.** Name, version, config, whether a v5 equivalent exists, whether it can be dropped.
- **Uploaded media.** File count, total size, storage provider (local filesystem, S3, other). If local filesystem, note where.
- **Admin users, roles, permissions.** Every account; every role; permissions matrix per content type.
- **Webhooks and scheduled tasks.** External integrations (CI build hooks, third-party services).
- **Environment variables and secrets.** `.env` contents audited; production secrets documented in the team password manager.
- **Database engine and version.** SQLite, Postgres, MySQL, or MongoDB. Version. Note that the v5 target is SQLite regardless (see §3); if v3 is MongoDB, the migration script handles the document-to-relational translation as it writes into v5's SQLite.
- **Current hosting, deployment, and backup process.** Where is the v3 instance deployed, how is it deployed, what does the current backup cadence look like.

Deliverable: a written inventory document, reviewed and signed off by the backend owner. Exit gate for Phase S0.

---

## 3. Target environment

| Component | Target |
|---|---|
| Strapi | 5, latest LTS at project start |
| Node | LTS version aligned with Strapi 5's supported range |
| Database | **SQLite** (committed, regardless of v3 engine) |
| Storage | S3-compatible object store (move off local filesystem if v3 used it) |
| Hosting | Confirmed with backend owner (see `07-OPEN-QUESTIONS.md` Q3 resolution) |

**SQLite is the committed database engine for v5.** ICJIA is an agency site with fewer than 500 hits per day, which is well inside SQLite's comfortable operating range. Strapi 5 supports SQLite as a first-class option (via `better-sqlite3`). The tradeoffs of the alternatives do not apply at this traffic level: Postgres and MySQL would introduce operational overhead (a database server to run, back up, patch, and monitor) without measurable benefit. SQLite keeps the entire CMS in a single file that can be backed up, copied, or restored with standard file-system tools — a good fit for agency IT.

If v3 is on SQLite or MySQL, the data-migration script reads from v3's existing engine and writes into v5's SQLite. MongoDB requires schema translation (document-shaped → relational) in the migration script; flag early in S0 if v3 turns out to be on Mongo, since that shifts complexity into S5.

---

## 4. Phases

Detailed per-phase entry/exit criteria, deliverables, and risk flags are in `04-PHASED-DELIVERABLE-PLAN.md`. Summary:

| Phase | Focus | Exit gate |
|---|---|---|
| **S0** | Inventory the current v3 instance (see §2) | Signed inventory document; backend owner confirms it matches production |
| **S1** | Provision Strapi 5 instance; SQLite database file; CI/CD; env vars | `strapi start` reaches admin login on staging URL |
| **S2** | Rebuild content-type schemas, components, dynamic zones | Every v3 content type has a v5 equivalent; schemas committed to git |
| **S3** | Port custom code (controllers, services, policies, lifecycle hooks) to v5 patterns | Unit tests pass; behaviors match v3 spot-checks |
| **S4** | Media storage configuration; mirror uploads bucket | A v3 upload URL resolves correctly against the v5 instance |
| **S5** | Data-migration script v1; dry-run against staging | Record counts match between v3 and v5; checksum diff under defined threshold |
| **S6** | Users, roles, permissions port (partly manual, partly script-assisted) | Every author account re-created with its original role |
| **S7** | Integration: Nuxt staging app queries v5 via GraphQL | Every content type renders on `next.icjia.illinois.gov` |
| **S8** | Author acceptance testing on staging v5 | Three or more authors sign off on the publish workflow for their content types |
| **S9** | Cutover: final data sync, swap Strapi admin DNS, announce | v5 is canonical; v3 frozen read-only for 30-day retention |

The project's critical path runs through S0 → S1 → S2 → S3 → S5 → S7, and S7 is the gate that unblocks website rebuild Phase 4 (see `04-PHASED-DELIVERABLE-PLAN.md`).

---

## 5. Data migration script

A Node script that reads from v3 via its REST API (with full relations populated) and writes to v5 via the Document Service API.

Requirements:

- **Idempotent.** Re-runnable from scratch against a clean v5. Re-running against a partially-populated v5 either skips or upserts based on a recorded `v3_id` mapping.
- **Dry-run mode.** Runs read-only against v3, writes only to a throwaway staging v5. Used repeatedly during S5.
- **Mapping output.** Produces `v3_id → v5_documentId` mapping file. Required for URL/redirect work in the frontend (some legacy URLs embed numeric IDs).
- **Structured logging.** Every record written produces a log line: content type, v3 id, v5 documentId, relation-count, success/failure. Failures halt with full context, not silent skips.
- **Checksum verification.** After a run, produce per-content-type record counts and sample field checksums; compare against v3 source. Threshold for acceptable diff is defined during S5 and recorded in the test report.
- **Order-aware.** Migrates content types in dependency order: referenced types first, referring types after. Relations are populated in a second pass once all records exist.

Testing: dry-run against staging at least three times across the phase. The final production run happens once, at cutover, from the same tested script.

---

## 6. Media migration

Uploaded media (images, PDFs, attachments) must survive the migration without URL breakage — external sites and printed materials may link to `uploads/some-file-name.pdf`.

Plan:

- **If v3 uses local filesystem:** move to S3-compatible object storage during S4. Do not carry the local-fs pattern into v5. Configure v5's upload provider to point at the new bucket. Copy existing files once, then verify.
- **If v3 already uses S3:** configure v5's upload provider to point at the same bucket and path prefix. No file movement required. Verify a sample of existing URLs resolve correctly when served through the v5 instance.
- **URL structure:** keep the same `/uploads/...` path. Do not rename files during migration.

Media migration is on the non-critical path (parallel to S3) but S4 must complete before S5 so the migration script can validate that image URLs resolve.

---

## 7. Breaking changes cheat sheet

High-impact differences between v3 and v5 that integrators and authors will notice:

| Area | v3 | v5 |
|---|---|---|
| REST response shape | Plain object: `{ id, field, field, ... }` | Plain object: `{ id, documentId, field, field, ... }` — `documentId` is the new primary identifier |
| Draft / publish | `published_at` nullable field on every entry | `status: "draft" \| "published"` + `documentId` (drafts and published versions share a `documentId`) |
| REST filtering | `?_where[...]` + `?_sort=` + `?_limit=` | `?filters[...]` + `?sort=` + `?pagination[...]` |
| REST populate | `?_populate=relation` (implicit on some routes) | `?populate=relation` (explicit; nothing populated by default) |
| GraphQL schema | v3 schema shape | v5 schema shape; queries rewritten |
| Permissions model | Users-permissions plugin controls public and authenticated | Restructured; granular per-content-type, per-operation |
| Plugin config | `config/plugins.js` | `config/plugins.ts` (or `.js`); different shape |
| Admin UI layout | v3 admin | v5 admin — cleaner, different navigation patterns, Document Service-aware |
| Lifecycle hooks | Model lifecycle callbacks | Document Service middlewares and per-content-type lifecycles |
| Custom fields | Limited | First-class custom-field API |

This table is a navigation aid, not a substitute for Strapi's upgrade documentation. Engineers porting custom code should work from Strapi's official migration guides.

---

## 8. Testing strategy

Three gates, all required for cutover:

### 8.1 Data parity (covers S5)

- Record counts per content type match between v3 and v5 after each dry-run.
- Sample field checksums match (e.g., for a 10% random sample of records, hash `title + slug + body` and compare v3 vs. v5).
- Relation counts match: if a v3 post has 3 tags and 1 author, the v5 equivalent has 3 tags and 1 author.
- Media URLs resolve for a sampled set of records.

### 8.2 API contract (covers S7)

- The Nuxt app can successfully query every production content type via GraphQL against v5.
- The Nuxt app can render a page of every archetype (listing, detail, resource, profile) using v5 data.
- No queries return errors; no fields are missing in the response that the frontend expects.
- Response times are within acceptable ranges for build-time querying (seconds, not minutes, for a full-site query pass).

### 8.3 Author acceptance (covers S8)

- At least three authors, representing different content areas (news, grants, research), complete end-to-end publish tasks on v5.
- Tasks include: creating a draft, previewing the draft in the Nuxt staging app, uploading media, editing relations, publishing, unpublishing, editing a published entry, re-publishing.
- Authors sign off in writing. Unresolved usability issues are recorded with severity; blocking issues prevent cutover until resolved.

---

## 9. Rollback plan

Because the v3 instance is never touched during the build, rollback is straightforward:

- **Before cutover:** no rollback needed — v3 continues to serve production.
- **At cutover:** if the cutover fails (data migration surfaces an unexpected error, DNS change fails, v5 authentication breaks), revert the DNS swap. v5 is taken offline; v3 resumes as authoritative. Team investigates the root cause before retrying.
- **After cutover, within 30 days:** v3 instance is retained read-only. If a serious regression surfaces in v5, v3 can be consulted to recover source data. A fresh migration from the retained v3 into a repaired v5 is possible during this window.
- **After 30 days:** v3 is decommissioned. Database dumps and media archives are retained indefinitely in the agency's backup system.

---

## 10. Out of scope for this plan

- Schema redesign. Like-for-like migration first; improvements later.
- Strapi 6 upgrade. Evaluate post-launch.
- Bilingual content field design. Tracked in `07-OPEN-QUESTIONS.md` Q2.
- Changes to the public-facing URL structure. Handled by the Nuxt frontend redirect table, not by Strapi.
- New content types introduced during the rebuild. If genuinely new content types are needed for v5, they are added as separate schema work during S2 and flagged to stakeholders.

---

## Appendix: References

- Strapi migration guides (authoritative): the project team works from Strapi's own v3-to-v4 and v4-to-v5 migration documentation during S2–S5.
- Internal: `02-MASTER-DESIGN-PLAN.md` §1.2 (content source decisions), §4.3 (data access patterns), §4.5 (preview architecture), `04-PHASED-DELIVERABLE-PLAN.md` (full phase detail).
