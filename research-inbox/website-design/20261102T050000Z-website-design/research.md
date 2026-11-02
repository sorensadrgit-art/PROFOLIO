# A project card is not automatically one giant link

Run: 20261102T050000Z-website-design. Scheduled: 2026-11-02T05:00:00Z.  
Evaluated: 2026-11-02 UTC. Author: Dot. Documentary design reference.

## Question and contribution
When should a premium project card link as a whole, and how should it change when a second destination or action is introduced?

The live catalog and eight prior website-design reports were read first. The topic tree contained those eight packages and README, with no same-run or visibly unfinished package. Prior work covers image meaning, layout, typography, forms and navigation disclosure. This addendum concerns the interaction boundary of editorial cards, not their crop or menu behavior. The run resumed with the same ID after an initial unavailable-branch checkpoint; live checks were repeated before research.

## Source findings
- **S1: The container has a content contract.** HTML's anchor content model is transparent but forbids interactive descendants, descendant anchors and descendants with a specified tabindex. An anchor used as a card wrapper cannot conform to that content model while containing another link or button merely because CSS makes them look separate. The anchor with href represents a hyperlink labelled by its contents. [HTML anchor](https://html.spec.whatwg.org/multipage/text-level-semantics.html#the-a-element)
- **S2: Purpose matters more than visual proximity.** WCAG 2.4.4 permits purpose to be determined by link text or by text with programmatically determined context, subject to its general-ambiguity exception. The informative explanation recommends meaningful link text and describes contextual relationships such as the same paragraph/list item. A project title visually near an isolated “View” label is not, by appearance alone, evidence of such a relationship. [Link Purpose](https://www.w3.org/WAI/WCAG22/Understanding/link-purpose-in-context.html)
- **S3: Large cards do not excuse small secondary controls.** WCAG 2.5.8's AA criterion uses 24 by 24 CSS pixels with spacing, equivalent-control, inline, user-agent and essential exceptions. The spacing exception centers a 24-pixel-diameter circle on each undersized target’s bounding box; it must not intersect another target or another undersized target’s circle. It is not a universal 24-pixel gap. Evaluate the secondary target itself and the applicable exception. [Target Size](https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html)
- **S4: Preserve real navigation.** MDN documents keyboard Enter activation for focused anchors with href and warns that fake href values plus click handlers impair link operations, including copying, dragging and opening elsewhere. It recommends buttons for actions instead of fake links. Its compatibility table is documentation, not this run's browser test. [MDN anchor](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/a)

## Original synthesis: decide the action set before the hit area
Start with a project title that is a real destination link. Then choose the smallest interaction model that communicates the available commands.

| Card content | Proposed baseline | Review gate |
|---|---|---|
| One destination, short non-interactive content | A whole-card anchor may be suitable | Inspect its accessible name, selection behavior and focus presentation |
| Long summary or dense metadata | A clearly styled title link | Preserve easy reading and text selection without accidental navigation |
| Project page plus live demo/download | Separate real links in a non-interactive card container | Distinct names, hit regions and tab sequence |
| Project link plus Save/Preview action | Link and separate button | No nested interactive wrapper; activation must not also navigate |

These choices are design recommendations, not a standard-mandated card taxonomy. Whole-card linking is not automatically inaccessible, and multiple links to the same destination are not automatically a failure. The decision is about the actual content and interaction cost, not a universal rule to maximize or minimize tab stops.

Avoid making a non-semantic container clickable and then patching keyboard behavior as a substitute for a real link. If a stretched link or overlay is proposed, review whether it intercepts secondary controls, selection or unrelated whitespace. This report does not provide or certify such an implementation. A visual focus treatment around the card may help, but the real focus target and meaningful label must remain inspectable.

## Integration, alternatives and costs
The baseline is native HTML and CSS, with JavaScript only for genuine stateful actions. It needs no package install, API, MCP service, account or additional hosting service. React/Next.js can render the structure, but framework routing, event propagation and hydration were not tested. Prefer visible explicit links over a custom click-proxy layer unless a later prototype demonstrates a reason for the extra complexity.

Alternatives include a plain project list, a title-linked card, a single-destination whole-card link, or a card with an explicit actions row. Editorial review and keyboard/touch/assistive-technology checks are the main costs. No conversion lift, performance gain, pricing or payload reduction is established.

## Proposed acceptance exercise — NOT EXECUTED
1. Tab through a grid and identify every destination/action from its exposed name; check visible focus at narrow widths and zoom.
2. Open a project link in a new tab, copy its address and follow it with JavaScript unavailable where the page supports that fallback.
3. Activate each secondary link/button. Confirm it does not also trigger parent navigation.
4. Attempt to select descriptive text. Review accidental activations and overlay coverage rather than assuming a large hit area is always better.
5. Inspect DOM nesting and accessible names; measure each pointer target and document any claimed exception.
6. Test the real browser/assistive-technology matrix. Screenshot appearance alone cannot certify the behavior.

Future application: a design-system card inventory could record each variant's destinations, commands, name and hit-area owner before visual styling is approved. A later lint rule could flag nested controls, but human review would still be needed for purpose and usability. Neither was built here.

## Provenance and rights
Four primary documentation pages were retrieved directly. HTML is a living standard, while the WCAG Understanding pages are informative explanations. No software release or maintained third-party component is selected; release/commit identifiers are null where unknown. Page-update dates are recorded separately. Documentation demos were not executed. This is original linked analysis, with no copied implementation, protected imagery, fonts or substantial source passages. Public visibility is not unrestricted asset reuse; no external asset rights were audited.

No browser, runtime, integration, performance, assistive-technology or accessibility-conformance tests were performed. Documentary QA is not deployment permission.
