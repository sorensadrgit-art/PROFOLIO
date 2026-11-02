# A filter chip is a choice, not automatically a tab

Run: 20261102T140000Z-website-design  
Scheduled: 2026-11-02T14:00:00Z  
Evaluated: 2026-11-02. Documentary research only.

## Decision and gap

**Question:** How should an editorial project gallery offer combinable category filters without implying tab-panel navigation or losing the visitor's place?

The live catalog and all eleven existing website-design reports were read before selection. They cover visual foundations, grid/reading order, typography, forced colors, forms, crops, sticky navigation, disclosure navigation, cards, tables and modal previews. None specifies multi-select gallery filtering. This is a narrow interaction and content-design addition, not a new component-library shortlist. The remote topic tree contained no same-slot package or unindexed topic directory at inspection.

**Recommendation, not a tested result:** use labeled native checkboxes for independent filters, styled as a restrained chip group without removing the actual input from keyboard/accessibility use. Keep gallery results one stable region. Choose tabs only for an actual one-panel-at-a-time content relationship. Design filter meaning, count feedback and empty-state recovery before animation.

## Source findings

1. **Tabs describe panels, not a visual pill shape.** APG defines a tab set around associated panels with one displayed at a time. Its keyboard contract includes arrow movement inside the tab list and Tab moving out. Automatic activation is recommended only when panel display has no noticeable latency. Giving unrelated filter pills tab roles therefore introduces a different interaction contract; that last judgment is this report's synthesis. [S1](https://www.w3.org/WAI/ARIA/apg/patterns/tabs/)
2. **Selection has an explicit state.** APG's checkbox pattern distinguishes checked, unchecked and optional mixed states; Space changes a focused checkbox. HTML defines checkbox checkedness independently of indeterminateness. The latter is not a third stored selection value. This is useful when a genuine aggregate control represents some selected child choices, but does not prescribe the gallery's OR/AND business rule. [S2](https://www.w3.org/WAI/ARIA/apg/patterns/checkbox/), [S5](https://html.spec.whatwg.org/multipage/input.html#checkbox-state-(type=checkbox))
3. **Announce the outcome, not the entire gallery.** WCAG's Understanding page distinguishes a search result list from a short result-count, waiting or no-results message. Such status messages, when not changing context, need programmatically determinable roles/properties so assistive technology can present them without focus. It does not require every result list to become a live region. [S3](https://www.w3.org/WAI/WCAG22/Understanding/status-messages.html)
4. **Automatic content changes are not categorically forbidden.** The On Input criterion addresses unexpected context changes from changed control settings, unless users have been advised beforehand. Its explanation distinguishes a content update from a context change. A filter can update nearby results without an obligatory Apply button; moving focus or changing context requires separate consideration. [S4](https://www.w3.org/WAI/WCAG22/Understanding/on-input.html)

APG patterns and WCAG Understanding pages are explanatory guidance, not a certificate for an implementation. HTML is a Living Standard; the fetched page displayed last updated 30 October 2026. Other source revision/commit dates were not established.

## Original design contract — untested

**Content rule:** label the group “Project disciplines” and explain “Choose any disciplines.” For this proposed gallery, multiple disciplines mean OR; separate groups, if later introduced, mean AND. No selection means all projects. These are product decisions, not requirements from ARIA. Make this rule visible where ambiguity matters.

**Visual rule:** use a shared baseline, short labels and a visible check indicator plus distinct selected treatment. Allow wrapping rather than a hidden horizontal overflow rail. Keep focus styling distinct from checked styling. A quiet neutral unselected chip should still look actionable. Do not let loading remove the controls or collapse the gallery's heading.

**State rule:** keep selected IDs, request generation, displayed result IDs and displayed count together. On a new selection, show a truthful pending state if a server request is needed. Only the current selection's completed response may replace results/count. A stale response must not announce a count for an older query. This is an original engineering safeguard, not an empirically tested performance claim.

**Focus rule:** keep focus on the filter being changed while results update. Provide a normal route to the results heading/list. A “Clear filters” button resets the selected set; if that button disappears after clearing, deliberately retain it or choose a logical focus destination. Do not silently unmount the focused control. Do not automatically focus the first result after every checkbox toggle.

**Feedback rule:** show a compact outcome such as “6 projects match” in a separate status element, with enough context to understand the number. Avoid making the whole image grid a live region. If selection is rapid, consolidate feedback for the completed current state rather than narrating obsolete responses. Exact announcement behavior must be tested with target browser/assistive-technology combinations.

**Empty/error rule:** zero matches is a successful query with no matching items, not a network failure. Preserve selected filters and offer Clear filters or a deliberate refinement. A failed query retains an explicit error/retry action; it must not pretend there are zero matching projects. If old results remain visible, label them as previous results rather than presenting them as current.

## Alternatives and adoption evaluation

| Choice | Suitable purpose | Main cost or risk |
|---|---|---|
| Native checkbox group, locally filtered list | Small multi-select project archive | Client ships the dataset; selection logic and count synchronization still need tests |
| Checkbox form with Apply button | Expensive server queries or a batch of edits | Adds one action; clearly distinguish draft selection from applied selection |
| Category links to dedicated pages | Categories are navigable destinations with independent content | Navigation and filter state are different; preserve meaningful URLs and back behavior |
| Genuine tabs | Mutually exclusive associated content panels | Must implement the panel relationship and keyboard model, not merely selected colors |

The proposed baseline uses HTML/CSS and a small JavaScript or TypeScript state layer. React/Next.js is optional, not a dependency requirement. No npm package, MCP, external AI connection, API credential, installation or new service is needed for local filtering. Existing static hosting can serve the assets; extra cost is development, content tagging and browser/assistive-technology QA, not a claimed zero-cost deployment. A remote search API introduces requests, latency, failure handling and backend costs; none is selected or priced here. Server-rendered links or a normal filter form are alternatives when client enhancement is unavailable.

Maintenance follows native platform semantics and the project's own state code. There is no package release cadence to evaluate. The sources offer APG examples as learning references; no example or demo was run. Browser/assistive-technology compatibility remains an implementation test requirement, especially after custom visual styling. No minimum framework/browser version or performance gain is asserted.

**Future original application:** a portfolio “discipline lens” could highlight shared craft across projects without changing the gallery into separate category pages. Keep the unfiltered editorial ordering stable within each result set, so changing lenses does not also unpredictably reshuffle the narrative. Evaluate whether that helps visitors find relevant work in a future usability study; no such study was performed.

## Proposed acceptance exercise — not performed

Use a small fixture with overlapping tags, a zero-match combination, long translated labels and deliberately reversed response completion order. Verify:

- Keyboard users can reach each independent choice, toggle it and perceive checked/focus states.
- Combined selections obey the stated OR/AND contract; clearing returns the agreed baseline.
- Rapid changes cannot let an old response replace current results or count.
- A result-count announcement has context, does not steal focus and does not read every card.
- Empty, pending, stale-display and failed states are visibly distinct and recoverable.
- Narrow layouts, text enlargement and forced colors preserve labels, control states and focus.
- Back/reload state matches the chosen URL policy if URL persistence is implemented.

## Rights and limitations

This package contains original analysis, proposals and source links, not copied implementations, screenshots or protected visual assets. Public access does not grant unrestricted reuse. APG links to the W3C Software and Document License (2023), which permits copying/modification subject to notice requirements and disclaims warranties; names/trademarks have restrictions. Consult the actual license before reusing source code or prose. [S6](https://www.w3.org/copyright/software-license-2023/)

No third-party assets or code are redistributed here. Other source-specific reuse licenses were not independently resolved and remain null in metadata. No runtime, accessibility, integration, performance or usability testing occurred. Documentary approval means this run's claim/link review only, not deployment permission or compliance certification.
