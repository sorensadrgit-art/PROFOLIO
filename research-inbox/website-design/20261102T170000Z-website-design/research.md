# A tooltip is supplementary copy, not a hidden control panel

Run: 20261102T170000Z-website-design  
Scheduled: 2026-11-02T17:00:00Z  
Evaluated: 2026-11-02 UTC  
Status: APPROVED_DOCUMENTARY; no implementation tested.

## Decision and incremental value

Question: how should a premium portfolio expose short contextual help without turning visual restraint into inaccessible, transient content?

Read the live catalog and twelve existing website-design reports before selecting this gap. The navigation-disclosure report mentions hover obligations, but does not resolve the distinction between a tooltip, an interactive information panel and native title text. No same-run package or unfinished topic package was found in the remote topic directory. This report adds a content-classification and dismissal-state contract rather than another navigation or modal guide.

**Recommendation, not a measured result:** keep indispensable project facts visible. Use a tooltip only for short supplementary description. Use an explicitly activated disclosure for longer or interactive details. A polished floating surface does not decide its semantics.

## Source findings

- **S1, normative WCAG 2.2:** SC 1.4.13 at AA covers additional content shown and hidden through hover/focus. Its three conditions concern dismissal without moving pointer hover or keyboard focus, pointer access to the revealed content, and continued visibility. Dismissal has exceptions for input errors or content that does not obscure/replace other content; user-agent-controlled presentation unmodified by authors is exempt. [WCAG criterion](https://www.w3.org/TR/WCAG22/#content-on-hover-or-focus)
- **S2, informative explanation:** native browser title tooltips illustrate that user-agent exception. The explanation includes custom tooltips and other nonmodal hover/focus popups in scope and describes Escape as one dismissal mechanism. This is explanatory guidance, not an independent normative standard. [Understanding 1.4.13](https://www.w3.org/WAI/WCAG22/Understanding/content-on-hover-or-focus.html)
- **S3, normative role definition:** WAI-ARIA 1.2 defines tooltip as a contextual description and recommends an aria-describedby reference before or when it is displayed. Adding the role alone does not implement event handling. [Tooltip role](https://www.w3.org/TR/wai-aria-1.2/#tooltip)
- **S4, qualified pattern guidance:** APG says its tooltip pattern remains work in progress without task-force consensus. It describes keeping focus on the trigger, Escape dismissal, and remaining open while the pointer is on the trigger or tooltip. It does not place focusable controls inside a tooltip; it suggests a nonmodal dialog for such a hover. Treat this as provisional interaction guidance, not a certified component. [APG tooltip](https://www.w3.org/WAI/ARIA/apg/patterns/tooltip/)
- **S5, HTML advisory-text limitation:** the HTML standard discourages reliance on title because user agents may require a pointer to expose it, excluding keyboard-only and touch-only use. Exemption from one WCAG criterion is therefore not evidence that title is an adequate sole delivery channel. [HTML title](https://html.spec.whatwg.org/multipage/dom.html#the-title-attribute)
- **S6, presentation is not semantics:** HTML popover can be applied to different semantic elements; a generic container still needs appropriate accessibility semantics. Its auto state supports light dismissal and close requests, while manual does not. The mechanism is not a universal tooltip accessibility implementation. [HTML popover](https://html.spec.whatwg.org/multipage/popover.html)

## Original design contract — untested

Classify each proposed annotation before choosing a component:
1. **Decision-critical fact:** client role, project availability, paid-download condition or an action's identity belongs in visible content near the action.
2. **Short optional explanation:** a plain-text tooltip may clarify an already understandable control. Its wording must not be the only usable name.
3. **Extended or actionable detail:** citations, links, copy buttons or several paragraphs belong in an explicitly activated detail region, with a separate focus and dismissal design.

Proposed state policy: model visibility and suppression separately. After Escape, suppress automatic reopening for the current uninterrupted hover/focus encounter. Reset that suppression only after the encounter ends, rather than on every pointermove or rerender. Track focus and pointer presence independently so leaving the trigger for the bubble does not immediately close it. This policy is original synthesis, not a browser guarantee or a tested state machine.

For visual composition, prefer a compact anchored note with a controlled surface, readable measure and no decorative motion dependency. A tooltip should explain a nearby object, not become a miniature navigation layer. Reassess the classification if text grows during translation.

## Alternatives and engineering evaluation

| Choice | Purpose and useful pattern | Main cost/risk |
|---|---|---|
| Visible helper text | Predictable explanation without discovery gestures | Uses layout space; edit content rather than shrinking it away |
| Custom tooltip | Optional short contextual description | Pointer/focus races, accidental reopening and assistive-technology differences require testing |
| Explicit disclosure | Longer details or additional actions | Needs clear activation, state and focus behavior; refer to prior disclosure research |
| Native title | Supplemental advisory text | Exposure is inconsistent across input methods; do not make it the sole path |
| Popover-backed surface | Browser-managed rendering/dismissal primitive | Still needs chosen semantics, interaction logic and feature-specific compatibility checks |

Technologies: semantic HTML, CSS and optional JavaScript/TypeScript state; React can host the same model but does not validate it. No npm package, backend, account, API key, MCP connection or hosted service is required for this proposal. Ordinary static-site hosting suffices. Incremental service fees are not inherent; implementation, localization and device/assistive-technology QA time remain real costs. No installation was performed.

Compatibility is deliberately not claimed from standards presence. Validate chosen popover features and fallback in the target browser matrix, including older embedded browsers. If enhancement fails, essential content must remain in ordinary HTML. No library shortlist or repository adoption is proposed, so repos.json is not applicable.

Maintenance evidence: WAI-ARIA 1.2 is the 6 June 2023 Recommendation; the retrieved WCAG 2.2 is dated 12 December 2024. Retrieved HTML pages display 30 October 2026 as the Living Standard update. APG's page revision date and exact commits are unknown. These are document dates, not product release or runtime support claims.

## Proposed acceptance exercise — NOT PERFORMED

Use an original fictional gallery toolbar with a visible “Compare projects” control, optional explanatory copy, and a separate “About this metric” disclosure containing a citation link.

- Keyboard: tab to the trigger, inspect accessible name and description separately, then dismiss without moving focus. Confirm the bubble stays dismissed during that encounter.
- Pointer: move from trigger to the bubble slowly, including any visual gap; ensure it remains reachable.
- Mixed input: keep keyboard focus while moving the pointer away; evaluate the agreed visibility policy.
- Touch: reach every essential fact and every action without simulated hover.
- Long copy/zoom: expand translations and magnification; check overlap, readable placement and dismissal.
- Route/unmount: remove an open trigger; verify timers/listeners and obsolete descriptions do not leak into a later instance.
- Interactive content: add a link to the proposed tooltip fixture and reject the classification rather than merely adding tabindex.
- Unsupported enhancement: disable JavaScript or the chosen popover support; inspect the content fallback.

A future original application could use this content classifier as a design-review checklist for metric annotations and icon toolbars, catching “hidden action panel” drift before animation work begins.

## Rights and limitations

This is original synthesis with links and short paraphrases, not copied examples or assets. Public documentation is not public-domain material. W3C document-use terms require source/copyright/status notices when copying, restrict derivative technical specifications and distinguish code-component licensing. See [S7, W3C document license](https://www.w3.org/copyright/document-license-2023/). W3C source ownership and document status are retained above; no source code or substantial text is reproduced. WHATWG document reuse terms were not fully evaluated for redistribution; no HTML-standard text or examples are redistributed.

No runtime, browser, screen-reader, integration, usability, accessibility-conformance or performance tests were run. Recommendations require a prototype and independent evaluation before adoption. Documentary approval means this package's bounded review only, not permission to deploy.

