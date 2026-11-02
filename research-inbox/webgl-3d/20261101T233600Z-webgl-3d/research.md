# Shader-ready is not first-frame-ready

Run: `20261101T233600Z-webgl-3d` · Scheduled: 2026-11-01 23:36 UTC · Evaluated: 2026-11-01, beginning 23:39 UTC.

## Question and incremental value

What should a Three.js scene's loading screen promise after shader precompilation finishes? Supporting question: how can a route change invalidate a pending warm-up result?

The live catalog and seven existing topic packages were read first. They cover delivery, renderer recovery, stale loads, materials, independent characters, selection and resize. Compilation is mentioned, but none defines a first-frame acceptance contract. No same-run artifact or unfinished topic directory was found.

## Verified findings

- `compile` prepares scene materials using a camera; the target scene's lighting and environment should already be configured. `compileAsync` exposes shader preparation as a promise and uses the parallel-compilation extension. Its documented scope is avoiding unnecessary shader-compilation stalls, not certifying the entire presentation. `initTexture` separately initializes textures ahead of use; `initRenderTarget` initializes target memory. [S1](https://threejs.org/docs/pages/WebGLRenderer.html)
- A texture's `needsUpdate` schedules its update/upload on rendering. A downloaded image and an initialized GPU texture are therefore different milestones. [S2](https://threejs.org/docs/pages/Texture.html)
- A material's `needsUpdate` indicates recompilation. Custom `onBeforeCompile` settings can be represented in `customProgramCacheKey`; that API is specific to WebGLRenderer. A warm-up result should not be treated as permanent across arbitrary material changes. [S3](https://threejs.org/docs/pages/Material.html)
- In the inspected implementation, `compileAsync` calls `compile` and polls the collected materials' current programs. A material whose current program is absent is removed from the waiting set, alongside ready programs. The code explicitly describes this as including disposed materials. Thus promise resolution is not evidence that the application's originally intended objects still exist. The no-extension branch delays the readiness check; an async API name alone does not establish a fully non-blocking workload. [S4]

## Proposed readiness contract — original synthesis, untested

Use named milestones rather than one overloaded `loaded` boolean:

1. **Assets resolved:** required source data has arrived and the loader has produced the expected objects. Check errors and required asset identity.
2. **Scene revision assembled:** camera, intended lighting, environment and material variants match the view about to be shown.
3. **Shader preparation finished for that revision:** await the appropriate renderer API, then check that this route and scene revision are still current.
4. **Representative render evaluated:** exercise the actual rendering path, including relevant post-processing and targets, and inspect errors and visible output in testing.
5. **Presentation accepted:** reveal the live view only under the application's tested policy, retaining a useful fallback on failure.

These are application-level proposals, not additional guarantees supplied by Three.js. A render call returning is not proof that the browser has displayed the intended pixels or that the scene meets a frame-time budget. Do not use a loader completion percentage as a performance certificate. Keep shader-error diagnostics enabled during development and inspect compilation/linkage failures separately; the renderer documentation recommends these checks. [S1](https://threejs.org/docs/pages/WebGLRenderer.html)

Give the warm-up operation a route/scene generation token. If navigation replaces the scene while preparation is pending, its completion must not hide the new route's fallback or mark its scene ready. This extends the earlier stale-load package's ownership principle to GPU preparation; it does not introduce cancellation into `compileAsync`.

Warm only the variants needed for the intended experience. Compiling every hypothetical variant can shift excessive work into startup. Texture initialization and target allocation also consume resources; preparing them earlier does not eliminate those costs, and its total impact must be measured. Keep one lifecycle owner responsible for teardown and invalidate readiness after relevant scene changes. Do not dispose shared resources merely to cancel one route's presentation.

## Proposed acceptance fixture — not executed

Use an original minimal scene with one textured object, one controlled material variant and the actual intended pass chain. Compare cold start, cached-source start and a subsequent visit. Record loader completion, compilation completion, first rendering activity and observed frame timing separately.

| Scenario | Evidence to collect |
|---|---|
| Texture source is ready before first use | Determine whether upload/decode work remains in the first visible frame. |
| Material variant changes after warm-up | Verify the new variant's behavior rather than reusing an old readiness flag. |
| Navigation during compilation | Old completion never reveals or updates the replacement scene. |
| Material disposed while waiting | Promise completion does not falsely declare the missing object usable. |
| With/without parallel compilation support | Measure behavior on actual devices; do not infer equal responsiveness. |
| Actual post-processing enabled | Inspect final output and frame cost, not just scene-material preparation. |

No entries in this matrix have passed runtime testing. A future original application is a development-only readiness timeline that shows these milestones and the scene revision, making misleading loading-screen claims easier to diagnose.

## Compatibility, licence, costs and alternatives

This is a JavaScript/Three.js WebGLRenderer integration note, not a tool shortlist. It does not certify WebGPURenderer or an R3F wrapper's preparation behavior. Use the application's pinned version and verify wrapper ownership before implementation. No additional API service, authentication, MCP connection or paid hosting is required by the proposed design. CPU/GPU time, memory, battery and delayed reveal are the relevant costs; no measured universal budget is available.

The inspected development snapshot declares Three.js `0.187.0`, with development tooling and no dependencies field. Commit `8086b3fda07a70bffad7ee39265c9617994ec482` has committer date 2026-11-01T22:25:58Z. This is not a verified npm release or installed application version. [S5] Its actual licence is MIT, requiring preservation of copyright/permission notices with copies or substantial portions and providing no warranty. Unrelated models, textures and demo media require their own rights. [S6]

Alternatives include a simpler material/pass setup, incremental scene reveal, or a static poster when startup cost is unjustified. No demo was executed, library installed, asset imported, application changed or deployment performed. Browser, GPU, runtime, performance, accessibility and integration tests remain unperformed. Official documentation is moving content; npm metadata and the application's lockfile were not verified.

## Pinned repository references

- [S4: WebGLRenderer implementation](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/src/renderers/WebGLRenderer.js)
- [S5: Package manifest](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/package.json)
- [S6: MIT licence](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/LICENSE)
