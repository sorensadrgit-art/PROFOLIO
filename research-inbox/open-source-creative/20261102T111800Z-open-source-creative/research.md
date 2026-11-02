# COBE v2: a globe demo is not an animation lifecycle contract

## Decision and question
**Can COBE provide a small original portfolio globe without inheriting a stale animation example?** Shortlist one project, shuding/cobe, for an optional location/story illustration, not a full geographic information system. Keep a text list or static original graphic as the baseline. Adoption is conditional on checking the actual package artifact and lifecycle behavior.

The live catalog, topic directory and eight prior creative packages were read first. They cover carousels, lightboxes, command menus, typography splitting, flowmaps, annotations, freehand geometry and morphing. No unindexed run directory or same-run artifact was observed. COBE is absent from catalog entries. This run adds a concrete v2 API/example mismatch and release-versus-main cleanup distinction, not another generic renderer report.

## Source findings
- COBE renders a WebGL globe with geographic markers, arcs and optional DOM anchor bindings. The README links the maintainer demo at https://cobe.vercel.app; it was not operated. Its size/performance slogans are not measured evidence. [S1]
- The README quick start supplies an onRender callback. However, the inspected main implementation never reads that option or schedules requestAnimationFrame, and its TypeScript options omit onRender. It renders during initialization, texture load and explicit update calls; the public API is update and destroy. Tagged 2.0.1 also exposes explicit update and does not consume onRender, but lacks main's texture-load update({}) call. This is a source-level discrepancy, not an executed failure report. [S1,S4,S5,S6]
- The implementation requests WebGL2 then WebGL1. If neither context exists it returns no-op update/destroy functions. Receiving an object is therefore not proof of successful rendering. Marker instancing uses a WebGL1 extension when available; fallback behavior has not been validated. [S4]
- Width and height are multiplied by devicePixelRatio internally, including size updates. The README's example uses 1000 dimensions with DPR 2 for a 500 CSS-pixel canvas. Do not additionally pre-scale dimensions by DPR without deciding the intended backing resolution. [S1,S4]
- The source manifest says version 2.0.1, ESM and TypeScript declarations, with no declared runtime dependencies. JavaScript and GLSL drive the implementation; esbuild, GLSLX, Terser, Next.js and React appear in development tooling, not a React peer compatibility contract. The build injects shader/texture constants: raw src/index.js is not a standalone drop-in browser module. [S2,S4,S8]
- GitHub latest release is tag 2.0.1, published 2026-03-19T17:02:49Z. Observed main commit 7e94076fa8c38707f4c9cd114ff4f7c5c880971e is dated 2026-09-22T07:38:35Z. Its destroy path clears image.onload and deletes the texture; tagged 2.0.1 lacks those two cleanup steps. Main fixes must not be attributed automatically to the release or npm artifact. [S4,S6,S7,S9]
- The actual root licence is MIT, copyright 2021 Shu Ding; retain copyright/permission notices with copies or substantial portions, and preserve the warranty disclaimer. The README separately credits a world-map asset and inspirations. This review has not independently cleared those assets' upstream rights, so code licensing is not a blanket clearance for copied map/demo media. [S1,S3]

## Original synthesis: own the loop and verify the artifact
The README/source mismatch makes copying the rotating-globe snippet an unreliable integration plan. Treat explicit updates as the inspected v2 contract, then verify the chosen published artifact before implementation. Similarly, a manifest version shared by main and a tag does not prove identical cleanup. This package reports source differences rather than claiming a released memory leak or a measured fix.

A globe object, a visible globe, loaded map texture and working DOM labels are separate acceptance observations. A no-op API can satisfy an object-existence check while leaving a blank canvas. Keep useful content outside this enhancement.

## Untested application recipe
Proposed original application: an editorial case-study globe with a few author-chosen public project regions and adjacent accessible project links; no visitor geolocation is needed.
1. Verify a pinned published cobe artifact against the inspected API and cleanup behavior; only then install in a separately authorized prototype. Do not execute repository debug scripts or disable browser security.
2. Initialize after the canvas exists on the client. Give the globe its own mount container: the implementation inserts a wrapper and moves the canvas. destroy deletes its graphics resources and anchors but does not unwrap the canvas in the inspected index source. The delegated anchor cleanup removes anchor nodes and its style element, not the wrapper. Test framework teardown/remount rather than assuming DOM ownership compatibility. [S4,S10]
3. Supply one deliberate logical-size/DPR pair; cap detail based on measured device budgets, not a marketing size claim.
4. Own one external animation callback if rotation is desired, drive update, and cancel that callback before destroy. Prefer a stationary state for reduced motion; avoid background animation when not useful. These are recommendations, not built-in feature claims.
5. Keep labels and navigation in ordinary DOM. CSS anchor positioning and globe visibility are visual mechanisms, not keyboard/focus semantics or a guarantee that hidden links are noninteractive. Check target browsers and provide an unanchored list fallback.
6. Test absent WebGL, delayed texture, resize, rapid remount, reduced motion, keyboard navigation and teardown before adopting. Check remaining wrapper nodes, callbacks and graphics resources with the real artifact.

Alternative patterns: a static SVG illustration for fixed storytelling, a plain linked location list for navigation, or a separately evaluated full mapping stack when geographic accuracy/layers are requirements. These alternatives have not been benchmarked.

## Integration, operating costs and limitations
The proposed use is an application-side browser library with no required hosted API, account, API key or MCP connection. A future backend for location records would be separate scope. Browser GPU work, bundle transfer, engineering, hosting bandwidth and maintenance still cost resources; no price or performance measurement was made. React/Next.js compatibility, CSS anchor support across the target matrix, artifact integrity and upstream map-asset rights remain adoption gates.

npm metadata retrieval was attempted but the endpoint was inaccessible through the available web tool; registry latest, integrity and release-to-artifact equivalence remain null. No package was installed, built or executed. No browser, accessibility, runtime, performance, dependency-security or integration tests passed because none were performed. Documentary QA is not deployment permission.

## Provenance
Scheduled 2026-11-02T11:18:00Z; retrieved and evaluated 2026-11-02. Pinned primary files, Git blob hashes and metadata URLs are in sources.json. Original inferences and untested proposals are labeled separately. No repository, protected artwork or substantial source text is republished.

