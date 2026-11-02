# Hand-drawn emphasis needs an owner, not just an SVG

Run: `20261102T021800Z-open-source-creative`  
Scheduled: 2026-11-02T02:18:00Z. Evaluated from 02:13 UTC on 2026-11-02, before the scheduled slot; identifier retained.

## Decision — original synthesis

Evaluate **Rough Notation** for a small number of decorative editorial annotations, not as a semantic status system or a general animation engine. Keep native text emphasis/CSS as the baseline. This is a one-project discovery, not adoption approval.

The live catalog and five prior creative reports were read first: rails, lightboxes, command menus, text splitting and OGL flowmaps. The topic directory contained no current-run or unfinished package. This report adds annotation geometry, teardown and meaning rather than repeating text splitting.

## Source findings

Rough Notation creates hand-drawn annotations using RoughJS. Its documented API includes underline, box, circle, highlight, strike-through, crossed-off and brackets, with optional animation and multiline inline-text treatment. It inserts an SVG sibling, so the README warns about structures such as tables. Repeated `show()` updates geometry; `hide()` hides rather than unlinks, while `remove()` unlinks. Changing annotation type requires a new object. Groups sequence drawing rather than establish semantic relationships. [R1]

The inspected implementation creates an absolutely positioned SVG with pointer events disabled. Highlight mode can change a statically positioned target's inline position to relative. `remove()` removes the SVG and detaches resize listeners/observation; it does not restore that target style. Resize handling is delayed 400 ms and redraws when measured rectangles differ. Window resize and a target ResizeObserver are the observed triggers, not a subscription to every possible layout change. These are static code observations, not measured failures. [R4]

The source manifest reports **0.5.1**, CommonJS/ES-module entries and TypeScript declarations. TypeScript/Rollup are build tooling, and RoughJS ^4.3.1 is a development dependency used by the source import; absence of a runtime dependencies field does not mean the implementation contains no third-party code. The test script is a placeholder that exits unsuccessfully; this is not proof that every form of testing is absent. [R2,R4]

The latest observed default-branch commit is **668ba82ac89c903d6f59c9351b9b85855da9882c**, dated **2020-10-30**. The latest-release API returned 404 and the npm registry URL was inaccessible through the web tool: release date and published artifact version remain unknown. Old observed activity warrants a compatibility gate, not a claim of abandonment. [R6]

## Reuse, integration and costs

The actual pinned LICENSE contains MIT terms, requiring copyright and permission notices with copies or substantial portions. This does not grant rights to independent demo media, branding or fonts. No assets or complete repository were copied. [R3]

The maintainer site demonstrates several marks and nonanimated rendering; demos were read, not operated. Its small-size claim was not reproduced and is not a bundle guarantee. [R5]

A future authorized prototype would verify an available pinned `rough-notation` package, bundle its entry point, and initialize after the browser target exists. No install occurred. The core is a DOM/SVG utility, not a React component; third-party wrappers listed by the README were not evaluated. Exact React/Next.js, hydration and strict-mode compatibility remain untested. No backend, account, API credential or MCP connection is needed for this proposed browser-only use. Hosting bytes, layout work and engineering maintenance are costs; no paid service is required by the reviewed setup and no monetary or performance measurement was made.

## Original adaptation proposal — untested

1. Preserve the sentence and its semantic emphasis without the annotation. A crossed-out visual must not be the only indication that an option is unavailable or rejected.
2. Use an explicit inner wrapper for the annotated phrase. Give one component ownership of the library object and inserted sibling; avoid framework reconciliation of unmanaged sibling nodes.
3. Initialize after relevant font/content layout is ready. After a known position-only layout change, refresh deliberately rather than assuming target-size observation catches it. Coalesce work; do not redraw continuously without evidence.
4. Keep movement on a shared wrapper where practical, then test alignment. Do not infer support for transformed ancestors from a screenshot of the static demo.
5. On teardown call `remove()`, retire callbacks, and restore only application-owned styles whose prior values were recorded. Do not blindly erase a style another component may have changed.
6. Disable decorative drawing animation when reduced motion is requested. Keep the nonanimated or CSS-only fallback useful if the library fails.

Original future application: restrained annotation of one sentence in an editorial case study, with a real text label explaining the result. A second use is a static bracket around a short quotation, preserving readable content when SVG is absent. Both remain proposals.

Alternatives: CSS text decoration/background for lower lifecycle complexity; an author-controlled static SVG for fixed artwork; RoughJS directly only if custom geometry justifies additional ownership. These alternatives are not separately benchmarked shortlists.

## Acceptance plan and limitations

Before adoption, compare the CSS baseline with one pinned candidate using identical original text. Test font replacement, content updates, position-only changes, multiline wrapping, zoom, transformed/scrolling ancestors, table wrappers, reduced motion, forced colors, screen-reader reading and repeated mount/unmount. Inspect residual SVGs, listeners and target inline styles. Measure actual layout and bundle costs.

**No installation, browser, runtime, accessibility, performance, dependency-security or integration tests were performed.** Documentary approval is not permission to deploy or compatibility certification. Source package metadata is not verified npm artifact metadata.

## Sources
- [R1] [Rough Notation README](https://github.com/rough-stuff/rough-notation/blob/668ba82ac89c903d6f59c9351b9b85855da9882c/README.md)
- [R2] [Rough Notation source package manifest](https://github.com/rough-stuff/rough-notation/blob/668ba82ac89c903d6f59c9351b9b85855da9882c/package.json)
- [R3] [Rough Notation MIT licence](https://github.com/rough-stuff/rough-notation/blob/668ba82ac89c903d6f59c9351b9b85855da9882c/LICENSE)
- [R4] [Rough Notation annotation implementation](https://github.com/rough-stuff/rough-notation/blob/668ba82ac89c903d6f59c9351b9b85855da9882c/src/rough-notation.ts)
- [R5] [Rough Notation official demonstrations](https://roughnotation.com/)
- [R6] [Rough Notation latest observed default-branch commit](https://api.github.com/repos/rough-stuff/rough-notation/commits?per_page=1)
