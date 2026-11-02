# A responsive comparison table must preserve the comparison

Run: `20261102T080000Z-website-design`  
Scheduled: 2026-11-02 08:00 UTC; evaluated: 2026-11-02 09:36 UTC.  
Author: Dot. Documentary research only; recommendations below are untested.

## Decision and scope

**Question:** How should a premium service or project-comparison section remain useful on narrow screens without destroying the relationships that make it a comparison?

**Original synthesis:** first decide whether people need to compare values across a common set of attributes. If so, prefer an intentionally simple semantic table with a locally scrollable fallback over an automatic conversion into visually attractive but context-poor cards. This is a design decision, not a claim that every table should scroll or that cards are inherently inaccessible.

Two supporting questions: which relationships need markup, and where does the reflow exception stop? This is distinct from the nine earlier website-design packages: typography, grids, forms, crops, sticky navigation, disclosure navigation, forced colors, visual-system foundations and project-card click boundaries. Live catalog and topic directory were checked; no same-run or unindexed unfinished directory was present.

## Source findings

- **Relationships:** WAI describes data tables as grids of logically related information. Mark header cells with `th` and data with `td`; increasingly complex relationships may need explicit scope or header references. A visual arrangement alone does not expose those relationships. [S1](https://www.w3.org/WAI/tutorials/tables/)
- **Do not over-prescribe scope:** WAI allows simple, unambiguous one-header tables with basic header/data markup; when heading direction could be confused, explicit scope clarifies it. This is not a blanket requirement to decorate every cell with additional attributes. [S2](https://www.w3.org/WAI/tutorials/tables/one-header/)
- **Identification versus explanation:** A caption identifies a table; a summary can explain a complex structure. WAI does not say both are mandatory for every table and warns against repeating the same information. [S3](https://www.w3.org/WAI/tutorials/tables/caption-summary/)
- **Reflow boundary:** WCAG 2.2's informative Understanding page explains the 320 CSS-pixel width condition for vertically scrolling content and the exception for parts requiring a two-dimensional layout. Its data-table example explicitly excludes individual cells from that exception: ordinary text within cells must still meet reflow requirements. A cell may have its own exception only if its content independently needs two-dimensional layout. This does not justify horizontal scrolling for the whole page. [S4](https://www.w3.org/WAI/WCAG22/Understanding/reflow.html)
- **Scroll operation:** MDN says some browsers do not make scrolling areas keyboard-focusable. Its guidance is to make the container focusable with `tabindex="0"` and provide appropriate role/name context, such as a named region. A visible overflow area is therefore not evidence that keyboard users can operate it. [S5](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/overflow)

## Proposed design contract — original, untested

Use a short comparison brief before selecting a component: what decision is the reader making, which attributes are shared, and which differences matter? For example, a service comparison might compare delivery format and review rounds. This is an illustrative future application, not actual SOREN pricing or an implemented offer.

1. Keep the common comparison attributes explicit and consistent. Write meaningful column and row labels; do not rely on colored ticks with an absent explanation.
2. Prefer a small flat table before considering grouped headers. Remove decorative complexity, not decision-critical data.
3. Give the section an editorial heading and, when useful, a distinct table caption. Avoid identical repeated announcements.
4. If the table genuinely needs two axes, constrain overflow to the table wrapper. Let surrounding narrative and each cell's prose wrap. Do not use page-level clipping as a way to hide the problem.
5. Make overflow discoverable without relying only on a fading edge. A concise visible instruction may help; avoid declaring that it replaces keyboard operation or an accessible name.
6. Treat the wrapper's focusability, name and visible focus indicator as an explicit review item. Do not place focus on every static cell or add a widget-style grid interaction model merely for appearance.
7. Do not hide disadvantageous values on small screens. If a secondary detail is moved to another view, preserve a clear path to it and verify that the comparison remains understandable.

The contract deliberately avoids sticky columns, drag-to-scroll scripting, custom scrollbars and synchronized duplicate tables as a default. They introduce additional behaviors needing review; they are not forbidden when real requirements justify them.

## Small actionable alternatives

| Pattern | Select when | Main tradeoff to verify |
|---|---|---|
| Simplified wrapping table | Few short attributes fit naturally | Long translations or identifiers may still overflow |
| Semantic table in local scroll region | Cross-column comparison needs two axes | Keyboard reachability, discoverability, cell wrapping and context |
| Independently labelled stacked records | Each item can be understood without cross-row comparison | Repetition and loss of rapid comparison; preserve all labels/data |
| Separate detailed comparison view | Landing page only needs an overview | Extra navigation; overview must not imply missing detail does not exist |

These are original selection heuristics, not a comparative usability study. Do not choose a conversion breakpoint solely because a device category is labelled “mobile.”

## Engineering, compatibility and operational evaluation

**Purpose/technology:** native HTML table semantics plus CSS overflow; optional application data mapping. No new npm package, JavaScript runtime dependency, API key, MCP service, browser extension, paid service or hosting change is needed for the proposed static pattern. A React/Next implementation would render the same semantic HTML, but framework versions and application integration were not tested. No install or deployment occurred.

**Maintenance/version:** this is platform guidance rather than a released tool. The retrieved WAI overview displays 16 February 2023; MDN overflow displays 27 August 2026. Other page-update dates and commit/release identifiers remain null where not observed. The normative context is WCAG 2.2; the cited Understanding page is explanatory guidance, not proof of conformance. WAI examples and MDN's overflow example are documentary references, not demos executed in this run.

**Costs/risks:** no incremental service subscription is proposed. Authoring effort, content upkeep, localization and browser/assistive-technology testing remain real operational costs; no monetary estimate or performance benchmark was measured. Large datasets, sorting, editable cells and virtualization are outside this bounded question and may require a separately researched component strategy.

**Rights:** public documentation is not unrestricted reusable content. W3C's document licence conditions include source/status notices and limits on derivative specifications; code components have separate terms. [S6](https://www.w3.org/copyright/document-license-2023/) MDN documentation is CC-BY-SA 2.5 or later unless otherwise stated, with attribution/share-alike conditions for reuse. [S7](https://developer.mozilla.org/en-US/docs/MDN/Writing_guidelines/Attrib_copyright_license) This package contains original analysis and brief factual paraphrases, not copied examples, full pages, assets or a relicensed upstream component. Any future imported code or artwork needs its own licence check.

## Next validation gate — not performed

Build one representative fixture only after implementation is authorized. Include long translated headings, long cell prose, empty values and multiple action links. Inspect semantic header associations, then test narrow/zoomed layout, keyboard scrolling/focus, touch discoverability, screen-reader table navigation and print output. Confirm that the page itself does not acquire unintended horizontal overflow. Record actual browser/AT versions and failures.

No runtime, performance, accessibility-conformance, browser, assistive-technology or integration test passed in this research run. Documentary approval covers this package's source/claim review only, not deployment permission. Future original applications include service-scope comparisons and case-study results matrices; neither was built.

