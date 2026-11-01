# PROFOLIO 3D & WebGL Engineering Research — Runtime Resilience Update

- Run ID: 20261101T064013-webgl-resilience-02
- Topic: 3D and WebGL Engineering
- Research date: 2026-11-01
- Research question: How should PROFOLIO keep its 3D experience available and avoid GPU/renderer leaks across React hide/unmount cycles, WebGL context loss, WebGPU device loss, background tabs, and mobile resource pressure?
- Relationship to prior research: Follow-up to `20261101T053112-3d-webgl-engineering-01`. The earlier package established the asset pipeline, renderer migration risks, and baseline fallbacks. This package adds current R3F 9.8.x release findings, a detailed loss/recovery model, and a lifecycle test plan. It does not repeat the earlier asset-optimization survey.

## Executive summary

The highest-value update is that React Three Fiber's stable 9.8.x line has recently received meaningful lifecycle fixes, while v10 remains a pre-release with a more aggressive WebGPU-first direction. For PROFOLIO, the practical recommendation is to pin and validate the 9.8.x stable line before considering v10, then make the canvas an optional enhancement around a persistent DOM/poster fallback.

Four findings matter most:

1. **R3F 9.8.1/9.8.2 materially improve renderer lifecycle.** The official releases document correct disposal of renderers created by R3F, preservation of Canvas roots while React Activity hides them, fixes for portal memory leaks, more reliable device-pixel-ratio updates, and correct removal behavior with demand rendering. This is new evidence compared with the earlier research snapshot. [S1][S2]
2. **WebGL context loss and WebGPU device loss are different recovery paths.** WebGL emits canvas events and requires the application to rebuild invalid GPU resources after restoration. WebGPU exposes `GPUDevice.lost`; every object owned by the lost device becomes unusable and must be recreated with a replacement device. [S6][S7][S8][S9][S10]
3. **A console message saying “Context Lost” is not automatically a production failure.** R3F intentionally releases renderer resources during unmount. Its v9.8.1 release notes specifically describe disposing a renderer and then losing its context as part of teardown. Diagnose unexpected loss separately from expected component teardown. [S1][S13]
4. **Fallbacks and lifecycle policy belong outside the canvas.** Keep the DOM content and a static poster available, use the R3F fallback/error-boundary patterns for unsupported or failed rendering, lazy-mount near the viewport, and pause nonessential work while the document is hidden. [S4][S14][S15]

## 1. Fresh framework changes: R3F 9.8.x and v10 alpha

### Verified release facts

- **R3F 9.8.1**, released 2026-09-24, fixed cases where a renderer was not disposed when Canvas unmounted and improved React Activity/Suspense visibility handling. The release notes state that an Activity-hidden Canvas keeps its renderer and scene state, then releases the renderer if it is actually removed. [S1]
- **R3F 9.8.2**, released 2026-10-15, fixed portal root-state leaks after unmount, automatic DPR changes when moving between displays or zooming, and stale objects under `frameloop="demand"`. Its release notes warn that a Canvas `dpr` prop can override imperative `setDpr` changes when Canvas rerenders; avoid mixing both control paths without a deliberate policy. [S2]
- **R3F 10.0.0-alpha.6**, released 2026-10-17 as a pre-release, makes WebGPURenderer the default renderer, routes GLSL-heavy apps to `@react-three/fiber/legacy`, separates WebGPU-specific hooks into `@react-three/tsl`, and introduces a multi-canvas sharing model. It also documents a fixed 1/60 physics phase timestep. These are alpha-release facts, not a production recommendation. [S3]

### PROFOLIO recommendation

- Treat **R3F 9.8.2** as the latest stable candidate identified in the release list at retrieval, subject to checking the actual project lockfile and peer dependencies before adoption.
- Do not upgrade to v10 alpha solely to obtain WebGPU. First inventory custom GLSL, Drei dependencies, post-processing, renderer-specific types, and any code that assumes WebGLRenderer.
- Keep a renderer adapter boundary so the page, controls, asset manifest, and fallback remain renderer-neutral.
- Re-run the lifecycle test matrix below after any R3F, React, or Three.js upgrade.

## 2. WebGL context loss: event-driven restoration

### Verified platform behavior

- `webglcontextlost` is fired on the canvas and does **not bubble**. Attach the listener directly to the canvas, not only to a distant document-level ancestor. [S6]
- Khronos' WebGL guidance says to call `event.preventDefault()` when the application intends to handle restoration, stop the render loop while the context is lost, and rebuild WebGL state/resources after `webglcontextrestored`. [S10]
- After restoration, pre-loss textures, buffers, shaders, programs, framebuffers, renderbuffers, and state are invalid or reset; they must be recreated. [S7][S10]
- Context loss can result from shared GPU pressure, long-running GPU work, switching graphics processors, or driver updates. It is not necessarily evidence of a bug in the scene itself. [S9]
- `webglcontextcreationerror` reports failure to create a context and may expose a platform-dependent `statusMessage`; treat that message as diagnostic text, not a stable cross-browser error code. [S8]

### Recovery policy

1. Keep the static poster/DOM experience visible behind or in place of the canvas.
2. On loss, mark the scene unavailable, call `preventDefault()` if the app is handling restoration, pause frame work, and ignore stale asynchronous completions from the old generation.
3. Wait for the browser's `webglcontextrestored` event. Do not assume a fixed restoration delay.
4. Recreate application-owned raw GL resources and re-establish application state. Let Three.js manage resources it owns; do not create a second renderer over the same canvas as an ad hoc recovery shortcut.
5. Re-load or re-bind custom resources only after the renderer is usable, then reveal the canvas after a successful first frame.
6. If restoration does not complete or the renderer repeatedly fails, remain on the poster fallback for that session rather than entering an unbounded retry loop.

### Three.js and test-only controls

Three.js documents `WebGLRenderer.dispose()`, `forceContextLoss()`, and `forceContextRestore()`. The latter two are useful for controlled testing when `WEBGL_lose_context` is available; they simulate loss/restoration and should not be mistaken for a universal production recovery API. [S13]

## 3. WebGPU device loss: a separate state machine

### Verified platform behavior

- A `GPUDevice` owns the objects created from it. When it becomes invalid, objects created directly or indirectly from it become unusable. [S5]
- `GPUDevice.lost` is a promise that remains pending during the device's lifetime and resolves with a `GPUDeviceLostInfo` when loss occurs. [S5][S7]
- Device loss can be transient, including browser resource management or driver updates. MDN recommends requesting a replacement device after transient loss and recreating resources on that new device. An adapter may become unavailable and return already-lost devices, so retries cannot be assumed to succeed forever. [S7]
- The current WebGPU Editor's Draft defines `reason` values `unknown` and `destroyed`. The `message` is implementation-defined and must not be parsed by application logic. [S5]
- `GPUDevice.lost` and related WebGPU APIs remain marked limited availability by MDN at retrieval; use feature detection and retain a non-WebGPU path. [S7][S8]

### Recovery policy

- Distinguish an intentional application `device.destroy()` from unexpected loss. Do not recreate a device that the application intentionally destroyed as part of normal teardown.
- For unexpected loss, re-request an adapter/device and rebuild all buffers, textures, bind groups, pipelines, and other device-owned resources from CPU-side scene descriptions and source assets.
- If the adapter is unavailable or a replacement device is immediately lost, stop retrying and show the static fallback.
- Use a bounded retry policy with backoff (for example, one retry after a short delay, then one final retry after a longer delay). This retry count/timing is a PROFOLIO engineering proposal, not a WebGPU requirement.
- When using Three.js WebGPURenderer, avoid reaching into undocumented/private backend device fields. The public Three.js material reviewed documents async initialization, `dispose()`, and WebGL 2 fallback, but does not establish a stable public application-level device-loss subscription contract. Keep any direct WebGPU management in a separate, explicitly owned integration until a supported public hook is documented. [S11]

## 4. React Three Fiber ownership and lifecycle

### Renderer ownership is explicit

The R3F Canvas API documents three ownership cases: R3F creates and disposes the renderer when `gl` is omitted or a props object is supplied; R3F also owns a renderer returned from a sync/async factory; if the application passes a renderer instance directly, the application owns its disposal. The docs recommend a dedicated renderer per root, including for WebGPURenderer. [S4]

**Recommended default:** use an R3F-owned renderer factory for a single scene root. Pass an existing renderer instance only when there is a clear owner responsible for teardown and no other root can dispose it.

### Hide versus unmount

R3F 9.8.1's Activity work is important for route/overlay transitions: an Activity-hidden Canvas can retain its renderer and scene state while scene effects and frame subscriptions are disconnected, then reconnect when shown. A true removal still releases the renderer. [S1]

Use hide/suspend semantics when the user is temporarily switching panels and scene continuity matters. Use actual unmount when the scene is permanently leaving the page and its GPU resources should be released.

### Interpret console diagnostics correctly

R3F's 9.8.1 release notes explain that renderer teardown can dispose the renderer and release its context. Therefore, a `THREE.WebGLRenderer: Context Lost.` message immediately following a known unmount can be expected cleanup. Investigate it as a fault when it happens while the Canvas is supposed to remain mounted, when a route remount stays blank, or when resource/context counts grow across repeated navigation. [S1][S13]

## 5. Visibility, lazy mounting, and mobile resource pressure

- Use IntersectionObserver to begin loading just before the 3D section enters view; a positive `rootMargin` lets the asset request and renderer initialization start ahead of visibility. Keep the callback short because it runs on the main thread. [S14]
- Use the Page Visibility API's `visibilitychange` event to pause nonessential animation, media, and expensive simulation when the document is hidden. Browsers commonly pause requestAnimationFrame callbacks in background tabs, but application-level state should still be reconciled when the tab returns. [S15]
- Prefer one persistent, high-value canvas over a page full of simultaneously mounted canvases. Use poster imagery for offscreen scenes and mount the renderer only when it adds meaningful value.
- Reduce render scale, shadows, post-processing, and particle count under measured performance pressure; keep these as configurable quality tiers rather than hard-coded device-name assumptions.
- Do not infer exact GPU memory use from compressed download size. Track source texture dimensions, render-target dimensions, DPR, active canvases, and asset counts; cross-browser GPU memory telemetry is not a dependable public budget meter.

## 6. Proposed runtime state model

This is an engineering synthesis, not a browser or framework API:

| State | User-visible result | Allowed work |
|---|---|---|
| `poster` | Static poster and complete DOM | No renderer |
| `loading` | Poster plus loading affordance | One renderer/asset initialization |
| `live-webgl` / `live-webgpu` | Interactive scene | Bounded rendering and input |
| `suspended` | Poster or paused frame | No continuous loop; preserve CPU scene state |
| `recovering` | Poster plus restrained recovery message | Rebuild only from authoritative CPU-side inputs |
| `fallback` | Static poster and functional page | No GPU retry until explicit new session/action |

Store durable scene inputs outside GPU objects: asset URLs, selected variant, camera preset, interaction state, and renderer-neutral configuration. Treat textures, buffers, materials, pipelines, and render targets as disposable caches that can be rebuilt.

## 7. Implementation and QA checklist

### Before implementation
- Read actual package versions from the lockfile; this research did not inspect a PROFOLIO application lockfile.
- Confirm whether R3F 9.8.2 is compatible with the chosen React and Three.js versions.
- Keep v10 alpha behind a separate prototype branch and renderer boundary.
- Ensure the DOM title, navigation, project information, and calls to action remain usable without the canvas.

### Required tests
| Test | Expected result |
|---|---|
| No WebGL support | Poster and page content remain complete |
| Context creation error | Diagnostic captured; static fallback remains |
| WebGL context lost while loading | Old async callbacks ignored; frame loop paused |
| WebGL context restored | App-owned GL state/resources rebuilt before revealing canvas |
| WebGPU device lost | New device requested only for unexpected loss; all old-device resources rebuilt |
| Adapter unavailable / immediate repeated loss | Bounded retries end in static fallback |
| React Activity hide/show | Scene state preserved; hidden frame subscriptions stop and resume |
| True Canvas unmount | R3F-owned renderer is disposed |
| Renderer instance passed to Canvas | Explicit application owner disposes it exactly once |
| Repeated route navigation | No growth in active roots, canvases, listeners, or retained portal state |
| React StrictMode / Fast Refresh | No stale teardown destroys a live remounted Canvas |
| `frameloop="demand"` | Removed objects disappear; input explicitly invalidates when needed |
| Move window between displays / browser zoom | DPR updates without an uncontrolled render-scale jump |
| Hidden tab / return | Animation pauses while hidden and resumes from coherent state |

### Observability
Record non-sensitive diagnostics: renderer family, package versions, context/device loss event type, retry count, time to fallback, recovery success, active Canvas count, scene asset bundle version, and first-visible-frame duration. Do not log adapter identifiers, device-specific fingerprints, or raw implementation error messages as stable identifiers.

## 8. Decision for PROFOLIO

1. **Stable baseline:** validate the R3F 9.8.2 stable line against the actual lockfile before adopting it; its recent lifecycle fixes directly address Canvas teardown, Activity hiding, portal retention, and DPR changes.
2. **WebGPU strategy:** evaluate R3F v10 alpha 6 separately. Its WebGPU-first direction and `/legacy` split are meaningful, but the release is explicitly pre-release and should not silently become the production baseline.
3. **Resilience:** build a renderer-neutral poster/fallback shell and an explicit loss/recovery state machine before making the scene more elaborate.
4. **Resource ownership:** one owner per renderer/root, one authoritative source for scene inputs, and explicit cleanup for custom GPU resources.
5. **Release gate:** no 3D hero should ship until context/device-loss tests, route lifecycle tests, reduced-motion behavior, and mobile quality tiers pass on representative devices.

## Limitations

- This is documentation and release-note research, not a benchmark on a PROFOLIO asset or physical device.
- The PROFOLIO application source and lockfile were not available in this run; installed package versions and project-specific compatibility are therefore unverified.
- WebGPU loss recovery is described at the platform API level. The public Three.js WebGPURenderer documentation reviewed does not expose a stable app-level device-loss callback contract; do not rely on undocumented internals.
- R3F v10 alpha 6 is a pre-release. Release status and API shape can change.
- Retry counts, quality tiers, and the runtime state model are recommendations, not standards.

## Version and provenance snapshot

- Research date: 2026-11-01.
- R3F stable release evidence: 9.8.1 (2026-09-24) and 9.8.2 (2026-10-15).
- R3F pre-release evidence: 10.0.0-alpha.6 (2026-10-17).
- WebGPU specification source: W3C GPU for the Web Editor's Draft dated 2026-10-30.
- Three.js and R3F API documentation: current official pages retrieved 2026-11-01; exact npm versions are not pinned by the pages.
- MDN pages and browser compatibility classifications retrieved 2026-11-01.

See `sources.json` for publishers, exact URLs, dates, version/scope notes, evidence summaries, and use in this report.
