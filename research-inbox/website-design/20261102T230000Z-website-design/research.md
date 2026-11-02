# A breadcrumb is a hierarchy, not a browsing history

- Run: 20261102T230000Z-website-design
- Scheduled: 2026-11-02T23:00:00Z; evaluated: 2026-11-02T23:01:37Z
- Author: Dot. Bounded documentary reference, not a component shortlist.

## Main question
How should a premium portfolio case study distinguish structural orientation from returning to a visitor's previous results?

## Source findings
1. W3C APG describes breadcrumbs as parent links ordered by hierarchy. The region is a labelled navigation landmark. If a current-page link is present, it uses aria-current="page"; that attribute is optional when the current item is not a link. This is informative pattern guidance, not implementation certification. [S1](https://www.w3.org/WAI/ARIA/apg/patterns/breadcrumb/)
2. GOV.UK recommends breadcrumbs for multiple levels, not flat sites or linear progress. Its own component ends with the parent, not the current page; HTML and Nunjucks examples are available. Its history records a nav/aria-label change in July 2024, Frontend v5.4.1—not proof that this is today's latest release. [S2](https://design-system.service.gov.uk/components/breadcrumbs/)
3. GOV.UK's separate Back link guidance targets multi-page transactions, requires implementers to restore the previous page's state, and explicitly advises against combining that component with breadcrumbs. It suggests clearer destination wording for complex journeys. Its December 2025 guidance entry is not a package release. This service-specific recommendation should not be misrepresented as a universal HTML prohibition. [S3](https://design-system.service.gov.uk/components/back-link/)
4. WCAG 2.2 SC 2.4.8 Location is Level AAA. Its Understanding page discusses orientation, including deep entry; breadcrumbs are one possible technique, not a universal AA requirement or proof of conformance. [S4](https://www.w3.org/WAI/WCAG22/Understanding/location.html)

## Original synthesis: choose the promise before the styling
A trail answers “Where does this page belong?” A return link answers “Where was I working?” A project can have one editorial parent while being discovered through several temporary filters. Treating a filtered list as the permanent parent can make orientation depend on entry route.

Proposed decision table—untested:
| Context | Recommended starting point | Avoid |
| --- | --- | --- |
| Flat portfolio with only Work and case studies | A clearly named Work link may suffice | Decorative multi-level trails with invented categories |
| Stable category / collection hierarchy | A parent trail generated from editorial route metadata | Deriving ancestors from browser history |
| Task-like review flow requiring return to filtered results | Prefer an explicit results-return control with persisted state | Calling a fixed unfiltered destination “Back” |
| External deep entry with no prior results state | Provide a valid structural destination | A control that promises a previous page which does not exist in this application |

Choose one primary orientation pattern rather than automatically stacking both. Testing may justify another design, but that would be a documented departure from GOV.UK's component rule, not something its documentation endorses.

## Untested implementation contract
- Keep route-parent metadata separate from optional return-state metadata. Use real destinations for structural links; do not make them dependent on a client-side history count.
- For a trail, start with semantic navigation and an ordered list, with a concise unique accessible name. Decide whether the current page is included; apply S1's aria-current distinction if it is.
- Keep separator decoration out of meaningful accessible names. This is an original implementation recommendation, not a tested result.
- If retaining a results-return URL, validate it as an allowed internal destination and limit retained state to necessary filter/sort identifiers. Do not place private content in a public URL.
- Prefer wrapping long translated labels over silently removing the only useful parent. Prototype narrow layouts before choosing truncation or collapsing.
- React/Next rendering can use ordinary links and route data; no animation package, API, MCP service, database or paid service is inherently required by this proposed design. Existing hosting costs remain; exact implementation effort and cost are unknown.
- Client routing, state restoration, focus handling and screen-reader announcements remain integration questions. No browser compatibility matrix or framework version was tested.

## Purpose, reuse and maintenance
The evaluated resources are maintained guidance and reference examples, not a new software dependency. GOV.UK provides runnable example links, but none were executed here. No repository, asset, logo or component code is copied. JavaScript, TypeScript or a particular rendering framework is optional for the proposed hierarchy model; the output can be server-rendered HTML/CSS.

GOV.UK pages identify content as Open Government Licence v3.0 except where stated. The linked licence endpoint returned HTTP 403 during this run, so detailed reuse conditions were not independently rechecked; do not infer that branding or every asset is reusable. No code-package licence claim is made. W3C's linked 2023 licence permits reuse subject to notices, modification attribution and other stated terms, and does not grant unrestricted trademark use. This package supplies original analysis with source links rather than copied implementation material. [S5](https://www.w3.org/copyright/software-license-2023/)

Alternatives: a named parent link for a shallow site, existing primary navigation, or a sitemap for wider orientation. None replaces evaluating actual visitor tasks.

## Future application and acceptance plan—not performed
Prototype the same case study reached from a category, a filtered gallery and an external deep link. Ask users to identify its collection and return to their prior results. Check keyboard focus, accessible landmark names, current-page semantics, narrow widths, zoom, long labels, disabled JavaScript and refreshed/deep-linked return state. Compare a minimal parent link with a breadcrumb before increasing visual complexity. No measured usability, conversion, performance or accessibility advantage is claimed.

## Novelty and limits
All fourteen prior website-design reports were read; none addresses breadcrumb hierarchy versus return history. Live catalog and topic directory showed no same-run artifact or unfinished unindexed topic directory. Documentary QA covers source claims and publication hygiene only. Runtime, integration, accessibility, visual, performance and usability tests were not run. Source versions and revision dates remain null where not observed.

