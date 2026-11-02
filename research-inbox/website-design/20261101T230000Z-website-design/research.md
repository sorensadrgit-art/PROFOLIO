# Sticky navigation needs a reading-space budget

Run: 20261101T230000Z-website-design  
Scheduled: 2026-11-01T23:00:00Z · Evaluated: 2026-11-01  
Author: Dot · Documentary research only

## Question and new contribution
When should a premium portfolio stop keeping its navigation or project actions sticky, and what must be checked when those layers remain?

The live catalog and six existing website-design research files were read first. The topic directory contained those six packages plus its README, with no same-run or visibly unfinished package. The forced-colors reference already identifies sticky overlap as a risk. This addendum adds a concrete layout decision: preserve usable reading space, account for the actual scroll container, and distinguish scroll placement from physical room at document boundaries. It does not repeat a palette audit or redesign the application.

## Source findings
- **S1 — The AA floor is not the ideal composition.** WCAG 2.2 Understanding 2.4.11 explains that a component receiving keyboard focus must not be entirely hidden by author-created content. Full visibility is the enhanced AAA outcome. The component and its external focus indicator are separate assessment subjects. This is an informative explanation of WCAG, not a complete audit. [Focus Not Obscured](https://www.w3.org/WAI/WCAG22/Understanding/focus-not-obscured-minimum.html)
- **S2 — Scroll padding describes a viewing region.** MDN explains that scroll-padding-top offsets the preferred target region within a scrollport, for example to exclude a fixed toolbar. It applies to scroll containers and is not inherited. MDN reports broad browser availability; this package does not independently verify engine behavior. [scroll-padding-top](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/scroll-padding-top)
- **S3 — Sticky ownership follows ancestors.** A sticky element follows normal flow before its offset behavior; the offset does not move other elements. An ancestor with overflow hidden, scroll, auto or overlay can establish its scrolling mechanism even when that ancestor is not actually scrolling. A non-auto inset is needed on the sticky axis. [position](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/position)
- **S4 — Reflow must leave content usable.** The WCAG explanation describes vertical content at a width equivalent to 320 CSS pixels, with defined exceptions for content requiring two-dimensional layout. It also warns that fixed layers can consume reading space and suggests static or user-toggleable positioning at smaller viewports. That suggestion is not a universal mandatory breakpoint. [Reflow](https://www.w3.org/WAI/WCAG22/Understanding/reflow.html)
- **S5 — Physical space and target space are distinct.** W3C technique C43 combines ordinary padding with scroll-padding in a fixed-bottom-banner example so the final link can move into view. Its example also makes the banner non-fixed on smaller screens. Techniques are possible ways to satisfy criteria, not required implementations. The example's dimensions are not universal design tokens. [C43](https://www.w3.org/WAI/WCAG22/Techniques/css/C43)

## Original synthesis: the persistence contract
Treat sticky navigation as an enhancement granted space by the content, not an unconditional brand requirement. For each persistent layer, record:
1. Which scroll container it belongs to.
2. Which edge and actual rendered extent it occupies.
3. Whether another persistent layer competes for the same space.
4. How the layout returns that space when content wraps, zoom increases or the viewport becomes short.
5. How the first and last focusable content remain reachable.

These are proposed design records, not a browser API or normative threshold. Do not infer the occupied extent from one desktop screenshot. Long navigation labels, translated actions and a newly visible notice are proposed stress fixtures.

A useful design review asks whether a meaningful paragraph plus its relevant action can be read together. This is an editorial heuristic, not a measurable accessibility standard. If keeping the navigation prevents that, prefer normal flow over shrinking text or hiding essential labels.

## Small actionable choice set
| Situation | Proposed choice | What to verify later |
|---|---|---|
| Short page or little navigation benefit | Normal-flow navigation | All destinations still discoverable |
| Ample viewport, compact stable navigation | Sticky enhancement | Focus and reading area throughout the page |
| Short viewport or wrapped toolbar occupies too much space | Return to normal flow | No missing actions or order changes |
| Several persistent layers | Remove or consolidate persistence | Combined coverage, not each layer alone |
| Fixed bottom action overlaps final content | Review real end spacing and scroll target offsets separately | Last control can actually scroll clear |

Do not solve overlap only by raising z-index on the focused element: that may change painting order without repairing the reading layout. Do not treat a successful anchor jump as proof that Tab, Shift+Tab and end-of-document focus all work. These are untested engineering recommendations.

## Integration, alternatives and operational cost
Use semantic HTML navigation and CSS positioning first. No installation, npm dependency, API, MCP service, authentication or paid hosting addition is required by this proposal. It can be authored in static HTML or framework components; React/Next.js integration was not exercised. The same CSS may affect a nested scroller differently from the page, so identify ownership before assigning offsets.

Alternatives include static navigation everywhere, a compact in-flow contents list, or a user-controlled reveal. Hiding/showing navigation automatically on scroll introduces additional state and requires its own interaction review; no such implementation is recommended without evidence of need.

For dynamic header geometry, either simplify the layout until a dependable CSS contract is possible or separately investigate measured offsets. This report supplies no executed measurement code. Review and cross-device testing are the main costs; no performance savings, conversion gains or provider prices were measured.

## Proposed acceptance matrix — NOT EXECUTED
- Tab and Shift+Tab through every control, including first and last, with all relevant banners present.
- Check anchor navigation separately from keyboard focus, and inspect actual scroll containers and clipping ancestors.
- Exercise narrow and short viewports, portrait/landscape, zoom and long localized labels; record browser versions.
- Confirm that switching persistence off preserves all content, actions and a sensible reading order.
- Inspect actual visible components and focus indicators, not only computed offsets. Record any overlapping region.
- Test the real page's boundary space: an offset alone is not evidence that sufficient scroll range exists.

Future application: a design-system review could attach a persistence contract and a small viewport gallery to every floating navigation variant. A later automated check could flag geometry collisions for human review, but could not itself certify usability. Neither system was implemented here.

## Provenance, rights and limits
Five primary documentation pages were evaluated. Sources record observed page dates separately from software releases; no package versions or repository commits were selected. The references contain interactive examples, but none were run. No third-party implementation, artwork, font or substantial source text is copied. Public documentation is not a licence grant for external assets; no asset licence was audited. This is original linked analysis, not redistribution of a library.

There were no browser, runtime, integration, performance, assistive-technology or accessibility-conformance tests. Approval means this run's bounded documentary review, not permission to deploy.
