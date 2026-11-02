# A project preview needs an exit contract, not just a backdrop

Run: `20261102T110000Z-website-design`  
Scheduled: 2026-11-02 11:00 UTC. Evaluated: 2026-11-02 UTC. Author: Dot.

## Decision and scope

**Question:** When is a modal project preview appropriate for a premium portfolio, and what opening/closing behavior must be designed before its visual treatment?

**Original synthesis:** use a short preview only when it preserves the reader's current browsing context. Keep a full case-study destination for extended reading. Treat focus entry, dismissal and return as one design contract; a dramatic overlay is not evidence of a functioning modal.

The live catalog and ten existing website-design research reports were inspected before selecting this gap. Their subjects include visual-system foundations, grids, typography, forced colors, forms, crops, sticky navigation, disclosure navigation, project-card actions and comparison tables. The disclosure report explicitly leaves modal containment out of scope. The live topic directory matched those ten catalog packages plus README, with no same-run or unindexed unfinished directory. This report addresses the preview lifecycle rather than repeating card-link boundaries.

## Source findings

- **S1 — A modal is an interaction boundary.** APG describes background content as inert, keyboard focus entering the dialog, Tab/Shift+Tab staying inside and Escape closing it. It recommends a visible close control and an accessible name. Initial focus depends on content: a static heading can be appropriate for long or structured material; describing all structured content as one string can be inappropriate. Return normally targets the invoker, with exceptions when it disappeared or the workflow has a more logical next destination. APG is authoring guidance, not a conformance certificate. [Dialog pattern](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/)
- **S2 — Native behavior has distinct modes.** MDN distinguishes `showModal()` from non-modal `show()`, documents `close()`, and notes that modal presentation makes the remaining page inert. Its dialog overview reports broad availability since March 2022 while warning that individual features vary. Do not infer support for newer dismissal attributes or invoker commands from that baseline alone. [MDN dialog](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog)
- **S3 — Hidden is not closed.** HTML warns that manually removing `open` can omit the close event and leave a modal document blocked. It advises supported closing methods instead. It prohibits `tabindex` on the dialog element itself; a focusable descendant is a different case. The standard also stresses intentional initial focus rather than blindly relying on the default candidate. [HTML dialog](https://html.spec.whatwg.org/multipage/interactive-elements.html#the-dialog-element)

## Original design contract — untested proposal

Before approving a preview design, record five decisions for each variant:

| Decision | Proposed portfolio baseline | Failure to look for |
|---|---|---|
| Entry purpose | A clearly named Preview action, separate from the full case-study destination | Readers expect navigation but receive an overlay |
| Opening position | Focus chosen for understanding the preview's title/content | The beginning scrolls away or the announcement loses structure |
| Working boundary | Background is unavailable while the modal is active | A dim background still accepts input |
| Exit | Visible Close control plus ordinary keyboard dismissal | Pointer-only exit or an invisible closed-but-blocking layer |
| Return | Re-establish a logical point in the gallery | Filtering/unmounting removed the original trigger |

This is a proposed component specification, not a universal requirement that all galleries use previews. Visual styling should follow those decisions. Start with a restrained frame, explicit title, limited content and a clear route to the full story. Do not turn a multi-chapter case study into a cramped preview merely to avoid a page transition.

A content change can invalidate the interaction design: adding a long results table or a form is not merely a larger screenshot. Revisit focus choice, scrolling and dismissal if scope grows. Prefer one preview at a time; nested previews, unsaved edits, routes inside overlays and complex media controls are outside this bounded recommendation.

For a future React/Next component, designate one lifecycle owner that reconciles application state with the native dialog's close/cancel events. Do not equate setting a CSS visibility flag with ending modality. The exact event/state implementation needs a separately authorized prototype; none is supplied or tested here.

## Actionable alternatives and engineering evaluation

- **Ordinary case-study page:** proposed default for long narrative, citations, multiple sections and a durable destination. Tradeoff: leaving the gallery requires a deliberate return path.
- **Short native-dialog preview:** proposed for a small contextual summary. Tradeoff: additional focus, dismissal, responsive and lifecycle validation.
- **Inline expansion:** proposed when the extra content belongs naturally in the document flow. Tradeoff: changes gallery geometry and may interrupt scanning.
- **Custom modal library:** defer until requirements exceed the native baseline. Any candidate needs its own version, licence, maintenance and integration review; this run selects no library.

**Purpose/technology:** the evaluated baseline is HTML dialog, CSS presentation and JavaScript DOM methods, not an npm product. No installation, authentication, API service, MCP connection, new hosting or paid service is required by this proposal. TypeScript/framework wrappers are possible but untested. No package dependencies or software-release versions were selected.

**Maintenance and demos:** HTML is a living standard displaying 30 October 2026; MDN displays 2 October 2026. APG revision and upstream commit IDs were not observed and remain null. Linked documentation contains examples, but no demo was executed. Browser/assistive-technology compatibility must be verified for the actual supported matrix, particularly if newer attributes are adopted.

**Costs and risks:** content maintenance, responsive design, localization and interaction testing remain real labor costs. No monetary estimate, payload saving, conversion uplift or performance gain was measured. Async content, removed triggers, mobile viewport changes and animation delays are review risks, not diagnosed application defects.

## Rights and provenance

This is original synthesis with brief factual paraphrases and source links, not copied examples or a redistributed component. Public availability does not grant unrestricted reuse of illustrations, fonts or other assets.

The APG footer links W3C's Software and Document License, whose redistribution conditions include notices and identification of modifications. [S4](https://www.w3.org/copyright/software-license-2023/) MDN documentation is CC-BY-SA 2.5 or later unless otherwise indicated, with attribution/share-alike conditions for reuse. [S5](https://developer.mozilla.org/en-US/docs/MDN/Writing_guidelines/Attrib_copyright_license) WHATWG's publication policy licenses living standards under CC BY 4.0; portions incorporated into source code use BSD 3-Clause instead. [S6](https://whatwg.org/ipr-policy#copyrights) No external artwork or source implementation was imported; future reuse requires checking its applicable terms.

## Proposed validation gate — NOT PERFORMED

After separate implementation authorization, test a single representative preview with a long title, delayed content and a trigger removed by filtering. Check entry announcement, initial focus, forward/reverse tab traversal, background interaction, visible close, Escape, focus return and reopening. Repeat at zoom/narrow viewport and with relevant assistive technology. Inspect actual native dialog state after each exit, not only opacity. Test failed enhancement and a direct full-case-study route.

An original future application is a design-system preview inventory recording entry, initial focus, exit, return target and content budget for each variant. It could expose inconsistent interaction decisions before visual polish. No inventory tool or prototype was built.

No runtime, browser, integration, performance or accessibility-conformance test passed in this run. Approval means bounded documentary QA only, not permission to deploy.

