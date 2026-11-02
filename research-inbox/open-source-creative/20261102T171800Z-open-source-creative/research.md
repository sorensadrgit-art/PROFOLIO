# Troika text: a sync callback is not a general readiness promise

Run: `20261102T171800Z-open-source-creative`  
Scheduled: 2026-11-02 17:18 UTC / 12:18 America/New_York. Evaluated: 2026-11-02.

## Question and shortlist decision

When a cinematic portfolio changes a Three.js title, what actually establishes that its new geometry is ready? Evaluate **troika-three-text 0.53.1** as one candidate for scene-integrated typography, with an explicit asynchronous update and resource-ownership contract. Keep ordinary HTML text for essential information. This is an original recommendation, not approval to install or a tested compatibility claim.

Intake read the live catalog, topic directory and ten prior creative reports. Those covered rails, image viewers, command menus, split typography, flowmaps, annotations, freehand outlines, morphs, globes and tilt. This adds asynchronous SDF text readiness. No same-slot artifact or unindexed topic directory was found. The existing COBE global/topic index discrepancy was preserved.

## Primary-source findings

**Readiness.** The pinned `Text.sync` implementation does work only when `_needsSync` is true. A callback passed when nothing needs synchronization is not called. During an existing sync, a changed request queues callbacks; completion applies the older result, initiates queued synchronization, and then emits the older completion event. Therefore, observing one `synccomplete` does not by itself prove that the newest application request has settled. `textRenderInfo` may be null or temporarily stale. These are source-reading findings, not reproduced race tests. [T2]

**Disposal.** `Text.dispose()` disposes instance geometry. It does not dispose the base material; the source deliberately leaves that ownership to callers and arranges derived-material cleanup when the base is disposed. The inspected method is not an asynchronous cancellation API. Do not interpret a disposal call as proof that outstanding callbacks cannot run. [T2]

**Font preparation.** `preloadFont` passes requested font, character sequences, glyph size and language into text processing. Array entries become separate sequences. Preparing individual letters does not necessarily prepare ligature sequences; preload a representative corpus instead of assuming an alphabet covers every title. This is a cache-warming facility, not synchronization of a particular Text instance. [T3]

**Purpose and integration.** Troika renders signed-distance-field text in Three.js. Its guide documents font parsing, fallback-font fetching, worker processing, curved text and caret/selection geometry. Fonts may be TTF, OTF or WOFF, not WOFF2. Worker-disabled processing remains asynchronous. Its documented fallback data normally comes from a CDN; self-hosting is possible. Demos include R3F and editing examples, but were not operated. [T1]

**Package and maintenance.** The pinned manifest declares JavaScript ESM/UMD entries, Three.js peer `>=0.125.0`, and dependencies bidi-js, troika-three-utils, troika-worker-utils and webgl-sdf-generator. These ranges are not a promise of compatibility with every later renderer or framework. npm metadata identified 0.53.1 as latest, published 2026-11-02T15:06:25.483Z, with gitHead `9c89d51f828f94a21e33d6f24b38128d78014d5f`. Its commit is dated 2026-11-02T15:01:47Z. The changelog lists example-link fixes and a BatchedText color-resolution optimization; no speed improvement was measured here. GitHub's latest-release endpoint returned 404, so a GitHub release publication timestamp remains unknown. [T4,T6,T7,T8]

**Licence.** The actual pinned repository licence is MIT, with ProtectWise and Jason Johnston copyright notices. Retain copyright and permission notices for distributed copies/substantial portions; warranty is disclaimed. This does not grant rights to unrelated demo imagery, branding or font files. Each selected font needs its own licence review. No demo assets or repository bulk copy were made. [T5]

## Original synthesis: distinguish a request, a processed result and a visible frame

The following is an untested application contract, not a supplied library guarantee:

1. Give the title controller one owner and a monotonically increasing request generation. Record text, font and layout settings together; avoid independent components racing to write them.
2. Serialize layout-affecting updates, coalescing rapid changes to the newest desired state. Keep an application record of the last successfully processed settings rather than depending on private Troika flags.
3. Treat a no-change request as a separate case. Do not blindly wrap every `sync(callback)` in a promise: that promise can remain unresolved when no sync is required. Transform-only changes also need a frame, not necessarily a typography rebuild.
4. On a completion callback, compare the captured generation with the current desired generation and the controller's alive state. Publish geometry-dependent placement only for the intended result. A callback guard protects application actions; it does not cancel Troika's internal work.
5. Schedule a render after the accepted completion in a demand-rendered scene. Processing geometry and displaying a frame are different milestones. Keep the HTML label useful while the decorative scene catches up or fails.
6. On route exit, stop new requests, detach owned listeners and remove the mesh. Dispose instance geometry and only those base materials the controller owns exclusively; do not destroy a material still shared by another label. Test teardown during in-flight work before adoption.

An original application is a bilingual case-study heading that changes with project selection while an HTML title remains the accessible source of meaning. Curvature, outlines and scene lighting can supply visual identity without turning the canvas into the only route to content. The accessibility outcome is untested.

## Alternatives, operations and costs

A normal HTML/CSS heading is the low-complexity alternative when text need not occupy 3D space. A pre-rendered texture trades dynamic text flexibility for simpler runtime layout, but still needs licensed content and equivalent readable text. A framework wrapper may reduce imperative setup, but its version and lifecycle contract require separate review; none was selected or tested here.

A later authorized evaluation would pin `troika-three-text@0.53.1`, resolve a compatible Three.js version and lock dependencies. It runs inside the frontend rather than requiring a new server, API account or MCP connection. Review CSP/worker policy, font fetch origins, loading failure behavior and hosting headers in the actual application. Avoid relaxing security policy merely to make a demo run. No installation, new connection or paid service was used.

Operational cost includes JavaScript transfer, font bandwidth, glyph generation, atlas memory and engineering time. Costs and latency are unmeasured. Preload only the intended corpus and size configurations; benchmark larger glyph sets and repeated language changes on representative hardware rather than assuming off-main-thread work is free.

## Proposed acceptance fixture — not executed

- Send A, then B while A is processing; record generations, completion events and displayed result. An old completion must not publish B's layout prematurely.
- Repeat identical settings and make a transform-only update. Neither path should wait forever on a callback that will not fire.
- Disable continuous rendering, change the title, and verify an explicit post-completion frame presents it.
- Preload letters only, then use a ligature and missing-script characters. Observe actual extra font/glyph requests; compare with a representative preload corpus.
- Exit during font loading; verify stale callbacks do not mutate application UI, measure retained resources, and check separately shared base materials survive.
- Exercise failed font fetches, restrictive CSP, zoom, keyboard navigation and assistive technology against the HTML baseline.

No runtime, visual, integration, performance, accessibility or dependency-security test was performed. Repository source was read, not executed; npm metadata does not prove tarball contents identical because the archive was not inspected. Documentary QA is recorded separately.

## Sources

See `sources.json` for pinned URLs, retrieval dates and limitations; `repos.json` contains the one-project shortlist. Findings are attributed above; controller design and acceptance fixtures are original, untested synthesis.
