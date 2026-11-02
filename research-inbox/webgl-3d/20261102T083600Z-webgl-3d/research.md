# A displaced vertex does not update the CPU visibility model

Run: `20261102T083600Z-webgl-3d` · Scheduled: 2026-11-02 08:36 UTC · Evaluated: 2026-11-02.

## Question and incremental value

How should a custom GPU-deformed mesh keep culling, picking and shadows consistent with its visible surface? This addendum concerns an ordinary Three.js Mesh with BufferGeometry and arbitrary vertex-shader displacement. It is not a universal recipe for skinning, morph targets, instancing or WebGPU materials.

All ten existing WebGL packages were read before selection. The instance-selection package discusses changed instance matrices and bounds; this addresses a different failure: CPU geometry is unchanged while the GPU moves vertices. The earlier shader-portability package does not define this visibility/picking/shadow contract.

## Source findings

- **Bounds are CPU-side inputs, not a readback of the shader.** At pinned commit `8086b3fda07a70bffad7ee39265c9617994ec482`, BufferGeometry.computeBoundingSphere reads position and morph attributes; Frustum.intersectsObject transforms an object or geometry bounding sphere into world space. Neither operation evaluates arbitrary vertex-shader code. Recomputing from unchanged CPU positions therefore cannot discover that shader's displacement. [BufferGeometry source](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/src/core/BufferGeometry.js), [Frustum source](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/src/math/Frustum.js).
- **Picking has both a broad phase and a surface test.** Mesh.raycast first checks its sphere and, when present, its box, then tests triangles through getVertexPosition. The inspected ordinary Mesh implementation reads position attributes and applies morph influences; it does not run arbitrary GLSL displacement. Enlarging bounds alone cannot make those triangles match a custom GPU surface. [Mesh source](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/src/objects/Mesh.js).
- **Disabling frustum culling is narrowly scoped.** Object3D.frustumCulled controls frustum checking and defaults to true. It is not a picking or shadow-deformation fix. For custom vertex-position changes, Object3D documents customDepthMaterial for directional/spot shadow rendering and customDistanceMaterial for point-light shadows; these properties are WebGLRenderer-only. [Object3D documentation](https://threejs.org/docs/pages/Object3D.html).
- **Normal geometry edits are a different case.** The geometry documentation says a bounding sphere may need recomputation after vertex modification; bounding-box calculation is application-owned. These operations remain useful when actual CPU attributes change, not as a shader-output measurement. [BufferGeometry documentation](https://threejs.org/docs/pages/BufferGeometry.html).

## Original synthesis: define three contracts before building the effect

| Contract | Proposed implementation decision | What it does not establish |
|---|---|---|
| Visibility envelope | Bound maximum local-space displacement over every supported time/uniform value. For a base sphere radius r and displacement-vector length bounded by D, use the same center and radius r + D. This follows the triangle inequality. | Tightness, screen-space bounds, arbitrary object-specific overrides or correct bounds after unrelated geometry changes. |
| Interaction surface | Choose an intentional CPU proxy for approximate interaction, or implement a matching analytic/custom raycast; exact GPU picking is a separate design with its own synchronization/readback costs. | Merely inflating a sphere/box does not change triangle hits. |
| Shadow surface | In the WebGL custom-shader path, make the shadow-pass deformation agree with the visible pass and share relevant time/amplitude uniforms. Choose depth versus distance material for the actual light type. | WebGPU compatibility or a guarantee that all clipping, side, alpha and material settings match. |

Treat a present boundingBox as part of the picking envelope too; an enlarged sphere can still be rejected by an old box. Keep envelope construction explicit after geometry replacement. Do not repeatedly add D to an already inflated radius. A user-editable amplitude needs a maximum or a bounds-update rule, not an assumed fixed value. These are original, untested engineering recommendations.

A temporary `frustumCulled = false` is a useful diagnostic comparison for disappearance near the camera edge; it is not a recommended blanket production setting. Other visibility, layer and depth rules still apply. If that comparison changes the symptom, investigate bounds rather than declaring the complete effect repaired.

## Practical fit, dependencies and operational requirements

Three.js is a JavaScript ES-module 3D library. The inspected development commit's package.json declares version 0.187.0; this is a source-tree identifier, **not a verified npm release or installed application version**. The repository commit timestamp is 2026-11-01T22:25:58Z; a single recent commit is not a maintenance SLA. [Package metadata](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/package.json).

This approach needs an existing Three.js scene, suitable browser/GPU support, a bounded deformation function and explicit ownership of picking/shadow behavior. No MCP, authentication, remote API, server or paid service is required for the proposed client-side design. Normal application hosting and engineering costs remain; no dollar or frame-time estimates were measured. The base package declares no runtime dependency field in the inspected metadata; framework wrappers and build dependencies are outside this finding.

The repository license is MIT with copyright/license-notice retention for copies or substantial portions and an as-is warranty disclaimer. This package copies no implementation or assets. MIT for the inspected code does not establish reuse rights for unrelated models, textures, demos or third-party assets. [License](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/LICENSE).

Alternatives: CPU-side deformation can align ordinary geometry queries but adds update work; an intentionally coarse interaction proxy can be simpler for decorative effects; a static/non-deformed fallback avoids this contract entirely. No benchmark establishes a winner. R3F applications can apply the underlying object/material concepts, but wrapper lifecycle, disposal and event behavior require separate validation. WebGPU/TSL adaptation is explicitly unverified; do not port WebGL-only shadow hooks by assumption.

## Original future application and proposed acceptance checks

For a rippling project-card gallery, use a bounded decorative visual mesh plus a stable semantic card interaction proxy when exact surface hits are unnecessary. For a sculpting surface, require exact picking and budget for a matching deformation implementation instead. This is a proposed application, not a tested demo.

Before integration:
1. Sweep camera edges and every allowed displacement amplitude; compare normal culling with the diagnostic bypass.
2. Test pointer hits on displaced peaks and troughs; document whether the expected surface is visual or proxy-based.
3. Check each supported light type at matching animation times, including shadow silhouette and alpha/clipping behavior.
4. Replace geometry and change amplitude limits; ensure bounds derive from a fresh base rather than cumulative inflation.
5. Measure frame time and interaction latency on target hardware, and validate keyboard/non-3D alternatives separately.

**Test status:** documentary/source review only. No installation, demo execution, browser/GPU, performance, accessibility, integration or deployment tests were performed. Moving documentation and pinned development source may differ from a deployed release. Exact application compatibility, npm publication date and release date remain unknown.

