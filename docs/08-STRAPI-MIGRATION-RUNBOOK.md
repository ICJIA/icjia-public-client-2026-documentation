# ICJIA Public Website — Strapi 3 → 5 Migration Runbook

**Status:** DRAFT v0.1
**Companion to:** `03-STRAPI-UPGRADE-PLAN.md`, `04-PHASED-DELIVERABLE-PLAN.md`
**Scope:** Operational procedure for running the Strapi 3 → Strapi 5 data migration via the `ICJIA/hub-migration-tools` framework (v4.1.0), from pre-migration audit through production cutover
**Last updated:** 2026-04-24 (named Chris Schweda / IDS as sole operator and S0 inventory signer)

---

## 1. Overview

This document is the **operational companion** to `03-STRAPI-UPGRADE-PLAN.md`. The upgrade plan describes *what* the migration does and *why* a parallel fresh Strapi 5 instance was chosen over an in-place upgrade. This runbook describes *how* to actually run it: exact commands, config fields, and go/no-go checkpoints.

Audience: the developer executing the migration — **Chris Schweda (IDS — Innovation and Digital Services, ICJIA)**. Chris is the sole operator for this migration and the accountable person for fixes after launch. Read this top to bottom before the first dry-run; treat it as a checklist during execution.

Scope boundaries:

- **In scope:** data migration procedure, local dry-run methodology, production run on the pre-provisioned DigitalOcean Strapi 5 server, rollback, post-migration sign-off.
- **Out of scope:** upgrade strategy (in `03-STRAPI-UPGRADE-PLAN.md`), schema redesign, Strapi 6, DO droplet provisioning (already done), frontend cutover (handled in Nuxt track P8).

Source of migration code: [`ICJIA/hub-migration-tools`](https://github.com/ICJIA/hub-migration-tools) v4.1.0. This tool was developed for ResearchHub; we adapt it for the public-site content types.

Source and target database: **SQLite on both sides** (see `03-STRAPI-UPGRADE-PLAN.md` §3).

---

## 2. Prerequisites

### 2.1 Tooling

| Tool | Minimum version | Purpose |
|---|---|---|
| Node | 18 | Running the migration scripts and local Strapi 5 |
| pnpm | 10 | Package manager for hub-migration-tools and Strapi 5 |
| `gh` | any recent | Forking/cloning hub-migration-tools, API introspection |
| `ssh` | any recent | Remote operations on the DO Strapi 5 server |
| `scp` | any recent | File copies to/from the DO server |
| `sqlite3` | 3.x | Inspecting SQLite files, checksums |

### 2.2 Access

- **Strapi 3 GraphQL endpoint** reachable from the operator's machine (read-only access is sufficient).
- **Strapi 3 SQLite database file** readable (for checksums and file-size recording).
- **DigitalOcean Strapi 5 server** reachable via SSH — the server is already provisioned.
- **Strapi 5 admin API token** with full-access scope (created during first admin login).

### 2.3 Artifacts

- Fork or local clone of `ICJIA/hub-migration-tools` (v4.1.0).
- Local Strapi 5 instance used exclusively for dry-run (`npx create-strapi@latest`).
- The signed Strapi 3 inventory document from S0 (see §3 below).

---

## 3. S0 — Pre-migration audit (ICJIA-specific)

This section realizes `03-STRAPI-UPGRADE-PLAN.md` §2 for ICJIA's public-site Strapi 3 instance.

### 3.1 Seed content-type inventory

Start from the content sources table in `02-MASTER-DESIGN-PLAN.md` §1.2. Expected content types (confirm and amend against the actual Strapi 3 schema):

| Content type | Collection / single | Notes |
|---|---|---|
| `news` | collection | News posts |
| `press-release` | collection | Press releases |
| `event` | collection | Events |
| `meeting` | collection | Meetings |
| `grant` | collection | Grants |
| `funding-opportunity` | collection | Funding opportunities |
| `research-hub-article` | collection | Research Hub articles |
| `app` | collection | Apps |
| `dataset` | collection | Datasets |
| `biography` | collection | Staff biographies |
| `unit-page` | collection | Unit / program pages |

Add or remove entries based on what actually exists. Any type in Strapi 3 not listed above becomes an explicit keep / drop decision recorded in the inventory document.

### 3.2 Introspect the Strapi 3 GraphQL schema

```bash
# Save the full schema for reference
curl -s -X POST "$STRAPI3_GRAPHQL_URL" \
  -H "Content-Type: application/json" \
  -d '{"query":"{ __schema { types { name fields { name type { name kind ofType { name kind } } } } } }"}' \
  | jq > strapi3-schema.json
```

For each content type, record: field list, field types, relations (source and target), component references, dynamic zones.

### 3.3 Record the Strapi 3 SQLite baseline

```bash
# Checksum and size of the v3 database file
sha256sum /path/to/strapi3/data.db | tee strapi3-db-checksum.txt
ls -l /path/to/strapi3/data.db

# Record counts per content type
sqlite3 /path/to/strapi3/data.db "SELECT name FROM sqlite_master WHERE type='table' ORDER BY name;"
# Then, for each content-type table (e.g., 'news'):
sqlite3 /path/to/strapi3/data.db "SELECT COUNT(*) FROM news;"
```

Save the record-count output as `strapi3-record-counts.txt`.

### 3.4 Exit gate

A signed inventory document (markdown or PDF), committed to the repo, that includes: the content-type table above (confirmed), field-level schema per type, relation and component references, custom-code inventory per `03-STRAPI-UPGRADE-PLAN.md` §2, media summary, user/role matrix, webhooks, env-var list, SQLite checksum, record counts. Signed by Chris Schweda (implementer and backend owner).

---

## 4. Fork and configure `hub-migration-tools`

### 4.1 Fork or clone

```bash
gh repo fork ICJIA/hub-migration-tools --clone=true --remote=true
# or: git clone https://github.com/ICJIA/hub-migration-tools.git
cd hub-migration-tools
```

Update `package.json` `name` to something specific (e.g., `icjia-public-site-migration`) so the fork is clearly scoped and its lockfiles don't collide.

### 4.2 Configure `config.dev.js` and `config.prod.js`

These two files are the primary configuration surface. Fields to set:

| Field | Meaning |
|---|---|
| `strapi3.graphqlUrl` | Strapi 3 GraphQL endpoint, e.g. `https://icjia.illinois.gov/graphql` |
| `strapi3.apiUrl` | Strapi 3 REST base, e.g. `https://icjia.illinois.gov` |
| `strapi5.graphqlUrl` | Strapi 5 GraphQL endpoint (local for dev, DO URL for prod) |
| `strapi5.apiUrl` | Strapi 5 REST base |
| `strapi5.token` | Full-access API token from Strapi 5 admin |
| `strapi5.dbPath` | Absolute path to the Strapi 5 SQLite file (e.g. `/home/forge/icjia-strapi5/.tmp/data.db`) |
| `contentTypes` | Array of content-type identifiers (see §3.1) |
| `allowedStatuses` | `['published', 'archived']` — confirm drafts don't need migrating during S0 |

### 4.3 Environment variables

Set these in the shell or in a `.env` file loaded by the tool:

```
STRAPI3_GRAPHQL_URL=https://icjia.illinois.gov/graphql
STRAPI3_API_URL=https://icjia.illinois.gov
STRAPI5_GRAPHQL_URL=http://localhost:1337/graphql         # dev; DO URL for prod
STRAPI5_API_URL=http://localhost:1337                     # dev; DO URL for prod
STRAPI5_TOKEN=<full-access token from Strapi 5 admin>
STRAPI5_DB_PATH=/absolute/path/to/strapi5/.tmp/data.db
MIGRATION_ENV=dev                                         # or 'prod'
```

---

## 5. Add ICJIA content-type schemas

### 5.1 Schema files

For each confirmed content type, create `schemas/{type}.settings.json`. Template:

```json
{
  "kind": "collectionType",
  "collectionName": "news",
  "info": {
    "singularName": "news",
    "pluralName": "news-items",
    "displayName": "News"
  },
  "options": { "draftAndPublish": true },
  "attributes": {
    "title": { "type": "string", "required": true },
    "slug": { "type": "uid", "targetField": "title", "required": true },
    "body": { "type": "richtext" },
    "publishedDate": { "type": "datetime" }
  }
}
```

One file per content type. `legacyId` is **not** defined — the tool adds it automatically (see §11 gotcha 1).

### 5.2 Extend the extractor

`migration/scripts/02-extract.js` holds a GraphQL query per content type. Add a query for every ICJIA type. Each query should populate all relations and components to the depth needed for faithful reconstruction.

Call `extractContent(type, query)` for each type in the orchestrator loop.

### 5.3 Update the hardcoded content-type arrays

Several scripts enumerate content types inline. Every one of these must be updated to include ICJIA's types (and drop any ResearchHub-specific types like `article`, `dataset`, `app` if they conflict with ICJIA semantics):

- `migration/scripts/01a-introspect.js`
- `migration/scripts/01b-generate-schemas.js`
- `migration/scripts/04-run-phase.js`
- `migration/scripts/04b-link-relations.js`
- `migration/scripts/05-validate.js`
- `migration/scripts/06-audit.js`

Find the array that looks like `['article', 'dataset', 'app']` (or similar) in each file and replace it with the ICJIA content-type list.

### 5.4 Field type map

`migration/config/field-type-map.json` maps Strapi 3 field types to Strapi 5 equivalents. Add or adjust entries for any ICJIA-specific media or upload field shapes that aren't already covered.

---

## 6. Local dry-run — Phases 01–06

The whole point of local dry-run is to shake out schema and extraction bugs against a disposable Strapi 5 before touching the production DO server.

### 6.1 Stand up a local Strapi 5 instance

```bash
npx create-strapi@latest icjia-strapi5-local --quickstart --no-run
cd icjia-strapi5-local
pnpm add @strapi/plugin-graphql
pnpm develop
```

On first boot, create an admin user and then generate a **full-access** API token under *Settings → API Tokens → Create new API Token*. Copy the token into `STRAPI5_TOKEN`.

Point the migration config at this local instance and at the **read-only** production Strapi 3. Confirm `MIGRATION_ENV=dev`.

### 6.2 Walk the phases

For each `pnpm migrate:phaseNN`:

#### Phase 01 — Schema setup

- **What:** runs `01-run-phase.js`, which delegates to `01a-introspect.js` → `01b-generate-schemas.js` → `01c-verify-schemas.js`. Reads Strapi 3's schema via GraphQL introspection, generates Strapi 5 schema files, verifies shape.
- **Outputs:** `schemas/*.settings.json` (or confirmation they match existing files), introspection JSON dumps in `migration/output/`.
- **Failure modes:** missing GraphQL access, unrecognized Strapi 3 field type, schema mismatch with the files created in §5.1.
- **Idempotent:** yes.
- **Exit question:** did every ICJIA content type generate a valid v5 schema?

#### Phase 02 — Extract

- **What:** runs `02-extract.js` against Strapi 3 GraphQL. Pulls all records, fully populated, for every content type in `contentTypes`.
- **Outputs:** `migration/output/extracted/{type}.json` per content type.
- **Failure modes:** rate-limit / timeout against production Strapi 3 (run during low-traffic window), GraphQL query depth errors, missing relations in the query.
- **Idempotent:** yes — reruns overwrite.
- **Exit question:** do the per-type JSON record counts match the `strapi3-record-counts.txt` from §3.3 (given `allowedStatuses` filter)?

#### Phase 03 — Media

- **What:** runs `03-run-phase.js`. Scans extracted records for media references, uploads files to Strapi 5, transforms content bodies to reference the new media IDs.
- **Outputs:** `migration/output/media-map.json` (Strapi 3 media ID → Strapi 5 media document).
- **Failure modes:** media file 404 against Strapi 3, upload provider misconfiguration on Strapi 5, content-body transformation misses a reference shape.
- **Idempotent:** yes — already-uploaded files are matched and skipped.
- **Exit question:** does every media reference in the extracted content resolve to a Strapi 5 upload?

#### Phase 04 — Write + link + fix

- **What:** runs `04-run-phase.js` (write records), then `04b-link-relations.js` (second-pass relation linking), then `04c-fix-timestamps-remote.js` and `04d-fix-image-refs.js` as needed.
- **Outputs:** records in Strapi 5; `migration/output/legacyId-map.json` (Strapi 3 id → Strapi 5 `documentId`).
- **Failure modes:** relation target missing (usually means write order is wrong), timestamp drift, orphan image refs.
- **Idempotent:** yes — upserts by `legacyId`.
- **Exit question:** are all records in Strapi 5 with their relations correctly linked to other migrated records?

#### Phase 05 — Validate

- **What:** runs `05-validate.js`. Ten-check validation: counts, schema parity, relation integrity, required fields present, timestamps sane, etc.
- **Outputs:** `migration/output/validation-report.json`.
- **Failure modes:** any of the ten checks fails.
- **Idempotent:** yes — read-only.
- **Exit question:** do all ten validation checks pass (or are failures within the diff threshold agreed during S5 per `03-STRAPI-UPGRADE-PLAN.md` §5)?

#### Phase 06 — Audit

- **What:** runs `06-audit.js`. Field-by-field parity audit: samples records and hashes `title + slug + body` (or equivalent key fields) on both sides, compares.
- **Outputs:** `migration/output/audit-report.html` (stakeholder-facing) and `audit-report.json`.
- **Failure modes:** unexpected ERROR categories (e.g., field content differs non-trivially between v3 and v5).
- **Idempotent:** yes — read-only.
- **Exit question:** is the audit report clean of unexpected ERROR categories?

### 6.3 Utilities used during dry-run

- `set-strapi5.js` — swaps the active Strapi 5 target (dev vs prod) by rewriting `config.js`.
- `reset-strapi5.js` — clears the local Strapi 5 SQLite DB (destructive; use between dry-runs to get a clean slate).
- `fix-tables.js` — markdown table cleanup across migrated content. Run with `--dry-run` first.

---

## 7. Local sign-off gate

Before the production run, all of the following must be true:

- [ ] Record counts match between Strapi 3 source and Strapi 5 local target (§6.2 Phase 05).
- [ ] Hand-reviewed sample: pull three records per content type from the local Strapi 5 admin and compare against the Strapi 3 originals.
- [ ] `pnpm migrate:phase06` audit report clean of unexpected ERROR categories.
- [ ] `pnpm fix-tables --dry-run` reports either no issues or a tractable, known list of issues.

Record the sign-off in the S0 inventory document (new section, dated).

---

## 8. Production run on DO (server already provisioned)

### 8.1 Switch config to production

```bash
cp config.prod.js config.js
# or: pnpm set-strapi5 prod
```

Set the production env vars (see §4.3) — `STRAPI5_GRAPHQL_URL`, `STRAPI5_API_URL`, `STRAPI5_TOKEN` now point at the DO server. `MIGRATION_ENV=prod`.

### 8.2 Reset the production Strapi 5 (destructive)

Only run once, and only after confirming that nothing in the DO Strapi 5 has authored data that matters:

```bash
# DESTRUCTIVE — wipes the Strapi 5 database on DO.
# Requires interactive confirmation; review the script before running.
pnpm migrate:reset
```

Create a production admin user and generate a production API token; update `STRAPI5_TOKEN`.

### 8.3 Run the full migration

```bash
pnpm migrate:full
```

This chains phases 01 through 06 against the DO Strapi 5. Watch for failures and halt at the first unexpected ERROR.

### 8.4 Remote SSH details for `04c-fix-timestamps-remote.js`

The script has hardcoded SSH defaults pointing at ResearchHub's server:

- Host: `137.184.64.249`
- User: `forge`
- Strapi directory: `/home/forge/v2.hub.icjia-api.cloud/v2hub`

Override via environment variables or edit the script to match the ICJIA DO server:

```bash
REMOTE_HOST=<icjia-do-host> \
REMOTE_USER=<icjia-do-user> \
REMOTE_STRAPI_DIR=<icjia-strapi5-path> \
  node migration/scripts/04c-fix-timestamps-remote.js
```

### 8.5 Post-migration table cleanup

If §6.2 Phase 05 or `pnpm fix-tables --dry-run` surfaced markdown-table issues that were deferred to production:

```bash
pnpm fix-tables --fix
```

Re-run the audit (`pnpm migrate:phase06`) to confirm the fixes didn't introduce regressions.

---

## 9. Rollback

### 9.1 Pre-migration backups

Before the `pnpm migrate:reset` in §8.2, on the DO server:

```bash
# Back up the Strapi 5 SQLite file (path depends on Strapi 5 version).
# Strapi 5 default is .tmp/data.db on quickstart; production configs often use data.db.
cp /path/to/strapi5/.tmp/data.db /backups/strapi5-data-pre-migration.db
# Back up the uploads directory
tar czf /backups/strapi5-uploads-pre-migration.tgz /path/to/strapi5/public/uploads
```

### 9.2 Rollback procedure if cutover fails

1. Stop the Strapi 5 process on DO (`pm2 stop strapi5` or equivalent).
2. Restore the SQLite backup in place of the migrated database file.
3. Restore the uploads archive.
4. Restart Strapi 5 via PM2.
5. Strapi 3 remains authoritative — the Vue 2 production site continues to serve traffic unaffected, because nothing about the public site changed yet.

### 9.3 Strapi 3 retention

Per `03-STRAPI-UPGRADE-PLAN.md` §9: Strapi 3 is kept read-only for **30 days** after public cutover. During this window, a fresh re-migration from Strapi 3 into a repaired Strapi 5 is possible if a serious regression surfaces. After 30 days, Strapi 3 is decommissioned; database dumps and media archives are retained indefinitely in the agency backup system.

---

## 10. Post-migration sign-off

### 10.1 Manual QA (carried from hub-migration-tools Phase 5)

Using the validation report's checklist:

- [ ] Content lists load in the Strapi 5 admin for every content type.
- [ ] Individual record edit screens render without errors.
- [ ] Relations are editable from the admin.
- [ ] Media previews render in the admin and on public endpoints.

### 10.2 Author UAT (S8 trigger)

Once the manual QA is clean, trigger the S8 phase from `04-PHASED-DELIVERABLE-PLAN.md`: three or more authors, representing different content areas, complete the end-to-end publish workflow (draft → preview → upload media → edit relations → publish → unpublish → edit published → republish). Written sign-off from each.

### 10.3 Decommission schedule

Decommission of Strapi 3 is scheduled for **30 days after public cutover** (P8 exit gate met). Until then, Strapi 3 is read-only on its existing host.

---

## 11. Appendix — Known gotchas

1. **Reserved `legacyId` field.** `hub-migration-tools` adds a `legacyId` attribute to every migrated schema, used to map Strapi 3 numeric IDs to Strapi 5 `documentId`s. ICJIA schemas in §5.1 must **not** pre-define `legacyId` — doing so breaks the upsert logic.
2. **`fix-timestamps-remote.js` hardcoded SSH values.** The script ships pointing at the ResearchHub server (`137.184.64.249`, user `forge`, dir `/home/forge/v2.hub.icjia-api.cloud/v2hub`). Either export `REMOTE_HOST` / `REMOTE_USER` / `REMOTE_STRAPI_DIR` at invocation time (preferred) or edit the script's defaults before running.
3. **`fix-image-refs.js` assumes an `images` JSON field.** Written for ResearchHub's article schema, which has an `images` JSON column. If ICJIA's `research-hub-article` (or equivalent) doesn't have this exact shape, the fix-refs step silently no-ops or errors. Verify during §5.2 and either add the field, adapt the script, or skip it.
4. **Strapi 3 status filter.** The tool defaults `allowedStatuses` to `['published', 'archived']`. If ICJIA has meaningful draft content that must come along, extend this list during S0 and re-run Phase 02.

---

## Appendix: References

- `03-STRAPI-UPGRADE-PLAN.md` — strategy document (what and why).
- `04-PHASED-DELIVERABLE-PLAN.md` §4 — Strapi track phases S0–S9; this runbook covers S2/S3/S5 execution + the cutover within S9.
- `02-MASTER-DESIGN-PLAN.md` §1.2 — content source boundaries.
- [`ICJIA/hub-migration-tools`](https://github.com/ICJIA/hub-migration-tools) — external repo; v4.1.0 is the tested baseline.
