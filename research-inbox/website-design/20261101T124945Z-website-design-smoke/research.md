# Premium controls in forced-colors mode: preserve affordance, not the palette

Run: 20261101T124945Z-website-design-smoke  
Evaluation: 2026-11-01  
Question: How should an editorial portfolio preserve recognizable project links and actions when forced colors remove shadow- and gradient-based cues?

## Scope and novelty

The existing editorial-system package establishes visual grammar, contrast and glass fallbacks. This addendum addresses a different failure boundary: user-agent palette replacement. The live catalog contained eleven packages and one website-design package at initial inspection; the topic directory contained only that prior package and its README. No same-run artifacts were present. This is documentary research, not a live-site accessibility audit.

## Verified source findings

**S1 — Rendering mechanism.** CSS Color Adjustment Level 1 describes forced colors as a user-preferred palette applied to selected properties. Shadows disappear and background images without a URL are removed. A dark/light theme is therefore not equivalent to forced-colors support. The specification retains automatic adjustment by default; opting out transfers color responsibility to the author. The retrieved edition is a Candidate Recommendation Snapshot dated 16 December 2025, not a final Recommendation. [CSS Color Adjustment](https://www.w3.org/TR/css-color-adjust-1/#forced-colors-mode)

**S2 — Small repairs, native semantics.** MDN recommends targeted legibility repairs rather than an unrelated alternate design. It explains that system-color selection follows native element semantics, not an added ARIA role: a div with a button role does not thereby acquire native button colors. Its example repairs a shadow-defined button using a border. The page reports modification on 20 April 2026; browser/device conformance was not tested here. [MDN forced-colors](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@media/forced-colors)

**S3 — Contrast is conditional, not “border every box.”** WCAG 2.2 Understanding 1.4.11 explains the 3:1 adjacent-color requirement for necessary identifying UI/state information, with exceptions including inactive controls and unmodified user-agent appearance. It does not require every control to have a visible hit-area boundary when its content already identifies the control. A new focus style still needs evaluation against its adjacent colors. Understanding documents explain the criterion; they are informative. [Non-text Contrast](https://www.w3.org/WAI/WCAG22/Understanding/non-text-contrast.html)

**S4 — Glass can fail a separate gate.** Understanding 2.4.11 distinguishes a focused component from an external focus indicator. At AA, author-created content must not entirely hide the component receiving keyboard focus; partial visibility is not a universal accessibility pass. Transparent/blurred overlays can instead cause non-text contrast failure. The document identifies sticky headers/footers as common overlap risks. [Focus Not Obscured](https://www.w3.org/WAI/WCAG22/Understanding/focus-not-obscured-minimum.html)

## Original design synthesis

Use a two-layer component contract:

1. **Meaning layer:** native link for navigation, native button for an action; visible title/action wording; selected state also expressed in text or geometry; logical source order.
2. **Art-direction layer:** shadow, gradient, tint and blur are optional differentiation. If removing them makes two actions indistinguishable, repair the meaning layer before adding another effect.

For a project card, keep the project title as the actual link. Do not create a second invisible full-card interactive overlay that competes with nested actions. For a toolbar, use a visible border only where loss of the elevation cue would otherwise erase grouping. These are proposed patterns, not measured usability findings.

Do not globally set forced-color-adjust to none to preserve a brand palette. If an exceptional color-dependent specimen is later considered, isolate it and review the alternative explanation and actual user palette behavior. This package supplies no approved exemption.

## Original, unexecuted CSS sketch

The selectors below target native HTML controls. This is a minimal proposal, not a drop-in accessibility certification. The normal theme must independently provide valid contrast and visible focus.

```css
.project-link:focus-visible,
.project-action:focus-visible {
  outline: 2px solid currentColor;
  outline-offset: 3px;
}

@media (forced-colors: active) {
  .project-link {
    color: LinkText;
    text-decoration: underline;
  }
  .project-action {
    color: ButtonText;
    background: ButtonFace;
    border: 1px solid ButtonText;
  }
  .project-link:focus-visible,
  .project-action:focus-visible {
    outline-color: Highlight;
  }
  .floating-navigation {
    background: Canvas;
    color: CanvasText;
    border-block-end: 1px solid CanvasText;
    backdrop-filter: none;
  }
}
```

An explicit system-color outline is a candidate to inspect across palettes, not proof of sufficient contrast. Check clipping by overflow containers and adjacency to imagery. A decorative card border may be unnecessary; avoid turning every editorial module into an identical box.

## Troubleshooting and future acceptance contract

| Symptom | Inspect first | Proposed correction | Required future check |
|---|---|---|---|
| Action disappears when shadows vanish | Native element and visible label | Add a meaningful boundary where needed | Inspect light, dark and custom forced palettes |
| Gradient-selected state vanishes | Whether selection has another cue | Add visible text/shape state | Keyboard and selected-state inspection |
| Focus visible before scrolling but lost below sticky header | Actual overlap at zoom and responsive heights | Reduce sticky footprint; investigate scroll padding with real dimensions | Tab and Shift+Tab through entire page |
| Light/dark toggle seems correct but forced colors breaks | Whether forced-colors mode was actually enabled | Test the separate mode | Record OS/browser/version and palette |
| Computed CSS looks right but screenshot differs | Paint-time adjustments and overlay stacking | Inspect rendered result, not just authored values | Manual visual review plus accessibility tooling |

Future implementation should test keyboard operation, 200% zoom, narrow layouts and actual OS forced-color settings. DevTools emulation can aid diagnosis but should not be represented as testing every user palette. No test in this table was executed in this run.

## Adoption, compatibility and limitations

This CSS-only idea has no new runtime package, hosting service, MCP integration or operating fee. Existing React/Next markup can use the selectors, but framework integration was not exercised. Prefer native controls over a custom widget library when the requirement is ordinary links/actions; a library does not remove palette testing obligations.

No repositories, fonts, images or assets are redistributed. This is original analysis with short paraphrases and links, not a licence grant for third-party material. There is no performance benchmark, accessibility conformance claim, production modification or measured conversion result. Re-check current browser support and the specification edition before implementation. Publication approval means bounded documentary QA only.

