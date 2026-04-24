# ICJIA Public Website — Pre-Implementation Security Requirements

**Status:** DRAFT v0.2
**Companion to:** `02-MASTER-DESIGN-PLAN.md`, `03-STRAPI-UPGRADE-PLAN.md`, `04-PHASED-DELIVERABLE-PLAN.md`, `07-OPEN-QUESTIONS.md`
**Scope:** Security work that must be resolved or specified before implementation begins, organized by the phase it blocks.
**Last updated:** 2026-04-24 (SEC-13 supply-chain bullet generalized from a specific-authoring-method scope to cover all new external package imports; strict broadening of the control, no weakening)

---

## Overview

A red-team / blue-team review of the planning set (v0.6.0) surfaced security work that is under-specified or missing in the strategy documents. The planning set is architecturally sound — accessibility, parallel-fresh Strapi migration, the preview flow, and rollback are well-designed. The gap is operational security: authentication detail, logging, incident response, secrets management, backup beyond rollback, and vendor compliance. This is standard for state-government work but must not reach implementation unaddressed.

This document is the punch list. Each item has a severity, the phase it blocks, and a resolution requirement. Items move through `OPEN` → `IN PROGRESS` → `RESOLVED` as they are addressed. Resolved items retain their resolution paragraph as a record — the file is append-only (same convention as `07-OPEN-QUESTIONS.md`).

## Severity definitions

- **CRITICAL** — must be resolved before any code is written. Implementation cannot begin.
- **HIGH** — must be resolved before the blocking phase begins. Earlier phases can proceed; the blocking phase cannot.
- **IMPORTANT** — should be resolved before the blocking phase. Not an absolute hard blocker; implementation may proceed with a written risk acceptance on file.

## How to use this document

- Every implementation phase in `04-PHASED-DELIVERABLE-PLAN.md` has a corresponding section below listing the security items that gate it.
- Before declaring a phase's entry criteria met, check the items here — both those that block the phase directly and any earlier unresolved CRITICAL items.
- This document is reviewed at every weekly status update until all items are resolved or explicitly risk-accepted.

---

## Block Phase 0 (Scaffold) — CRITICAL

These must be resolved before the repository is initialized or any code is written. They apply to the project as a whole, not a specific phase.

### SEC-01: Incident-response owner and escalation path

**Status:** OPEN (interim contact: Chris Schweda)
**Severity:** CRITICAL
**Blocks:** P0

A state-government public website undergoing a rebuild with two live backends during the transition has nontrivial operational risk. **Chris Schweda (IDS) is the interim security contact** — as project lead and accountable implementer, Chris is the immediate receiver for any suspected compromise during the project. This is sufficient for day-to-day development, but the agency should still designate a formal security contact (internal staff or external consultant) with explicit authority to take the site offline during an incident, so that Chris is not the sole escalation path.

**Resolution required:**

- Confirm Chris Schweda as the interim security contact for the duration of the project (operational today).
- Designate a formal agency security contact — internal staff or an external consultant — with explicit authority to take the site offline during an incident. This can be the same person as Chris if the agency prefers, but it must be a documented designation with escalation authority, not an informal default.
- Document the escalation path: who is called first (Chris), who is called second (formal security contact), who has decision authority at each level.
- Document out-of-band contact methods (phone, personal email) in case agency email is compromised.
- Store the incident-response plan in a location accessible without the agency network in case of outage.

### SEC-02: State-government vendor compliance confirmation

**Status:** OPEN
**Severity:** CRITICAL
**Blocks:** P0, S1

The plan commits to Netlify (production hosting) and a DigitalOcean droplet (Strapi 5 host) without confirmation that these vendors meet Illinois state-government requirements for data residency, encryption at rest, and third-party compliance attestations (SOC 2, FedRAMP) where applicable.

**Resolution required:**

- Confirm with agency IT or procurement that Netlify is an approved vendor for agency production workloads. Specifically: US-based data residency, TLS 1.2+ in transit, encryption at rest, SOC 2 Type II (or equivalent) attestation on file.
- Same confirmation for the DigitalOcean Strapi host.
- If either vendor is not approved, identify an approved alternative before P0 begins.

### SEC-03: Secrets-management strategy

**Status:** OPEN
**Severity:** CRITICAL
**Blocks:** P0, S1

The plan references runtime secrets (`STRAPI_PREVIEW_SECRET`, `STRAPI5_TOKEN`, Strapi admin credentials, Netlify build-hook URLs) but does not specify where secrets live, who has access, or how they are rotated.

**Resolution required:**

- **Inventory.** Document every secret: name, purpose, where it is generated, where it is stored (Netlify env vars, agency password manager, Strapi admin UI), who currently holds it.
- **Storage.** No secret is ever committed to the repository, pasted into a planning document, or left in a plaintext `.env` file outside a developer's home directory. Production secrets live in Netlify Environment Variables and (for server-side Strapi) in the DigitalOcean droplet's environment.
- **Rotation.** Minimum cadence: preview secret every 30 days, Strapi admin API tokens every 90 days, initial setup admin password on first successful login.
- **Access list.** Principle of least privilege. Named list of who can read which secrets. Access is logged.

---

## Block Phase S1 (Strapi 5 Provisioning) — HIGH

These must be resolved before the new Strapi 5 instance is stood up.

### SEC-04: Security logging and audit-trail configuration

**Status:** OPEN
**Severity:** HIGH
**Blocks:** S1, P0

The plan does not specify what security events are logged, where logs are stored, how long they are retained, or who has access. For a state-government site, auditability is a hard requirement — both for routine monitoring and for post-incident forensics.

**Resolution required:**

- **Strapi admin audit log enabled.** Captures every mutation (create, update, delete, publish, unpublish) with timestamp, user ID, content type, entity ID, and diff summary.
- **Netlify Function logging.** All requests to `/api/preview` and admin-facing endpoints logged with timestamp, HTTP status, client IP, user agent. PII redacted per agency policy.
- **Retention.** Logs retained for 90 days minimum; 1 year preferred; match agency record-retention schedule if longer.
- **Access control.** Log access restricted to the named incident-response list (SEC-01). Every log access is itself logged.
- **Alerts.** At minimum: multiple failed admin logins from one IP; preview-URL-generation burst from one user; unauthorized admin mutation attempts.

### SEC-05: Backup policy (distinct from rollback)

**Status:** OPEN
**Severity:** HIGH
**Blocks:** S1

The plan documents rollback (DNS reverse during cutover, v3 retained read-only for 30 days). That is disaster recovery for the *migration*. It is not a backup strategy for the v5 instance after cutover, which needs regular backups against the usual range of failure modes (corruption, accidental deletion, ransomware).

**Resolution required:**

- **Database.** SQLite file backed up daily to an off-site, encrypted destination (S3-compatible storage with encryption at rest).
- **Media.** Uploads bucket versioned; 90-day object-lock on critical media.
- **Retention.** Daily backups for 30 days; weekly backups for 1 year; monthly for 3 years (or per agency retention schedule, whichever is longer).
- **Restore testing.** Restore from backup tested quarterly at minimum. A documented restore runbook exists.

---

## Block Phase 6 (Search and Admin) — HIGH

These must be resolved before the admin flow is implemented in code.

### SEC-06: Admin authentication specification

**Status:** OPEN
**Severity:** HIGH
**Blocks:** P6, S8 (author UAT needs the admin flow to exist)
**Related:** `07-OPEN-QUESTIONS.md` Q7 (admin scope audit)

The plan defers admin authentication to Q7 but provides no security acceptance criteria. Default Strapi authentication is not sufficient for a state-government site whose author accounts are authorized to publish public-facing content.

**Resolution required:**

- **Multi-factor authentication (MFA).** TOTP (authenticator app) at minimum for every admin account. SMS acceptable but not preferred.
- **Rate limiting on login.** Maximum 5 failed attempts per IP per 15-minute window. Lockout notification to the administrator.
- **Session timeout.** 30-minute idle timeout on admin sessions; 8-hour absolute timeout regardless of activity.
- **Password policy.** Minimum 14 characters. Blocklist of known-compromised passwords (checked against a haveibeenpwned-style list at creation and change).
- **Initial setup admin.** Disabled or removed after S6, before S7 integration.
- **Rotation.** All admin account passwords rotated at S9 cutover and every 90 days thereafter.
- **Account provisioning.** New accounts created with one-time passwords delivered out-of-band; user must change password on first login.

### SEC-07: Preview-token operational plan

**Status:** OPEN
**Severity:** HIGH
**Blocks:** P4 (preview-mode end-to-end)
**Related:** `02-MASTER-DESIGN-PLAN.md` §4.5

The preview architecture documents a signed-cookie + shared-secret design. It says the secret is "rotated on a schedule" but does not specify how or when. Rotation of a shared secret across two services (Strapi and Netlify) requires coordination.

**Resolution required:**

- **Rotation cadence.** Every 30 days minimum.
- **Rotation orchestration.** Update the secret in Strapi configuration and Netlify environment variables within a coordinated 1-hour window. For that window, the preview endpoint accepts both the old and new secrets (dual-secret acceptance). After the window, only the new secret is accepted.
- **Rate limiting.** Maximum 20 preview requests per authenticated user per minute to catch abuse if a Strapi account is compromised.
- **Cookie properties.** HTTP-only, Secure, SameSite=Strict, max-age ≤ 8 hours.

---

## Block Phase 8 (Cutover) — HIGH

These must be resolved before the public DNS swap.

### SEC-08: Legacy-site dependency audit

**Status:** OPEN
**Severity:** HIGH
**Blocks:** P8

During the parallel-operation window, the existing Vue 2 site continues to serve production traffic. It has not received dependency updates during the rebuild (and likely for some period before). If it contains unpatched critical vulnerabilities, those are exposed for the project duration.

**Resolution required:**

- **Dependency vulnerability scan** of the current Vue 2 site early in Phase P0 (not at P8 — that's too late).
- **Critical findings** either patched in a final legacy deploy before Phase P0 or accepted in writing by the accountable officer.
- **Code freeze** on the legacy site from Phase P7 onwards — no further merges, no new deploys.

---

## Implementation-phase requirements (IMPORTANT)

These should be specified and implemented during their target phase. Not absolute hard blockers, but implementation should not proceed without them unless a written risk acceptance is on file.

### SEC-09: Content input validation

**Severity:** IMPORTANT
**Target phase:** P4

CMS-authored content (Markdown, rich-text, image and PDF uploads) is an attack surface. Authors are trusted but accounts can be compromised.

**Resolution during P4:**

- Markdown sanitization via a configured sanitizer (Remark + rehype-sanitize, or DOMPurify on rendered HTML). Scripts, inline event handlers, and dangerous attributes stripped.
- Image uploads validated against magic bytes (not just extension). Maximum file size 50 MB. SVG uploads either blocked or re-serialized through a whitelist.
- Rich-text fields use a tag allow-list; HTML comments stripped; dangerous protocols (`javascript:`, `data:`) blocked.

### SEC-10: Content Security Policy and transport headers

**Severity:** IMPORTANT
**Target phase:** P0 (baseline) and P7 (tightening)

**Resolution:**

- **CSP:** strict default-src 'self'; no unsafe-inline unless strictly necessary and then only with nonces; frame-ancestors 'none'.
- **HSTS:** `max-age=31536000; includeSubDomains`.
- **X-Content-Type-Options:** nosniff.
- **Referrer-Policy:** strict-origin-when-cross-origin.
- **Permissions-Policy:** disable camera, microphone, geolocation, payment, USB by default.

### SEC-11: CORS restriction

**Severity:** IMPORTANT
**Target phase:** S1 (Strapi config), P4 (Nuxt Functions)

**Resolution:**

- Strapi GraphQL and REST CORS restricted to `icjia.illinois.gov`, `next.icjia.illinois.gov`, and named dev/staging origins only. Wildcard origins forbidden.
- Admin endpoints require same-origin.
- Netlify Function endpoints (preview, form submission, webhook receivers) enforce origin checks.

### SEC-12: Rate limiting on public endpoints

**Severity:** IMPORTANT
**Target phase:** P0 (baseline), P6 (search), P4 (preview)

**Resolution:**

- 1000 req/min/IP on public static routes (Netlify CDN handles this; monitor for edge-bypass attempts).
- 100 req/min/IP on search endpoint.
- 20 req/min/user on preview (covered in SEC-07).
- 5 failed logins per IP per 15 min (covered in SEC-06).

### SEC-13: Supply-chain defenses

**Severity:** IMPORTANT
**Target phase:** P0

**Resolution:**

- Dependencies pinned via committed lockfiles (already in plan).
- `npm audit` or equivalent runs on every CI build; Critical/High vulnerabilities block merge.
- GitHub Secret Scanning enabled on the repository. Pre-commit hook for secret detection in the developer environment.
- Any new external package import (a fresh `import` or `require` of a third-party library) is reviewed by a human before merge. Typosquatting defense: lookalike package names (e.g., `left-pad` vs `leftpad`) are a documented supply-chain risk regardless of how the code was drafted; the review step applies uniformly to every new dependency.

---

## Out of scope (coordination with agency policy)

These items do not block implementation but must be resolved operationally. Flag them for the appropriate agency staff.

- **Data classification.** Which content is public, which is restricted, which is PII? For a public agency site the default is "all public," but confirm with legal/records.
- **FOIA coordination.** If any content on the site is FOIA-eligible, the audit trail must be exportable per agency records officer's requirements.
- **Illinois-specific security standards.** Confirm with agency IT whether NIST CSF, ISO 27001, or Illinois state IT standards apply to this project. Map requirements to specific phases.
- **Record retention.** Match agency record-retention schedule for published content, unpublished drafts, database backups, and audit logs.

## Tracking

This is an append-only document. When an item is resolved:

1. Change `Status: OPEN` to `Status: RESOLVED (YYYY-MM-DD)`.
2. Append a `Resolution` paragraph describing what was done, where the implementation lives (which PR, which configuration, which runbook), and who resolved it.
3. Do not delete the original item — the history is part of the security audit trail for this project.

Items reviewed at every weekly status update. Any item moved from `IN PROGRESS` to `RESOLVED` is noted in that week's update.
