# A freehand outline is not an editable drawing document

Run: 20261102T051800Z-open-source-creative. Scheduled 2026-11-02 05:18 UTC; evaluated 2026-11-02 after resuming the same initially blocked run.

## Decision — original synthesis, untested

Shortlist **Perfect Freehand** for an original, optional sketch layer in a portfolio case study. Its useful boundary is geometry generation: keep input samples and application state separate from the rendered outline. Choose plain SVG artwork instead when visitors do not need to draw. This is a one-project discovery, not a drawing-editor product recommendation.

The live catalog and six prior creative packages were read first. They cover carousels, lightboxes, command menus, text splitting, flowmaps and Rough Notation. This package adds user-authored freehand stroke data and pressure/finalization ownership rather than another annotation overlay. No unfinished same-run remote directory was observed.

## Source findings

Perfect Freehand is written in TypeScript. `getStroke` accepts input points and returns outline polygon points; the inspected implementation composes `getStrokePoints` and `getStrokeOutlinePoints`. Rendering, document history and persistence are outside that function. [P1,P4]

The README describes simulated pressure by default. To use measured stylus pressure, it instructs passing pressure with each point and disabling `simulatePressure`. Supplying pressure alone is therefore not the documented selection of real-pressure mode. [P1]

There is a concrete default discrepancy: the README options table lists size 8 and last true, whereas inspected `getStrokePoints` destructures size 16 and last false. The composition forwards the options unchanged. Set critical options explicitly rather than relying on the README table. This source observation is not proof about every published artifact. When last is true, the implementation uses the actual final input position instead of the interpolated one. [P1,P4,P5]

The source package manifest says 1.2.3 and supplies ESM, CommonJS and TypeScript declaration exports. It declares development tooling, but no runtime dependencies or React peer requirement. That does not certify a particular framework/bundler combination. [P2]

GitHub's latest release endpoint returned v1.2.3, published 2026-02-01. Release notes describe maintenance/build changes and claim no API break; those are maintainer statements, not reproduced tests. The latest observed default-branch commit is dated 2026-02-01 and changes a port link. A later branch commit is not automatically part of the release. The npm metadata endpoint was inaccessible, so registry version and artifact equivalence remain unknown. [P6,P7]

The actual root LICENSE grants MIT reuse subject to retaining its copyright and permission notices with copies or substantial portions; warranty is disclaimed. The README's sponsorship invitation is not a mandatory fee in that licence. This does not establish rights to third-party icons, artwork, fonts or examples' media. [P3,P1]

## Integration, costs and risks

A later authorized evaluation can install a pinned package after checking the registry artifact, import `getStroke`, and render the result with SVG or Canvas. The README provides rendering recipes and links a [maintainer demo](https://perfect-freehand-example.vercel.app/); the demo was not operated. This frontend use needs no API key, MCP service or separate hosted backend. Saving drawings to a backend would be a separate application design. No software was installed. [P1,P2]

Costs are bundle transfer, geometry calculation, retained sample storage and application engineering; no paid hosted service is required by the reviewed API. Monetary, latency and memory costs were not measured. Long strokes and frequent recomputation deserve profiling; do not promote the release's optimization wording into a speed guarantee.

The README also warns that polygon outlines can self-cross and presents optional flattening with another package. Do not assume generated outlines are suitable for cutting, fabrication or geometry export without validation. That optional dependency needs its own review. [P1]

## Original adaptation contract — recommendations to test

1. Preserve a stroke record containing stable ID, raw points, pressure mode, explicit rendering options, coordinate-space convention and generator version. Treat outline points/path data as derived display data. If only the outline is stored, later pressure or smoothing edits cannot reliably recover the original samples.
2. Keep pointer capture, cancel/commit state, undo and persistence in the application. A geometry function is not an interaction lifecycle. Restrict any scroll-blocking drawing surface to a clearly entered sketch mode; ordinary portfolio reading must still work.
3. Use explicit size and last values. Preview an in-progress stroke with the intended mode and regenerate on accepted completion. Do not label pointer cancellation as a completed saved drawing.
4. Convert input and output consistently through resize, scroll and zoom. Test rather than assume page coordinates match SVG viewBox coordinates.
5. Preserve meaningful text and normal controls outside decorative sketches. Offer a non-drawing route to any important action; a sketch should not be the only way to explain a project or navigate.
6. On route teardown retire input handlers and pending rendering work, without deleting saved stroke records. Keep any network save receipt separate from visual completion.

Original future application: a local-only “draw your route” overlay on an architecture case study, resettable without changing the underlying content. Another is an authoring tool that exports original decorative marks as static SVG, removing runtime drawing dependency from the published portfolio. Neither is implemented here.

Alternatives: static original SVG for fixed artwork; native Canvas strokes for simpler uniform-width input; Rough Notation for emphasis tied to existing DOM text, as covered in the prior package. These alternatives are not benchmarked comparisons.

## Proposed acceptance and limitations

Test stylus versus mouse, explicit simulated/measured pressure, tap/two-point strokes, cancel versus completion, repeated mount/unmount, long strokes, transformed coordinate spaces, keyboard-accessible mode controls and a useful no-drawing fallback. Compare retained source samples with exported geometry and verify rendering after reload. Check the exact installed version against the cited source discrepancy.

**No installation, browser, runtime, accessibility, performance, dependency-security or integration tests were performed.** Documentary QA is not deployment approval. npm artifact equivalence, exact framework compatibility and third-party media rights remain unverified.

## Sources
- [P1] [Perfect Freehand README](https://github.com/steveruizok/perfect-freehand/blob/176e00f2399f4969e1b0965c5921d96a3e50ce9f/README.md)
- [P2] [Package manifest](https://github.com/steveruizok/perfect-freehand/blob/176e00f2399f4969e1b0965c5921d96a3e50ce9f/packages/perfect-freehand/package.json)
- [P3] [Actual MIT licence](https://github.com/steveruizok/perfect-freehand/blob/176e00f2399f4969e1b0965c5921d96a3e50ce9f/LICENSE)
- [P4] [getStroke composition](https://github.com/steveruizok/perfect-freehand/blob/176e00f2399f4969e1b0965c5921d96a3e50ce9f/packages/perfect-freehand/src/getStroke.ts)
- [P5] [getStrokePoints implementation](https://github.com/steveruizok/perfect-freehand/blob/176e00f2399f4969e1b0965c5921d96a3e50ce9f/packages/perfect-freehand/src/getStrokePoints.ts)
- [P6] [Latest release metadata](https://api.github.com/repos/steveruizok/perfect-freehand/releases/latest)
- [P7] [Latest observed default-branch commit](https://api.github.com/repos/steveruizok/perfect-freehand/commits?per_page=1)
