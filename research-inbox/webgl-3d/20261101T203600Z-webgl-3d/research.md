# Resize the pipeline, not just the canvas

Run: `20261101T203600Z-webgl-3d` · Scheduled: 2026-11-01 20:36 UTC · Evaluated: 2026-11-01, beginning 20:43 UTC.

## Question and new value

How should a responsive Three.js post-processing scene keep its canvas, camera and offscreen targets consistent when layout or pixel density changes?

The six existing topic packages were read first. They cover delivery, recovery, stale loads, materials, character ownership and instance selection. They mention DPR and target dimensions but do not define this resize contract. Live topic inventory contained no package for this run or unfinished topic directory.

## Source findings

- Renderer sizing has two units: `getSize` returns logical dimensions; `getDrawingBufferSize` returns physical dimensions including pixel ratio. `setSize` takes logical dimensions and can leave CSS styles unchanged with its third argument. [S1](https://threejs.org/docs/pages/WebGLRenderer.html)
- Three's `EffectComposer` is a WebGLRenderer addon, not the WebGPURenderer pipeline. Its `setSize` accepts logical dimensions and resizes internal buffers and passes with its own pixel ratio. Its pixel-ratio setter triggers resizing. [S2](https://threejs.org/docs/pages/EffectComposer.html)
- The inspected implementation stores a separate composer pixel ratio, initializes it from the renderer, and multiplies logical dimensions before resizing both targets and each pass. Changing the renderer ratio alone is therefore not a synchronization contract. Composer disposal releases its two targets and copy pass; it does not iterate user passes for disposal. [S5]
- Render targets expose their own size and disposal API. Target disposal releases its textures as well. Depth attachments and multisampling are additional choices, not implied by CSS canvas dimensions. [S3](https://threejs.org/docs/pages/RenderTarget.html)
- Perspective camera projection must be updated after changing camera properties. [S4](https://threejs.org/docs/pages/PerspectiveCamera.html)

## Proposed resize contract — original synthesis, untested

Give one scene owner a revision containing logical width, logical height and chosen pixel ratio. Use container dimensions, not an assumption that the canvas fills the browser window.

1. If dimensions are zero or invalid, postpone rendering and allocation changes until the container is usable. Do not calculate an infinite camera aspect.
2. On a meaningful size or quality change, update the renderer's ratio and logical size. Preserve stylesheet ownership where appropriate.
3. Set the perspective camera aspect to width/height and update its projection.
4. Explicitly synchronize the composer ratio and logical size. Do not feed physical drawing-buffer dimensions into a composer that will multiply them again.
5. For standalone targets outside the composer, derive integer target dimensions from the desired physical resolution and effect scale. Update custom resolution/texel-size uniforms from the actual target dimensions, not blindly from the window.
6. Publish the revision only after the related CPU-side updates, then render. This is an application ordering proposal, not an atomic GPU-allocation guarantee.

A 1000-by-500 logical canvas at ratio 2 implies a 2000-by-1000 drawing buffer. Giving those physical dimensions to a ratio-2 composer would request 4000-by-2000 targets: four times the intended pixel count. This arithmetic illustrates a unit error, not a measured memory allocation or frame-time result.

Avoid repeatedly allocating on unchanged input. Rapidly varying quality settings can create resize churn; coalesce changes and test stabilization rather than adapting every frame by default. A custom pass may own further targets; verify its size and cleanup contract separately. Do not assume composer cleanup handles arbitrary pass resources.

## Small acceptance plan — not executed

| Scenario | Evidence required |
|---|---|
| Container changes without window resize | Canvas, camera aspect and every target follow the new container. |
| Ratio changes while CSS size stays constant | Composer and independent targets change coherently. |
| Hide then reveal container | No invalid aspect or zero-size render; correct first restored frame. |
| Custom half-resolution effect | Its target and reciprocal-resolution uniforms agree. |
| Repeated resize and unmount | No retained obsolete target/pass resources; no repeated unchanged allocation. |
| Ratio 1 versus 2 | Record actual dimensions and visual edges; do not infer speed from pixel count. |

A future original application is a portfolio scene-quality panel showing logical size, drawing-buffer size and each effect's target size together. It would make unit errors visible without claiming reliable total GPU-memory telemetry.

## Compatibility, operation and alternatives

This is a JavaScript Three.js integration note, not a tool shortlist. Use the addon from the same pinned Three.js distribution; no new API, MCP server, authentication or paid service is required by this design. Browser GPU work, retained targets and extra passes are real operational costs, but no universal budget or price is established. A direct renderer without post-processing removes composer coordination; a static poster avoids this runtime path entirely. R3F wrappers and WebGPU pipelines require their own verified lifecycle contracts—this report does not certify either.

The inspected repository snapshot declares version 0.187.0; this is a development snapshot, not a verified npm release or the application's installed version. Commit 3fd3e4ec0b454bcc171e5256fd5240251c92340b is dated 2026-11-01T09:04:17Z. Its package manifest has development tooling but no dependencies field. [S6] Actual lockfile compatibility remains unknown. The included Three.js licence is MIT, requiring preservation of its copyright/permission notice with copies or substantial portions and providing no warranty; that does not grant rights to unrelated scene assets. [S7]

Official documentation is moving content. The responsive manual URL attempted during discovery returned 404 and was not used as evidence. No demo, browser, GPU, runtime, performance, accessibility or integration test was executed. No library was installed, asset copied, application changed or deployment performed.


## Pinned repository references

- [S5: EffectComposer implementation](https://github.com/mrdoob/three.js/blob/3fd3e4ec0b454bcc171e5256fd5240251c92340b/examples/jsm/postprocessing/EffectComposer.js)
- [S6: Package manifest](https://github.com/mrdoob/three.js/blob/3fd3e4ec0b454bcc171e5256fd5240251c92340b/package.json)
- [S7: MIT licence](https://github.com/mrdoob/three.js/blob/3fd3e4ec0b454bcc171e5256fd5240251c92340b/LICENSE)
