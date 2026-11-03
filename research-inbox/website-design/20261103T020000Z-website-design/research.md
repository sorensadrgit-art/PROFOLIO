# A page landmark is not a working skip link

Run: `20261103T020000Z-website-design` · Scheduled: 2026-11-03 02:00 UTC.
Evaluated: 2026-11-03 UTC. Author: Dot. Bounded documentary design reference.

## Question and decision
How should an editorial portfolio expose its page structure without turning every visual chapter into a landmark, while still letting keyboard visitors bypass repeated navigation?

**Original synthesis:** Design two related but distinct routes: a compact semantic map of major page areas, and an explicit skip-to-content interaction. A visually impressive scroll to the hero is not evidence that keyboard focus moved. Conversely, adding a main landmark does not demonstrate that a particular skip link works.

The live catalog, all 15 prior website-design reports and the complete topic tree were inspected first. No same-run or unindexed topic directory was found; all indexed topic entries were published. Earlier work addresses sticky reading space, navigation commands and breadcrumb orientation; the accordion report already warns against excessive panel regions. This contribution concerns page-region granularity and the skip interaction's destination contract, not another menu or positioning design.

## Source findings
- APG explains that landmarks expose major page structure and support assistive navigation. Native main and nav elements imply landmark roles. It advises restraint: seven or fewer is a rule of thumb, not a WCAG limit or a reason to invent seven regions. [S1](https://www.w3.org/WAI/ARIA/apg/patterns/landmarks/)
- APG's practice distinguishes landmarks from ordinary sections. A section becomes a region landmark when it has an accessible name. Repeated landmark roles generally need distinct labels; identical navigation sets can share a label. Labels should not redundantly include the role name. Its advice to use one main landmark per page has separate-document/application caveats. [S2](https://www.w3.org/WAI/ARIA/apg/practices/landmark-regions/)
- The WCAG 2.2 Understanding page explains SC 2.4.1, Level A: provide a way around content blocks repeated across pages. It lists skip links and structural techniques as possible approaches. This does not mean every site must use one particular skip-link implementation. Understanding documents are informative, not the normative standard. [S3](https://www.w3.org/WAI/WCAG22/Understanding/bypass-blocks.html)
- Technique G1 describes a first interactive link to main content, visible always or on focus. Activation moves focus to main content; its example continues Tab navigation inside the story. The technique prefers persistent visibility but does not require that for SC 2.4.1. G1 is an optional sufficient technique, not a universal implementation mandate. [S4](https://www.w3.org/WAI/WCAG22/Techniques/general/G1)

## Original design contract — unimplemented
Use a single-page case study as the bounded fixture. Begin with a site header, primary navigation, main narrative and footer. Express narrative chapters through headings; promote a chapter to a named region only when independent region navigation is useful. Do not label every decorative container merely because the design system calls it a section.

| Design decision | Proposed starting point | Evidence required before adoption |
| --- | --- | --- |
| Site versus chapter structure | A small page-level landmark map; chapter headings below it | Inspect the actual accessibility tree, not component names |
| Several navigation groups | Names such as Primary and On this page | Visitors can distinguish purposes; identical duplicated sets assessed separately |
| Repeated header before case study | An early, clearly named skip link targeting main content | Link appears on focus and activation transfers focus |
| Cinematic cover preceding useful narrative | Main starts at the editorial content boundary, not an arbitrary animation marker | Target identity remains useful with media unavailable |
| Responsive alternative header | Preserve one coherent exposed structure for the active view | No duplicate hidden controls or confusing region list |

Keep target identity stable across the rendered page. Review duplicate IDs, route replacement, and conditional layouts before relying on a fragment. Do not claim a particular tabindex or JavaScript recipe is universally necessary: this research did not compare browser focus algorithms. If the chosen stack needs a focus repair, document why and test it, rather than masking an unexamined target problem.

For a client-rendered route, make the destination available when the skip control can be used. Treat route-entry focus, a user-activated skip, and restoring previous scroll as separate interactions. This is a proposed integration boundary, not a claim about a router's default behavior. Avoid moving focus simply because a section became visible during scrolling.

## Alternatives and engineering assessment
A static HTML/CSS baseline can express landmarks, headings and an anchor without an npm package. Framework components can emit the same structure; React, Next.js, hydration and routing integration remain untested. No API, MCP connection, account, installation or additional hosting service is required for this reference. There is no library version or software release selected here.

Alternatives are an always-visible skip control, one revealed on focus, or other suitable bypass mechanisms evaluated against the actual page and user-agent support. Landmarks serve structural navigation; an explicit visible-on-focus link can also serve keyboard users who do not use landmark-navigation commands. Neither alternative is an automatic conformance certificate. Prefer an in-flow contents list for chapter navigation rather than promoting all chapter containers into landmarks solely to mimic that list.

The sources include linked landmark examples and a described skip-link example; none were executed. Principal costs are content modelling, implementation review, localization and browser/assistive-technology testing. No paid service pricing, measured performance gain or conversion claim applies. Main risks are excessive landmark noise, indistinguishable labels, broken targets, hidden focus and a scroll-only false positive.

Future application: a design-system page-shell checklist could collect region names, skip target identity and a keyboard traversal recording alongside visual screenshots. It would be evidence for review, not a replacement for human testing.

## Proposed acceptance exercise — all NOT RUN
1. Load the fixture without enhancement. Reach the skip link before repeated navigation and verify its wording and visibility.
2. Activate it; inspect actual focus and the next Tab destination. Do not pass based only on viewport movement or URL fragment.
3. Inspect headings and landmark navigation using recorded browser/assistive-technology versions. Verify that chapter structure remains understandable without one region per chapter.
4. Repeat with two navigation groups, duplicated responsive templates and long translated names. Check exposed names and duplicate IDs.
5. Repeat after client navigation, media failure, zoom and a sticky-header state. Check target availability and visible focus separately from landmark existence.
6. Verify normal navigation remains usable when the skip link is not used. Record failures rather than silently adding focus-moving animation.

## Rights, maintenance and limitations
These are rolling W3C primary documentation pages, not installable production components. Observed update dates are recorded in sources.json; unobserved revisions, release dates and commits remain null. APG footer links to the W3C Software and Document License (2023), which permits reuse subject to notice, disclaimer and modification-notice conditions, and restricts trademark publicity. Public availability is not unrestricted reuse. [S5](https://www.w3.org/copyright/software-license-2023/) This package provides original analysis and links, not copied implementation, artwork or substantial documentation text; no asset licence is selected.

No runtime, browser, accessibility-conformance, usability, integration or performance tests were performed. Independent review and a bounded sensitive-data scan are documented in qa.md. Approval is documentary only, not Gemini approval or deployment permission.
