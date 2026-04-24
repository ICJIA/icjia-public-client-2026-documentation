# ICJIA Public Website Redesign — Executive Summary

**Audience:** Agency directors, program managers, and anyone in leadership who does not work with websites day-to-day.
**Status:** v2.1
**Last updated:** 2026-04-24 (§6 Gantt figure replaced with two tables for easier scanning; obsolete glossary entry for "Gantt diagram" removed)

This is a self-contained summary. You do not need to read any other document to understand this project. Technical terms are defined in the glossary at the end (§10). If something here is not clear, ask a team member directly — that is faster than tracking down supporting documentation.

---

## 1. The short version

We are building a new version of the agency's public website. The new site will **look better and work faster**. That is the point, in plain language. The current site looks dated and is slow on phones; the new one will look contemporary and be nearly instant.

Behind the scenes, we are also updating the publishing tool that staff use to edit pages — from a version that no longer receives security updates to the current version. Staff will see a cleaner, simpler editing screen; otherwise the workflow is the same.

The entire project is expected to take **6 to 8 weeks** of active work, start to finish.

Residents see no disruption. The existing site stays online the whole time; when the new one is ready, we switch the public address in a pre-announced window. Bookmarks and existing links continue to work.

## 2. What the new site will look and feel like

**Visual.** The new design is deliberately contemporary — cleaner typography, more readable layouts, a consistent look across every section. Compared to the current site, it will feel like a fresh page on a modern state government site rather than a page from 2021.

**Speed.** On a typical phone, most pages will feel instant. On an older phone or a slow connection, they will still be noticeably faster than today. We have measured the current site and know specifically where it underperforms; the new architecture solves that directly.

**Accessibility.** The site meets Illinois state-government accessibility standards by design — not with workaround code patched on top. Screen-reader users, keyboard-only users, users who need large text, and users who prefer reduced motion will all have a seamless experience.

**What's the same.** The same information is there — news, events, grants, research, about pages, policies, and the services residents look for. Menus are organized similarly. Existing web addresses continue to work; anything that moves is automatically redirected.

## 3. Why we are rebuilding rather than continuing to patch

The current site was built in 2021 and has reached its limits. Three specific problems motivated this project; none of them are reparable without rebuilding.

**It is slow on phones.** Most people who reach us are on a phone. On a typical mid-range phone, the current site takes several seconds to become usable — long enough that measurable numbers of visitors give up and leave before the page finishes loading. The delay is not caused by slow servers or a bad connection; it is built into how the site is structured. No amount of tuning fixes it.

**The publishing tool is past end-of-life.** The software staff use to edit news items, events, grants, and research — called a "content management system" in the trade, and specifically called Strapi at our agency — is running a version that the vendor no longer supports. It no longer receives security updates. Each month it stays on that version is a small but compounding risk.

**Accessibility is held together with workarounds.** The current site has two dozen small scripts that run on every page to fix accessibility problems that the underlying software generates. When one of these scripts breaks, a screen-reader user can be locked out of a page. This is a fragile situation to be in as a state-government agency whose users include many people who depend on assistive technology.

Fixing any one of these would justify some work. All three together justify a rebuild.

## 4. What changes for residents visiting the site

Almost everything that matters to a resident stays the same. The changes they will notice:

- **Pages appear much faster.** On a phone, most pages will be ready by the time the screen has finished loading. There is no visible loading spinner on content pages.
- **The site is easier to read.** Larger, clearer typography; better color contrast; more space between sections.
- **It works on more kinds of devices.** The current site is optimized for modern phones and desktops; the new site also works properly on older phones, tablets, and systems with high-contrast or large-text settings turned on.
- **Assistive technology works correctly.** Users of screen readers, keyboard navigation, or browser zoom get a seamless experience without the gaps in the current site.
- **Bookmarks still work.** Anywhere a resident or an external site has linked to `icjia.illinois.gov/...`, that link continues to resolve — either to the same content on the new site, or automatically redirected to the new location.

What they will **not** notice: any change in what the site does. All services, forms, downloads, and information are available in the same places, or easily findable through the same search.

## 5. What changes for staff who publish content

The editing process stays familiar. The improvements are:

**Preview before publishing.** Every content type — news, events, grants, research, biographies, unit pages, policy documents — will have a Preview button in the editor. Clicking it shows exactly what the page will look like on the public site, with real images and real layout, before anything is published. Today, staff typically publish first and check afterward; that becomes optional.

**A cleaner editing screen.** The new editor is a current-version upgrade of the same tool staff already use. Workflows are the same — create draft, preview, publish, unpublish, edit published, republish — but the screens are more responsive, less cluttered, and easier to use on a laptop or tablet.

**The same accounts and permissions.** Every staff account carries over with the same role. Whoever can publish news today can publish news after the switch. Whoever has admin access today keeps it. Nothing about who can do what changes.

**Author support during the transition:**

- A short orientation session (about one hour) before the switch, covering what is different.
- Written step-by-step guides tailored to each content type.
- A named support contact — Chris Schweda (IDS) is the single point of contact for staff questions for the first month.
- A standing weekly check-in during the first month for any issues that surface.

## 6. How long the project takes

**Approximately 6 to 8 weeks of active work, start to finish.**

This is substantially faster than a traditional website rebuild (typically 9–12 months for a project of this scope) because we are combining three things that compress the work:

- **AI-assisted code generation.** A large share of the repetitive work — creating page templates, writing the code that connects the editing tool to the public site, translating designs into working pages, and setting up the data migration — is handled by AI tooling working alongside the developer. Work that historically took weeks of developer time takes days.
- **Prior planning is already done.** The design direction is approved. The technical choices are made. The content inventory exists. The project starts on day one with known requirements, not a discovery phase.
- **Focused scope.** We are not adding new features or changing what the site does. We are rebuilding what exists, better. A fixed scope lets the work progress in a predictable sequence.

The project runs in two tracks that work at the same time and come together about halfway through. The tables below show the shape. Numbers are working days relative to project start, not calendar dates — calendar dates depend on when we begin.

**Track 1 — Visible website**

| Activity | Working days | Duration |
|---|---|---|
| Setup | 1–2 | 2 days |
| Visual design system | 3–9 | 7 days |
| Site structure | 10–13 | 4 days |
| Homepage | 14–17 | 4 days |
| Page templates | 18–22 | 5 days |
| Content rollout | 23–27 | 5 days |
| Search and admin | 28–30 | 3 days |
| Accessibility polish | 31–34 | 4 days |
| Switch to new site | 35–36 | 2 days |

**Track 2 — Publishing tool upgrade** (runs in parallel with Track 1; finishes around day 17, where the two tracks converge)

| Activity | Working days | Duration |
|---|---|---|
| Inventory | 1 | 1 day |
| Set up new version | 2 | 1 day |
| Content-type rebuild | 3–4 | 2 days |
| Move custom features | 5–7 | 3 days |
| Move images and files | 5 | 1 day *(runs alongside "Move custom features")* |
| Data migration | 8–10 | 3 days |
| Move author accounts | 11 | 1 day |
| Integration | 12–13 | 2 days |
| Author testing | 14–16 | 3 days |
| Switch to new version | 17 | 1 day |

**What stays on a human timeline.** Accessibility reviews by real people, author testing on the new editing tool, and final stakeholder approval. These are deliberately not compressed; they are where we catch problems that no amount of automation would surface.

**Milestones visible to leadership:**

- **End of week 2.** A working version of the new site is visible at a staging address (`next.icjia.illinois.gov`) — not the public address yet. Leadership can click through it.
- **End of week 4.** Real content flows from the updated publishing tool to the new site. Staff can try drafting, previewing, and publishing on the new system.
- **End of week 6.** The new site has all content types rendered and is ready for accessibility and visual polish.
- **End of week 7 or 8.** Final review; cutover to the public address.

## 7. What we need from leadership

Four decisions, none of them large, but each one blocks the start:

1. **Who owns the publishing-tool upgrade?** This is a project in its own right. It needs an owner — either agency IT, an internal team, or a contractor — with availability during the project. Without this owner, the work cannot begin.
2. **A brief conversation with staff authors.** We need to confirm which content types are still being actively edited (and therefore worth carrying into the new system as-is) and which have been static for a year or more. A 30-minute conversation is enough.
3. **An accessibility reviewer.** Either someone internal or an external consultant, available during weeks 6–8. Engaging a reviewer early — rather than at the end — catches problems while there is still time to fix them.
4. **A cutover window.** A pre-announced, pre-scheduled window of about two hours on a low-traffic day (weekend morning, late evening) when we switch the public address. Two weeks of advance notice is typical. Leadership picks the day.

Everything else — the development, the testing, the setup, the migration — happens without leadership intervention.

## 8. Risks worth naming at the leadership level

Three. None are unusual; all are manageable.

**The publishing-tool upgrade is on the critical path.** The visible website work can run for several weeks before it needs the upgraded publishing tool. After that point, it needs it. If the upgrade starts late or hits an unexpected problem — for example, if the current tool has a custom feature that takes longer than expected to recreate — the visible website work pauses and waits. We manage this by starting the upgrade's first steps in week 1, in parallel with the visible-website work, so any problems surface early.

**Running two sites in parallel takes a small amount of author attention.** For about two weeks before the cutover, the old and new sites are both online — old at the public address, new at a staging address. Staff continue publishing as normal, and both sites receive the content. Authors verify that what they published appears correctly on the new site. This is light work — probably ten to fifteen minutes per author per week — but it is work.

**Every change needs a settling-in period.** Even with a simpler editor and a faster site, the first two to three weeks after the switch will generate questions. "Why is this button in a different place?" "How do I do the thing I used to do?" The written guides and named support contact handle most of these; a few will require a brief team response. We have budgeted for this.

These risks are ordinary for a project of this size. They are managed through planning and communication, not by attempting to eliminate them.

## 9. How progress is tracked

**Weekly written update,** delivered every Friday to a defined stakeholder list. One page. Three sections: what was completed this week, what is coming next week, any blockers. No meeting required; the update is read at leadership's convenience.

**Each phase has a single completion question** that decides whether it is done. Not a long checklist — one question. For example: "Does the homepage display live content from the editing tool on the staging site?" The answer is yes or no; "mostly" does not count as yes. This discipline keeps phases from dragging.

**Decisions are recorded in writing** the moment they are made — the date, the decision, and who made it. Nothing relies on anyone's memory of a hallway conversation.

## 10. Glossary

Plain-language definitions for every technical term that appears in this document. Ordered alphabetically.

- **Accessibility.** Features of a website that let people use it regardless of disability. Common examples: compatibility with software that reads pages aloud (for blind users), full functionality when navigating by keyboard alone (for users who cannot use a mouse), and readability at large text sizes. Illinois state-government sites are required by law to meet a specific accessibility standard.
- **Agency IT.** The Illinois Criminal Justice Information Authority's internal information-technology staff — the people who administer the agency's servers, accounts, and internal systems.
- **AI-assisted code generation.** Software that writes programming code for a developer based on written instructions, which the developer then reviews and refines. It is to programming what a very fast draftsman is to an architect: it handles repetitive work so the person in charge can focus on the parts that require judgment.
- **Archetype (page archetype).** A template pattern for a kind of page. For example, every news article has the same general structure — headline, date, body, related items — so we build one "news article template" and every actual article uses it. The site has about six archetypes, covering every page on the site.
- **Build / rebuild (the technical kind).** In web development, a "build" is the automated process of preparing a website's files so it can be served to visitors. Each time content is published, a rebuild runs automatically. This takes a few minutes and is invisible to visitors.
- **Content management system (CMS).** The software that staff use to write and publish content. In this project, specifically the tool called Strapi. Residents never see it; only staff do.
- **Critical path.** The sequence of steps in a project where each one must finish before the next can start. If any step on the critical path slips, the whole project slips by the same amount. Steps that are not on the critical path can slip without affecting the end date.
- **Cutover.** The moment when the public web address is switched from the old site to the new one. It takes a few minutes to execute and is the final step of the project.
- **Dev / staging / production.** Three copies of the website. *Dev* is where developers try things. *Staging* is a private working copy that looks like the public site; staff test here. *Production* is the public site — what residents actually see.
- **Draft, preview, published.** The three states of a piece of content in the editing tool. *Draft*: being worked on; not visible to the public. *Preview*: draft rendered exactly as it will appear once published. *Published*: visible on the public site.
- **Editor, author, publisher.** Words used interchangeably for staff who create and publish content on the agency's behalf.
- **Milestone.** A specific, visible moment in the project that demonstrates progress — for example, "the homepage is live on staging." Milestones are the points where leadership can check in.
- **Parallel (running in parallel).** Two lines of work happening at the same time rather than one after the other. In this project, the visible-website rebuild and the publishing-tool upgrade are parallel.
- **Phase.** A self-contained piece of the project with a clear start, a clear end, and a single completion question. This project has nine phases on the website side and ten on the publishing-tool side. Each phase takes days, not weeks.
- **Production.** The public site — what residents actually see at `icjia.illinois.gov`. The opposite of "staging."
- **Redirect.** A rule that automatically forwards visitors from an old web address to a new one. If a resident has bookmarked a page that has since moved, a redirect quietly sends them to the new location.
- **Responsive.** A website that rearranges itself for the device viewing it. A phone sees a single-column layout; a desktop sees more content side by side. All modern sites are responsive; the new one is designed for phones first.
- **Rollback.** The plan for undoing a change if something goes wrong. In this project, rollback is fast: switch the public address back to the old site, which remains running for several weeks after the cutover specifically for this purpose.
- **Screen reader.** Software that reads a web page aloud for users who are blind or have low vision. The two most common are VoiceOver (on Apple devices) and NVDA (on Windows). We test the new site against both.
- **Staging.** A private working copy of the website, running at a different web address (`next.icjia.illinois.gov`), used for internal review and author testing before anything reaches the public site.
- **Static site.** A website where every page is prepared in advance rather than being assembled on the fly when a visitor arrives. This is what makes the new site fast: the work is already done by the time a visitor clicks.
- **Strapi.** The specific publishing tool that agency staff use. Version 3 is the current one (end-of-life); version 5 is the current release we are moving to.
- **URL / web address.** The address of a specific page, such as `icjia.illinois.gov/news/some-story`. "URL" and "web address" mean the same thing.

## 11. Questions

Questions about this project go to **Chris Schweda**, project lead and developer (IDS — Innovation and Digital Services, ICJIA). Chris has been with the agency for 25+ years, has built or managed 15+ websites across Vue 2/3, JavaScript, and TypeScript, and built the current ICJIA website (April–August 2020), which has been in continuous production since. Chris is the sole implementer of this project and the person accountable for fixes after launch — as has been the case for the current site for the past five years. You do not need to read any supporting document to understand the shape of this work. If something in this summary is not clear or you want deeper detail on a specific aspect — visual design, accessibility, the editing tool, the timeline — email Chris directly.
