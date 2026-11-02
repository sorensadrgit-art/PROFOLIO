# A frozen shadow needs two update gates and a frame

- Run: 20261102T143600Z-webgl-3d
- Scheduled: 2026-11-02T14:36:00Z; evaluated: 2026-11-02
- Question: How should an occasionally changing Three.js scene refresh frozen shadow maps without silently keeping stale shadows?
- Scope: WebGLRenderer shadow refresh ownership, with an R3F demand-loop integration boundary. Documentary research, not an implementation or measured optimization.

## Why this is new

The live catalog and all twelve existing WebGL topic reports were read before selection. Earlier reports cover broad lighting budgets, GPU deformation shadow hooks, frame counters and runtime lifecycle. None documents the interaction between renderer-wide shadow freezing, per-light freezing, and demand-loop scheduling. No existing package or unfinished entry for this run ID was found. This is a focused update protocol, not another renderer comparison.

## Source findings

1. **There are two independent WebGL shadow gates.** The pinned renderer exits the entire shadow pass when global automatic updates are off and its global dirty flag is false. Inside the light loop it separately skips a light whose automatic updates and dirty flag are both false. Setting only a light's flag cannot bypass the first gate; setting only the renderer flag cannot bypass a frozen light's gate. After a processed light it clears that light's flag, and after the pass it clears the global flag. These are observations from the pinned implementation, not an all-version API promise. [S2](https://github.com/mrdoob/three.js/blob/104aa0bc3cd4894da6349048948d92ccd5332bfa/src/renderers/webgl/WebGLShadowMap.js)

2. **Dirty is not rendered.** LightShadow documents explicit refresh on the next render, not immediate computation. R3F's demand loop similarly needs a scheduled frame for imperative changes, and invalidation requests a frame rather than drawing synchronously. Therefore a shadow dirty flag and a frame request solve different parts of the problem. [S1](https://threejs.org/docs/pages/LightShadow.html) [S3](https://r3f.docs.pmnd.rs/advanced/scaling-performance)

3. **Updating a map is a cost, not merely changing a boolean.** The observed WebGL implementation renders six cube faces for a point-light shadow. LightShadow documents a quality/computation trade-off for map dimensions. Freezing can avoid repeated shadow production when suitable, but does not itself remove the stored shadow resources or prove a frame-time improvement. [S2](https://github.com/mrdoob/three.js/blob/104aa0bc3cd4894da6349048948d92ccd5332bfa/src/renderers/webgl/WebGLShadowMap.js) [S1](https://threejs.org/docs/pages/LightShadow.html)

4. **Scope the mechanism to the renderer actually in use.** WebGLRenderer is a WebGL2 renderer; WebGL1 is unsupported from r163 onward. Do not transplant its renderer.shadowMap control contract into WebGPURenderer or infer equivalent backend behavior from a shared LightShadow class. [S6](https://threejs.org/docs/pages/WebGLRenderer.html)

## Original synthesis: one owner for the refresh transaction

The following is an untested application design, not upstream code:

- First enable shadow rendering and configure the relevant light to cast shadows, with appropriate caster/receiver flags. Dirty flags do not enable a disabled shadow pass.
- Start with automatic updates while establishing a correct reference image. Freeze only a scene/light whose shadow-producing inputs can be tracked.
- Prefer keeping the renderer-wide gate automatic and freezing selected lights individually. This reduces the chance that an unrelated dynamic light is accidentally held back.
- If the application intentionally freezes both levels, a refresh transaction marks every affected frozen light dirty, marks the renderer dirty, then schedules the frame through the existing render-loop owner.
- Apply changes to the object/light state before that transaction. Keep normal matrix updates enabled, or explicitly integrate the application's manual matrix-update policy. A dirty flag cannot repair stale transforms.
- For initialization, request an explicit refresh even when freezing before the first frame. Do not depend on an uninitialized map bypassing either gate.
- Treat animation as an interval, not a single edit: changing a caster continuously while refreshing once produces only a snapshot. Keep relevant updates enabled during motion, or deliberately accept a sampled-shadow policy with visual testing.
- After the last animation update, request a final refresh before returning to a frozen state. Route changes and asynchronous asset attachment need the same ownership check so an obsolete scene cannot request work on a replacement scene.

A useful application-side event is “shadow inputs changed” with the affected lights and a reason. Candidate reasons include light/target movement, caster transforms or geometry, caster insertion/removal, visibility, and shadow-camera or shadow-map configuration changes. These are proposed invalidation categories to validate, not a complete engine dependency tracker.

Avoid a blanket rule that every visible edit requires rebuilding the shadow map. A receiver-only transform may only need a main render in a simple depth-shadow setup, while VSM and objects that also cast need separate consideration. The pinned implementation includes receiving objects in its VSM shadow selection. Start conservatively; reduce invalidations only after testing the chosen algorithm. [S2](https://github.com/mrdoob/three.js/blob/104aa0bc3cd4894da6349048948d92ccd5332bfa/src/renderers/webgl/WebGLShadowMap.js)

## Two practical alternatives

**Per-light freeze:** Leave the global gate open, disable automatic updates only on static lights, and dirty those lights when needed. This is the preferred first prototype because dynamic lights retain normal behavior.

**Whole-scene freeze:** Disable global shadow updates only when one scene owner controls all relevant mutations. That owner must coordinate both gates where individual lights are also frozen. This is more fragile when independent components add objects or change light configuration.

For a largely decorative scene, an authored contact-shadow image or baked lighting may be a simpler alternative, provided its fixed-light assumptions and asset rights fit the design. That is a design recommendation, not a tested substitute for dynamic shadows.

## Compatibility, dependencies, maintenance and operation

Three.js is a JavaScript 3D library. The inspected dev commit is 104aa0bc3cd4894da6349048948d92ccd5332bfa, dated 2026-11-02T13:16:24Z, and its package declares version 0.187.0. This establishes a source snapshot, not the latest npm release, a supported deployment target or a release date. The package has no runtime dependencies field; build/test development dependencies are separate. Pin and inspect the application's actual lockfile before adopting the protocol. [S4](https://github.com/mrdoob/three.js/blob/104aa0bc3cd4894da6349048948d92ccd5332bfa/package.json)

A vanilla integration needs the existing Three.js renderer, scene and render-loop owner. R3F additionally needs the project's compatible React/Fiber/Three versions and its invalidate interface; no particular Fiber version was verified here. No API key, MCP connection, backend service or paid service is required for this pattern. Host/bandwidth and client GPU costs remain application-dependent; no cost or performance savings were measured.

The actual Three.js LICENSE is MIT: preserve the required copyright and permission notice with copies or substantial portions, and note the warranty disclaimer. This does not clear unrelated GLB models, textures, fonts or demo artwork for reuse. No protected demo assets or repository copies are included. [S5](https://github.com/mrdoob/three.js/blob/104aa0bc3cd4894da6349048948d92ccd5332bfa/LICENSE)

Official docs are the API reference for these controls; no rendered demo was exercised in this run. The maintenance evidence is the observed dev commit only. Release cadence, regression history and long-term support remain unassessed. Rolling documentation may differ from the application version.

## Proposed acceptance fixture — not executed

Use a small original scene with one static directional light, one dynamic light, a moving caster and a receiving plane. Record renderer/version, shadow algorithm, loop mode and gate settings.

1. With both gates automatic, save the visual baseline.
2. Freeze one light; move its caster and request only a normal frame. Confirm the expected stale shadow is detectable.
3. Dirty that light and request a frame with the global gate open. Compare with the automatic baseline.
4. Freeze both gates; exercise light-only, global-only and both-flags cases. Confirm only the applicable combined update refreshes the frozen light.
5. In demand mode, set dirty flags without invalidating, then invalidate. Observe scheduling and eventual output separately.
6. Insert/remove an asset, change a light target and complete an animation. Verify the last shadow state, not just the first frame.
7. Test any VSM, custom depth material, skinned or GPU-deformed caster separately; do not generalize the simple fixture.
8. Measure shadow work and whole-frame cost with a consistent profiling boundary. No performance claim is accepted from flags alone.

An original future application is a portfolio lighting inspector that displays “scene changed,” “shadow refresh requested,” and “frame observed” separately. Such a diagnostic could make stale-map bugs explainable without pretending that a requested update proves correct rendering.

## Limitations

Documentary review only. No installation, code execution, runtime, integration, accessibility, visual, mobile or performance test was performed. The protocol and fixture are untested recommendations. Approval means this package's documented review, not deployment permission or Gemini approval.

