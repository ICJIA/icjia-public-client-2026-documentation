# ICJIA Public Website Redesign — Documentation

**Last updated:** 2026-04-23

This folder contains the planning set for the rebuild of icjia.illinois.gov. The site will be faster, easier for staff to update, and meet stricter accessibility standards. Behind the scenes the underlying content system (Strapi) is also being upgraded from a version that is no longer supported to the current version.

## Reading paths by role

Read in the order listed. Files are numbered; skip what isn't in your path.

| Role | Reading order |
|---|---|
| Non-technical manager, funder, or sponsor | **01** |
| Project sponsor or program manager | 01 → 02 → 04 |
| Backend / CMS engineer | 01 → 02 → 03 → 04 → 07 |
| Frontend engineer | 01 → 02 → 05 → 06 → 04 → 07 |
| Accessibility reviewer | 06 → 02 §5 → 05 §1.4 |

## Document status

| # | Document | Status | Audience |
|---|---|---|---|
| 00 | `00-README.md` | — | everyone (this file) |
| 01 | `01-EXECUTIVE-SUMMARY.md` | v1.0 | non-technical leadership |
| 02 | `02-MASTER-DESIGN-PLAN.md` | DRAFT v0.5 | technical team |
| 03 | `03-STRAPI-UPGRADE-PLAN.md` | DRAFT v0.1 | backend / CMS engineers |
| 04 | `04-PHASED-DELIVERABLE-PLAN.md` | DRAFT v0.1 | project management |
| 05 | `05-DESIGN-SYSTEM.md` | DRAFT v0.1 | frontend engineers, design |
| 06 | `06-ACCESSIBILITY-STRATEGY.md` | DRAFT v0.1 | frontend, QA, a11y reviewers |
| 07 | `07-OPEN-QUESTIONS.md` | LIVING | everyone — appended as decisions close |

## Contributing to these documents

- **07-OPEN-QUESTIONS.md** is append-only. When a question is decided, update its status and add the resolution as a new paragraph underneath — do not delete the original question. Decided items remain as a record and are referenced later.
- **Other documents** are revised in place, with a version bump in the frontmatter and an entry in the "Last updated" line noting what changed.
- **Changes to any planning document are recorded in [`../CHANGELOG.md`](../CHANGELOG.md).** The changelog package version follows the master design plan's version (`02-MASTER-DESIGN-PLAN.md`).
- Cross-references between documents use the numbered filename (e.g. `02-MASTER-DESIGN-PLAN.md §4.5`). If you rename or add a document, sweep for stale references.
- Forthcoming documents: `TEST-PLAN.md` (test strategy, coverage, procedures) — not yet written.
