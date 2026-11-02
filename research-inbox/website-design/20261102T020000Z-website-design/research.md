# A navigation label should not hide two different commands

Run: 20261102T020000Z-website-design  
Scheduled: 2026-11-02T02:00:00Z · Evaluated: 2026-11-02  
Author: Dot · Documentary research only

## Question and new contribution
How should a premium navigation bar expose a category landing page and its child links without making the same label unpredictably navigate or expand?

The live catalog and seven existing website-design research files were read first. The topic directory contained those seven packages and its README, with no same-run or visibly unfinished package. Prior research covers editorial composition, forced colors, grids, typography, forms, image crops and sticky reading space. This addendum addresses command identity and disclosure behavior, not another positioning or visual-style survey. The scheduled slot is retained although source evaluation began before it.

## Primary-source findings
- **S1 — Visual menu styling does not require menu semantics.** WAI's disclosure-navigation example deliberately uses ordinary navigation links rather than ARIA menu roles. Typical site navigation does not require the complete menu/menubar interaction model. Its optional arrow-key behavior supplements rather than replaces Tab traversal. The example closes an open dropdown with Escape and returns focus to its controlling button. WAI explicitly labels the example illustrative, not production-ready, and warns of browser/assistive-technology support gaps. [Disclosure navigation](https://www.w3.org/WAI/ARIA/apg/patterns/disclosure/examples/disclosure-navigation/)
- **S2 — Two purposes can have two controls.** WAI's hybrid example puts a top-level destination link alongside its associated disclosure button, with a nested list of child destinations. This documents a concrete alternative to overloading one label. It is an example, not a requirement that every navigation item have both controls. [Navigation with top-level links](https://www.w3.org/WAI/ARIA/apg/patterns/disclosure/examples/disclosure-navigation-hybrid/)
- **S3 — State describes controlled visibility.** The disclosure pattern uses a button whose Enter/Space activation toggles content; aria-expanded reflects whether it is visible, and aria-controls may identify the controlled region. These attributes do not themselves implement the state changes. [Disclosure pattern](https://www.w3.org/WAI/ARIA/apg/patterns/disclosure/)
- **S4 — Hover adds obligations.** Additional author-controlled content shown on hover or focus is generally required to be dismissible, hoverable and persistent under SC 1.4.13, with specified exceptions. Dismissal without moving pointer/focus is not required for an input error or content that obscures/replaces no meaningful content. A decorative gap between trigger and dropdown must not make the additional content impossible to reach by pointer. [Content on Hover or Focus](https://www.w3.org/WAI/WCAG22/Understanding/content-on-hover-or-focus.html)
- **S5 — Focus is not activation.** SC 3.2.1 prevents a component receiving focus from initiating a change of context. The explanation distinguishes content changes from context changes; revealing content is not automatically forbidden. Navigating to a new page or unexpectedly moving focus is a different matter. [On Focus](https://www.w3.org/WAI/WCAG22/Understanding/on-focus.html)

## Original synthesis: assign one purpose per control
For a category such as Work, first ask whether its landing page contributes something beyond the child list. If not, a disclosure button alone may be enough. If it does, preserve a normal destination link and place a separately named expansion button beside it. Avoid a first-tap-expands, second-tap-navigates rule unless there is a demonstrated need and a tested explanation: its meaning depends on hidden state.

The proposed design contract records the destination, expansion control, controlled region, visible state, keyboard dismissal behavior and narrow-layout variant. A chevron can reinforce expansion but must not be the only understandable distinction. These are original design recommendations, not an existing component library or normative naming scheme.

| Need | Proposed baseline | Avoid |
|---|---|---|
| Few destinations | Visible ordinary links | Hiding all navigation merely for visual minimalism |
| Category only groups children | Disclosure button plus child list | A destination-looking control with no clear expansion cue |
| Category has its own landing page | Separate link and disclosure button | One ambiguous target doing two jobs |
| Large application-style command surface | Evaluate a genuine menu pattern separately | Adding menu roles for appearance alone |

On a narrow layout, keep the same content relationship even if the visual panel becomes in-flow. Do not let a breakpoint silently remove the category landing page or replace explicit activation with hover. Treat any later full-screen modal navigation as a separate interaction decision; this report does not specify modal focus containment.

## Integration, alternatives and costs
The baseline uses semantic HTML, CSS and a small JavaScript disclosure state controller. No npm package, API, MCP service, authentication, new hosting service or paid dependency is required. Static pages and framework components can express it, but React/Next.js behavior and hydration were not tested. A server-rendered visible-link fallback is worth evaluating so failed enhancement does not strand navigation; that is an untested proposal, not an implementation delivered here.

Alternatives are a flat navigation list, a category landing page containing all child links, or a disclosure without a landing page. Choose based on information architecture before animation style. Hover activation increases input-mode and dismissal testing responsibilities. Review, localization and cross-device testing are the principal costs; no conversion or performance improvement was measured.

## Proposed acceptance exercise — NOT EXECUTED
1. Traverse links and disclosure buttons with Tab and Shift+Tab. Verify their names and actions are distinguishable and hidden child links do not remain unexpectedly reachable.
2. Activate the category link and expansion control separately on touch and keyboard. Confirm one does not unexpectedly perform the other's command.
3. Open, dismiss with Escape, reopen and move focus outside. Inspect actual focus and exposed state, not just panel opacity.
4. If hover is added, move into the panel, dismiss without relocating the pointer and test magnification. Record any exceptions claimed against the actual criterion.
5. Change width, orientation and label length while open. Check that controls and destinations survive without contradictory expanded state.
6. Test real browser/assistive-technology combinations and failed enhancement before adoption. This exercise is not a complete accessibility audit.

Future application: a design-system navigation inventory could flag entries assigned both a URL and an ambiguous toggle action, then require explicit design review. It would identify a review question, not prove a usability defect. No such tool was built.

## Provenance, rights and limits
Five W3C primary documentation pages were evaluated. The two APG examples are demonstrations, not a maintained production package selected for installation. Observed page-update dates are recorded separately from software releases; unknown revisions and commits remain null. Source documents are linked, not copied or relicensed; no implementation, icon, font, asset or substantial source text is redistributed. Public visibility does not grant reuse rights for external artwork. No asset licence audit occurred.

No browser, runtime, assistive-technology, integration, performance or accessibility-conformance tests were performed. Approval means bounded documentary QA only, not deployment permission.
