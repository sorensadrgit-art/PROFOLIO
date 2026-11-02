# Flubber: a shape morph is not a topology-preserving SVG transition

## Decision and bounded question
**When is Flubber a useful open-source portfolio effect, and when should a compound SVG remain unchanged?** Shortlist one project, **veltman/flubber**, for a deliberately simple, filled silhouette morph. Do not treat it as an arbitrary SVG animation engine. This is an original documentary assessment, not an installation or deployment recommendation.

The current catalog and seven existing creative packages were read first: carousels, lightboxes, command menus, text splitting, OGL flowmaps, Rough Notation and Perfect Freehand. This question adds geometry-preservation boundaries rather than repeating those packages. The topic directory contained those seven runs and its README, with no unfinished unindexed package observed.

## Source findings
- Flubber provides JavaScript shape interpolators, not a clock. Inputs are SVG path data or coordinate rings; the application supplies progress and draws the output. SVG strings are the default result; point arrays are optional. The README warns that a compound path loses everything except its first outer shape in interpolation. [S1]
- Static source inspection explains the boundary: the polygon parser stops at a later move or a closing command; the curve approximation selects the first split path. It does not preserve compound-path topology. Browser path measurement is attempted when a document exists, otherwise the library uses its path-properties dependency. [S5]
- A separate endpoint optimization returns the original input string when progress is less than 0.0001, or the original target string when remaining progress is less than 0.0001. This only applies to string output and the corresponding string endpoint. Intermediate output uses normalized rings. [S4]
- The manifest identifies version **0.4.2**, with CommonJS/browser build and module entries. Dependencies are d3-array, d3-polygon, earcut, svg-path-properties, svgpath and topojson-client, with older declared major ranges; the observed build/test stack includes Rollup, Bublé, Babel and Tape. This is manifest evidence, not a current dependency audit. [S2]
- The inspected master head is **0cadadf3eb15cd5b2ec7c45c70601ff88b76a6c3**, dated **2019-10-16T01:49:53Z**. This indicates an old observed mainline snapshot, not proof the maintainer will never update it. The latest-release endpoint returned 404; release date is unknown. npm metadata was not retrieved successfully, so registry latest, package integrity and release-to-commit equivalence remain unverified. The README browser snippet names 0.3.0 while the manifest says 0.4.2. Do not adopt its example version without checking the intended artifact. [S1,S2,S7]
- The actual LICENSE is MIT, copyright Noah Veltman 2017: retain its copyright and permission notice with copies or substantial portions; warranty is disclaimed. This does not license unrelated artwork, logos or third-party demo assets. Public visibility alone is not permission. [S3]

## Original synthesis: endpoint screenshots can conceal information loss
The endpoint fast path and first-path normalization together imply a misleading acceptance test: a compound icon can look intact at the beginning and end while its intermediate geometry omits subpaths. This is a **static-source inference, not an observed rendered defect**. An endpoint-only screenshot comparison therefore cannot establish suitability. The highest-risk candidates include lettering with counters and compound brand marks. Splitting subpaths is not by itself a solution to holes, fill rules or semantic correspondence.

The useful pattern is a disposable decorative silhouette, with the meaningful label and controls outside the morph. Treat the shape as presentation derived from application state, not as the authoritative vector document. This separates a reversible animation from navigation, focus and content.

## Untested integration plan and future application
Proposed application: a portfolio category badge that changes between two original, simple silhouettes while its adjacent category text updates independently.
1. Audit both inputs for a single intended closed filled contour; normalize their coordinate system. Keep original SVGs as fallback.
2. After verifying the desired registry artifact, install an explicitly pinned flubber version in an ordinary JavaScript build. No installation was performed here. The manifest's module entries do not certify any particular React, Next.js, Vite, TypeScript or SSR version.
3. Create the interpolator when inputs change, not on every animation frame. Drive progress through one owned animation lifecycle; cancel scheduled callbacks on teardown and prevent overlapping transitions.
4. Begin with the documented maxSegmentLength default of 10; smaller values trade additional geometry work for smoothness. The README's pixel interpretation assumes no transforms. Evaluate the actual output scale before tuning. [S1]
5. Provide a static state when reduced motion is requested and keep keyboard/focus behavior independent of decorative geometry. These are proposed requirements, not accessibility test results.
6. Inspect progress near both endpoint thresholds and at several interior points, including holes, curved outlines, mismatched coordinate scales, rapid state changes and unmount. Test the real browser and framework build. Establish measured frame and memory budgets before enabling many simultaneous morphs.

## Alternatives, costs and operational boundaries
For marks whose exact contours or holes matter, prefer retaining the original SVG and changing opacity/visibility instead of generating intermediate geometry. For no motion requirement, a static icon is the simplest alternative. These are original design alternatives, not a comparative library benchmark. The README's historical third-party pricing statements are not used as current facts.

This is a local library integration, not a hosted API or MCP server. No account, API key or MCP connection is needed for the proposed client-side use. Existing application hosting still incurs its normal costs; license fee is not required under MIT. Bundle transfer, geometry preparation, repeated rendering, dependency review and maintenance ownership remain engineering costs. No dollar estimate or performance claim is justified by this review.

The official README links an [SVG demo](https://veltman.github.io/flubber/demos/basic-svg.html) and a [Canvas demo](https://veltman.github.io/flubber/demos/vanilla-canvas.html); their execution was not tested. Assets were not copied. A shipping gate should include artifact/dependency review, actual browser rendering, interruption cleanup and reduced-motion/keyboard checks. Runtime, performance, accessibility, security and integration certification are explicitly absent.

## Provenance
Retrieved/evaluated 2026-11-02; scheduled slot 2026-11-02T08:18:00Z. S1–S6 are first-party files pinned to the observed commit; S7 is GitHub commit metadata. Source claims, original inference and untested application proposals are separated above. See sources.json, repos.json and qa.md for evidence and review scope.
