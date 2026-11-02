# An accordion saves space, not the reader's comparison state

Run: 20261102T200000Z-website-design  
Scheduled: 2026-11-02T20:00:00Z  
Evaluated: 2026-11-02 UTC; research began before the scheduled slot.  
Status: documentary research; no implementation tested.

## Question and incremental value

When should a premium case study collapse supporting content, and when does an exclusive accordion make the narrative harder to compare or navigate?

Read the live catalog and thirteen existing website-design reports before selecting this question. Prior navigation disclosure, preview-dialog and tooltip packages address different interaction contracts. This package adds case-study content hierarchy, simultaneous-reading policy and accordion semantics. No same-run package or unindexed topic directory appeared in the live topic listing. The unrelated known COBE catalog mismatch is outside this run and remains untouched.

**Recommendation, not a tested outcome:** keep the project brief, contribution and outcome visible. Collapse genuinely optional supporting sections only after descriptive labels and a readable expanded layout exist. Prefer independently open sections when readers may compare process evidence. Exclusive opening is a product choice, not an automatic accessibility improvement.

## Source findings

- **S1, APG accordion pattern:** headers use buttons within appropriately ranked headings, with expanded state and a relationship to the controlled panel. Enter/Space activate the header; Tab follows the page's focusable content. Panel regions are optional, and the guide warns against excessive landmark regions, using more than approximately six simultaneously expandable panels as an example. This is pattern guidance, not conformance certification. [APG](https://www.w3.org/WAI/ARIA/apg/patterns/accordion/)
- **S2, HTML details:** details is a disclosure; its first summary child supplies its legend. A shared nonempty name creates an exclusive group whose other members close when one opens. The standard explicitly cautions that this can hinder users who need to inspect several items or compare their contents. Details is not a substitute for tabs or menus. [HTML](https://html.spec.whatwg.org/multipage/interactive-elements.html#the-details-element)
- **S3, information structure:** WCAG's explanatory material says presentation-derived relationships must remain programmatically determinable or available in text. Merely making each row large and bold does not establish the intended structural relationship. [Understanding 1.3.1](https://www.w3.org/WAI/WCAG22/Understanding/info-and-relationships.html)
- **S4, content versus markup:** descriptive headings and labels are addressed separately from correct programmatic identification. A title can be meaningful while structurally incorrect, or correctly marked up while unhelpful. [Understanding 2.4.6](https://www.w3.org/WAI/WCAG22/Understanding/headings-and-labels.html)
- **S5, focus order:** sequential focus must preserve meaning and operation; it need not mechanically copy every visual placement. This supports reviewing the expanded and collapsed states as navigation structures rather than treating animation as the whole behavior. The latter application is this report's synthesis. [Understanding 2.4.3](https://www.w3.org/WAI/WCAG22/Understanding/focus-order.html)

The three Understanding pages are informative explanations of WCAG 2.2, not additional normative requirements. The HTML page displays Living Standard update 30 October 2026; the three Understanding pages display an update date of 9 March 2026. Exact revision commits of the APG/Understanding pages were not established. No package release or browser support date is inferred from these document dates.

## Original content and interaction contract — untested

Build a fictional case study with a visible one-paragraph brief, a visible role/outcome strip and optional “Research evidence,” “Rejected directions” and “Implementation decisions” sections. Labels should preview the subject, not repeat “Learn more.” Add a short always-visible summary when the label alone cannot explain why the reader should open the section.

Separate three choices:
1. **Content importance:** essential evidence for the headline outcome stays visible. Optional detail can be collapsed; do not hide an important qualification merely to produce a cleaner screenshot.
2. **Reading policy:** allow multiple sections open when comparison matters. Choose exclusivity only for a validated one-at-a-time task, and explain the consequence if it is unexpected.
3. **Implementation:** choose native details/summary or a deliberately authored accordion. Do not mechanically add APG button/heading markup inside native summary; these are distinct implementation routes whose accessibility behavior needs evaluation.

For a custom accordion, keep the header button as the sole element inside its heading. Place persistent utility actions beside that heading, not nested within its button. Synchronize visible state, aria-expanded and the panel's actual availability. A closed panel must not leave invisible interactive descendants in the tab sequence. Avoid decorating every panel with a region landmark by default.

Keep the focused header stable during user-triggered expansion. If a programmatic “Collapse all” operation would hide the currently focused descendant, deliberately relocate focus to a logical surviving control before hiding it. Do not use a delayed animation callback to close a newly reopened section. These are proposed state-management safeguards, not behavior guaranteed by native controls or proven by this review.

Use quiet separators, a clear open/closed indicator and a visible focus treatment. Keep the expanded text at a readable measure; extra whitespace is preferable to a dense nested accordion. On narrow screens, reflow content rather than converting every section to exclusive mode just to reduce page height. Treat deep links, browser Find, print output and back/reload restoration as explicit acceptance decisions; this report does not claim identical browser behavior for them.

## Alternatives and adoption evaluation

| Approach | Purpose | Costs and risks |
|---|---|---|
| Visible sections plus in-page contents | A narrative readers should scan or compare | Longer page, but less discovery work; sticky contents still need focus/occlusion testing |
| Native details/summary | Optional independent detail with browser-managed toggling | Custom styling and feature-specific compatibility must be checked; named exclusivity is not the same question as basic details support |
| Custom accordion | A justified interaction/state requirement beyond the native design | Own keyboard, state, heading/panel relationships and cleanup; more implementation and QA effort |
| Separate detail page | Substantial supplementary material with its own destination | More navigation; preserve a clear route back and meaningful links |

The baseline uses HTML/CSS, with optional JavaScript or TypeScript for application state. React/Next.js may host it but are not required dependencies. No npm package, API key, MCP server, account, backend or paid service is necessary for the proposed static content. Hosting can use the existing static-site setup. Costs are authoring, localization, implementation and browser/assistive-technology QA; no monetary estimate or performance saving was measured. No installation occurred.

Maintenance follows the project's component and content contracts, not a selected library's release cadence. APG has a linked example for learning, but no demo was run or copied. Native details support does not establish support for every newer attribute or a custom visual treatment. Verify the intended browser and assistive-technology matrix before adoption. This is not a repository shortlist; repos.json is not applicable.

**Original future application:** a case-study “evidence drawer” could retain the reader's chosen open sections through local layout changes, while a separate explicit reset restores the editorial default. Compare it against fully visible evidence in a future usability study. No claim of better engagement or comprehension is made here.

## Proposed acceptance exercise — NOT PERFORMED

Use the fictional case study above, with long translated labels, a panel containing links and a nested subheading, and several independently open sections.

- Inspect accessible names, heading levels, expanded state and panel relationships independently.
- Use keyboard activation and Tab in both states; closed content must not create invisible stops.
- Open two evidence sections and compare them without one unexpectedly closing under the chosen policy.
- Exercise programmatic collapse while focus is inside a panel; verify the documented recovery destination.
- Rapidly reverse expansion during an animation and check for stale completion hiding current content.
- Test narrow widths, text enlargement, forced colors and reduced motion without losing controls or content.
- Check Find, fragment navigation, print and back/reload against explicit product requirements, documenting differences rather than assuming support.
- Disable enhancement and verify that essential brief/outcome information remains available.

## Rights and limits

Original analysis and short paraphrases are provided, not copied examples, screenshots, complete source documents or protected assets. Public access is not unrestricted reuse. The cited APG and Understanding page footers link to [S7, W3C Software and Document license 2023](https://www.w3.org/copyright/software-license-2023/): copying, modification and distribution are permitted subject to its notice, pre-existing terms and change-notice requirements. Do not confuse it with [S6, W3C Document license 2023](https://www.w3.org/copyright/document-license-2023/), which has different derivative-specification restrictions. Check the specific source notice before reuse. WHATWG redistribution terms were not fully assessed; no substantial specification text is reproduced here.

No runtime, accessibility, integration, visual, usability or performance tests were performed. Documentary approval means bounded claim/link review only, not certification or deployment permission.
