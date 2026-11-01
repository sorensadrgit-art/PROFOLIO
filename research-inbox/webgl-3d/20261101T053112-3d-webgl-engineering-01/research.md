# PROFOLIO 3D & WebGL Engineering Research

- Run ID: 20261101T053112-3d-webgl-engineering-01
- Topic: 3D and WebGL Engineering
- Research date: 2026-11-01
- Scope: Three.js, React Three Fiber (R3F), WebGL/WebGPU, Blender-to-glTF delivery, runtime performance, scene lifecycle, and Spline.
- Research question: What production architecture gives PROFOLIO cinematic, interactive 3D while controlling asset cost, device compatibility, battery use, and future WebGPU migration risk?

## Executive summary

The strongest direction is a progressive, asset-first 3D layer rather than a site whose essential content depends on a live canvas. Keep the HTML experience complete and readable without GPU rendering; lazy-mount a single high-value 3D scene where it materially improves the story; provide a poster/static fallback; and treat renderer selection, compressed assets, and cleanup as explicit engineering decisions.

Three findings have particularly high implementation value:

1. WebGPU is not yet a drop-in replacement for a GLSL-heavy Three.js scene. Three.js WebGPURenderer can fall back to WebGL 2, but the Three.js migration guide says ShaderMaterial, RawShaderMaterial, onBeforeCompile modifications, and the old EffectComposer pass chain need migration to TSL/node materials. The R3F v10 migration guide, which labels v10 an alpha release at the time checked, makes WebGPU the default Canvas renderer and directs GLSL/WebGL-only apps to the /legacy entry. Do not upgrade a production scene without an inventory of shaders and dependencies. [S1][S5][S6]

2. The largest reliable performance wins are usually upstream of shader cleverness: reduce unnecessary geometry/material/object counts, share resources, instance repeated objects, compress meshes and textures, avoid permanent 60 fps rendering for scenes that can sleep, and cap pixel ratio. R3F explicitly recommends mutation in useFrame rather than React state for frame-rate updates, resource reuse, loader caching, and on-demand rendering. [S2][S3][S4][S19]

3. Treat the Blender-to-glTF pipeline as a build pipeline with validation, not as a one-click export. Blender's glTF exporter has defined material and animation support boundaries; gltfpack can substantially optimize meshes, but it may discard unknown extensions and has limited custom-data preservation. Validate both the original and optimized GLB, then visually test the final asset in the actual target renderer. [S11][S12][S13][S14]

## 1. Renderer strategy: WebGL 2 today, WebGPU deliberately

### Verified findings

- Three.js WebGPURenderer attempts a WebGPU backend and can fall back to a WebGL 2 backend. The Three.js manual describes it as a universal renderer, but also explicitly says the renderer remains experimental and that some scenes can have missing features or perform better with WebGLRenderer. [S5][S6]
- The migration cost is not theoretical: custom ShaderMaterial, RawShaderMaterial, and onBeforeCompile modifications are not supported on WebGPURenderer; they must be ported to TSL/node materials. The old EffectComposer effect-pass workflow is also not supported by that renderer's new post-processing stack. [S5]
- MDN currently labels the WebGPU API as limited availability / not Baseline and requires a secure context (HTTPS). A WebGPU-only deployment therefore has a browser-support failure mode. [S7]
- The R3F v10 migration guide is explicitly marked alpha. It says the default @react-three/fiber Canvas uses WebGPURenderer, with WebGL2 fallback, while GLSL and WebGL-only libraries should use @react-three/fiber/legacy. It lists three >=0.185.0 and React >=19.0 <19.4 as the v10 dependency ranges documented there. Treat these as the guide's stated compatibility floor/range, not a guarantee that every later release has the same requirements. [S1]
- The R3F Canvas API provides a fallback element for systems without GL support and recommends guarding against context crashes with an error boundary. [S2]

### Engineering conclusion

For a production portfolio scene with custom GLSL, post-processing, or shader-based Drei components, keep the current WebGL 2 path until the entire dependency chain has been verified against WebGPU. If the scene uses standard materials, lights, loaders, and controls only, prototype WebGPURenderer behind a feature/configuration boundary and test the fallback path explicitly.

Do not mix renderer assumptions in shared components. Keep renderer-specific code at the scene root and pass only renderer-neutral scene data and interaction state into shared components. Maintain a WebGL 2 fallback or static image when the audience cannot be controlled.

### Migration gate

Before switching a scene:
- Search for ShaderMaterial, RawShaderMaterial, onBeforeCompile, direct WebGLRenderer type assumptions, EffectComposer, postprocessing, and Drei components that depend on GLSL.
- Confirm every dependency supports the intended renderer.
- Test both WebGPU and forced WebGL 2 modes. Three.js exposes forceWebGL on WebGPURenderer for testing the fallback backend. [S5]
- Record actual Three.js, R3F, React, and post-processing package versions from the project's lockfile; the documentation pages are moving targets and do not pin every package version.

## 2. Blender → GLB/glTF → browser asset pipeline

### Authoring and export

glTF 2.0 is the Khronos runtime 3D asset delivery format; the current registry lists specification 2.0.1. Blender 5.2's glTF documentation describes mesh, texture, camera, punctual-light, extras, and animation export. It also documents important translation limits: curves and non-mesh data need conversion to meshes; glTF triangulates quads and n-gons; UV seams and flat-shaded boundaries can increase exported vertex counts. [S13][S14]

Recommended authoring checklist:
- Keep a clean source .blend and export only the intended collection/selection.
- Apply and inspect transforms, object origins, normals, UVs, and scale before export.
- Convert curves and procedural geometry that must appear in the delivered asset.
- Use Principled BSDF nodes that map to glTF's metal/roughness model; inspect the exported result rather than assuming every Blender node graph is portable.
- Name objects, materials, actions, and animation clips intentionally. Blender's exporter uses active/stashed actions or NLA tracks according to its animation mode; unassociated actions are not automatically guaranteed to export.
- Use GLB for a compact, self-contained handoff unless a split .gltf + .bin + texture structure is needed for inspection or independent caching.
- Confirm texture color-space intent and the final exported material under the target renderer.

### Blender-specific traps

The Blender manual says only certain animation types are exported through the documented glTF animation path: object transforms, pose bones, and shape-key values. Other properties such as physics, lights, or materials are not generally exported as ordinary glTF animations by that path. GPU instancing export has constraints: instances must be mesh objects, must be children of the same parent, and material variation is not handled by that extension. [S13]

For repeated objects, choose between Blender's GPU instance export and runtime InstancedMesh only after checking the target loader, per-instance material needs, and whether the instance transforms must be interactive. Do not assume that every Blender Geometry Nodes graph becomes a portable browser-side procedural system.

## 3. Compression: optimize the right resource, then validate

Use separate decisions for geometry, texture delivery, and GPU residency:

| Concern | Useful option | Trade-off / requirement |
|---|---|---|
| Mesh vertex/index size and cache locality | gltfpack / meshoptimizer | Quantization and mesh merging can alter precision, node structure, and metadata. |
| Geometry transfer compression | EXT_meshopt_compression / KHR_meshopt_compression | Register the meshopt decoder with GLTFLoader. |
| Draco-compressed geometry | DRACOLoader | Can reduce geometry transfer size but adds client decode work; reuse one decoder instance. |
| GPU-friendly texture delivery | KTX2 + Basis Universal | KTX2Loader transcodes to a supported compressed GPU format; it needs its WASM transcoder and renderer support detection. |

Three.js GLTFLoader documents support for Draco, meshopt, Basis/KTX2, GPU instancing, WebP/AVIF and several material extensions. It exposes setDRACOLoader, setMeshoptDecoder, and setKTX2Loader. KTX2Loader.detectSupport(renderer) must be called before loading so the transcoder can choose a supported output format. DRACOLoader recommends reusing one loader/decoder instance. [S8][S9][S10]

gltfpack is valuable because it can optimize vertex fetch and transform cache, quantize geometry, merge meshes to reduce draw calls, resample/quantize animations, and prune redundant nodes. Its README documents meshopt compression options and warns that unknown/vendor/custom extension nodes can be discarded and custom data preservation is limited. It also notes that Draco and some interactive-scene extensions are not supported by gltfpack. Therefore, never run gltfpack blindly over an asset whose extras, extension payloads, animation-pointer data, or vendor metadata are required. [S11]

Recommended order:
1. Export a baseline GLB from Blender.
2. Validate the baseline with Khronos glTF-Validator.
3. Record file size, primitive/mesh count, triangle count, texture dimensions, animation names, and extension list.
4. Run gltfpack with a conservative option set; use meshopt compression only when the target GLTFLoader is configured for it.
5. Validate the optimized output again.
6. Compare the rendered output: silhouette, normals, UVs, material response, transparency, animation timing, node names, and interactive metadata.
7. Test download and decode cost on a representative mid-range mobile device.

The Khronos validator checks glTF 2.0 structure, GLBv2 correctness, references, buffers, accessors, animation data, and supported extensions, and emits a JSON report. It is a structural validator, not a substitute for visual QA or performance testing. [S12]

## 4. Runtime performance: focus on frame cost, draw calls, pixels, and memory

### React Three Fiber

- Share geometry and materials instead of creating a fresh object for each repeated item; R3F notes that mounting materials/lights can trigger compilation and geometry processing. Use InstancedMesh for many copies of the same geometry/material. [S3][S19]
- Keep per-frame motion out of React state. Use refs and useFrame with delta-time-based updates; avoid setState inside useFrame or pointer-move handlers. Reuse scratch vectors/matrices rather than allocating them each frame. [S3]
- Use useLoader/useGLTF-style caching for shared URLs. The same URL is a cache key; do not mutate or dispose cached resources casually if other scene instances reuse them. R3F attempts to dispose unmounted objects, but shared/cached resources may need dispose={null} at the shared ownership boundary. [S3][S4]
- Prefer Canvas frameloop="demand" for scenes that are static between user interactions. Call invalidate when imperative controls or external code mutate the scene. An invalidation schedules a frame; it is not an immediate synchronous render. [S4]
- Keep the canvas bounded and cap device pixel ratio. R3F's Canvas API defaults DPR to [1, 2]; for mobile-first content, test a lower ceiling such as 1.5 and measure quality/performance on actual devices rather than blindly using the full devicePixelRatio. This is a project recommendation, not a universal browser threshold. [S2]

### Three.js and GPU-side work

InstancedMesh reduces draw calls when objects share geometry/material but have different transforms. When instance matrices or colors are changed, set the corresponding needsUpdate flag. Recompute bounds when transforms change enough to invalidate them. [S19]

For particle fields, prefer one or a small number of Points/BufferGeometry batches or instanced billboards over thousands of independent Mesh objects. Keep attributes compact, update only the data that changes, and move repeated animation math into the vertex shader when it is justified by profiling. This is an engineering recommendation based on the documented Points/BufferGeometry/instancing primitives, not a measured benchmark for PROFOLIO. [S19][S20]

Use renderer.info during profiling to inspect draw calls and triangle counts. Avoid querying expensive WebGL state synchronously in hot production loops: MDN warns that getError and many getParameter calls can stall the calling thread and jank the page. Collect diagnostics during development or at controlled intervals. [S6][S15]

### Texture memory and lighting

Compressed JPG/PNG transfer size is not the same as GPU memory use: the GPU typically needs an expanded or GPU-native texture representation. Three.js's cleanup guide estimates that a 1024×1024 texture can consume roughly 4–6 MB in use. As a rough RGBA8 calculation, a 4096×4096 base level alone is 64 MiB before mipmaps or other copies; mipmaps add about one-third again. Treat this as a planning estimate, not a fixed allocation guarantee. [S17]

KTX2/Basis is valuable because it allows runtime transcoding to GPU-supported compressed texture formats instead of assuming one hardware format. Set the loader up against the actual renderer before loading. [S9]

Use a deliberate lighting rig rather than accumulating lights and shadow maps. Prefer environment lighting plus a small number of purposeful key/fill/rim sources, then validate the final look on low-end hardware. Spline's own optimization guidance recommends simple materials, fewer objects, fewer lights, and restrained post-processing; it suggests fewer than three lights for its scene workflow. That is vendor guidance, not a universal Three.js hard limit. [S21]

Color management is a frequent source of apparent material defects. Three.js uses Linear-sRGB as its working space for lighting and rendering; color textures such as base-color/emissive maps should be tagged sRGB, while data textures such as normal and roughness maps should not be treated as color textures. Custom shaders must handle output color-space conversion correctly. [S18]

## 5. Scene lifecycle, resilience, and architecture

Recommended scene boundaries:
- A page-level DOM layer owns layout, text, controls, accessibility, and the non-WebGL fallback.
- A scene shell owns Canvas/renderer creation, loading state, error boundary, resize, DPR, and visibility/lifecycle policy.
- Reusable scene components own logical groups and interactions, not global renderer state.
- An asset layer owns GLB/texture loading, cache keys, decoder instances, and shared-resource policy.
- A profiling layer records renderer.info and asset metrics in development.

Three.js resources are not automatically freed by JavaScript garbage collection. Geometries, materials, and textures need explicit disposal when no longer used; disposing a material does not dispose its textures, because textures may be shared. ImageBitmap-backed textures may also require ImageBitmap.close() at the application level. [S17][S8]

R3F attempts to call dispose on unmounted objects. When a cached/shared asset is reused across components, use an explicit ownership convention and disable automatic disposal only at the shared boundary; otherwise one component can dispose a resource still in use elsewhere. [S4]

Provide a static poster or lightweight DOM/CSS scene when WebGL cannot be created, a context is lost, the device is under memory pressure, or the scene fails to load. Test context-loss recovery deliberately: MDN documents WEBGL_lose_context as a way to simulate loss and restore events. [S2][S16]

## 6. Spline versus custom Three.js/R3F

Spline is a strong option for rapid art direction and designer-owned interactive scenes. Its current code export documentation lists Vanilla JS, Three.js, React, Next.js, and R3F export targets, but says animations and events are only enabled for Vanilla JS and React exports. Choose the export target based on the interaction requirements, not merely the framework label. [S22]

Spline's Performance Panel estimates export size/loading time and reports objects, clones, booleans, polygons, materials, lights, post-processing effects, and textures. Its optimization guidance recommends reducing polygons, objects, textures, lights, and post-processing; it also recommends reusable components/material assets and limiting page embeds. Use these metrics before export, then profile the actual page because Spline labels some estimates as averages and says actual values vary. [S21]

Spline Play Settings documents an Auto renderer that uses WebGPU where supported and falls back to WebGL elsewhere; it also offers explicit WebGPU-only and WebGL-only choices. For a public audience, Auto is the documented safest choice. Self-hosted project export includes the runtime and assets but is documented as Enterprise-only. [S23][S24]

Engineering conclusion:
- Prefer Spline when visual iteration speed and designer-authored interactions dominate, and its runtime/export/licensing constraints fit the project.
- Prefer custom Three.js/R3F when you need deterministic code ownership, custom data-driven interactions, low-level performance control, reusable components, or a bespoke shader/asset pipeline.
- A hybrid can work: author or prototype the art direction in Spline, then decide whether to keep the Spline runtime or rebuild the scene with a controlled GLB + R3F implementation. Do not assume a Spline scene can be converted losslessly into an editable Three.js scene.

## 7. PROFOLIO implementation recommendations

Priority 0 — baseline resilience
- Keep all core page content in DOM; no headline, navigation, CTA, or essential information should exist only in the canvas.
- Provide a poster image, meaningful fallback, and error boundary.
- Load the scene only when near the viewport; avoid multiple full-screen canvases.
- Test keyboard/touch interactions and reduced-motion behavior outside the renderer.

Priority 1 — asset pipeline
- Establish a Blender 5.2 → GLB → gltfpack → glTF-Validator → target-renderer visual QA pipeline.
- Preserve source .blend and unoptimized GLB for reproducibility.
- Add a manifest per asset with source file, dimensions, triangles, texture sizes, animations, extensions, compression codecs, license/credit, and validation report.
- Use KTX2 for texture-heavy scenes where its transcoding and browser/WASM requirements are acceptable.
- Use Draco only when its download savings justify decode cost; compare against meshopt on actual target devices.

Priority 2 — frame and draw-call budget
- Share geometry/materials, instance repeated objects, and use batched Points/BufferGeometry for particle systems.
- Default static/mostly-static scenes to demand rendering; explicitly invalidate on input.
- Cap DPR adaptively and tune shadow maps/post-processing by device class.
- Track draw calls, triangles, texture dimensions, initial transfer bytes, parse/decode time, first visible frame, and sustained frame time.

Priority 3 — WebGPU readiness
- Keep renderer-specific code behind a narrow scene-root boundary.
- Inventory GLSL and WebGL-only dependencies before adopting R3F v10.
- Prototype TSL equivalents for shaders that are core to the visual identity.
- Retain WebGL 2 and static fallback coverage until cross-browser tests pass.

## 8. Validation matrix

| Test case | Expected result |
|---|---|
| Desktop browser with WebGPU | Scene renders on the selected WebGPU path; no GLSL compatibility warnings or missing materials. |
| Browser without WebGPU | WebGL 2 or explicit static fallback works; no blank hero. |
| WebGL unavailable / GPU disabled | DOM content remains usable and fallback is visible. |
| Simulated WebGL context loss | Error state is controlled; restore or fallback behavior is tested. |
| Mid-range Android/iOS device | Bounded DPR, acceptable load/decode time, no thermal/battery runaway from an idle scene. |
| Slow network | Poster/DOM appears first; 3D is progressively loaded and failure does not block content. |
| Shared GLB mounted in multiple components | Cached geometry/material/texture remains valid; unmounting one consumer does not dispose another's resources. |
| Optimized GLB | Khronos validator passes; appearance, animation, node names, extras, and extensions match the source intent. |
| Reduced-motion preference | Nonessential motion is reduced or replaced with a static state. |

## 9. Risks and open questions

- No PROFOLIO application source, representative Blender asset, or target-device benchmark was available in this research run. Therefore, no project-specific performance numbers or measured compression ratios are claimed.
- R3F v10 is explicitly alpha in the migration guide checked on 2026-11-01. Recheck release status, peer dependencies, and breaking changes before adoption. [S1]
- WebGPU remains limited-availability according to MDN, and Three.js WebGPURenderer still documents experimental status. Do not make WebGPU the only path for an uncontrolled public audience. [S5][S7]
- gltfpack can discard unknown extensions and has limited custom-data preservation. Treat any custom extras/extension payload as a compatibility gate before optimizing. [S11]
- Spline self-hosted export is documented as Enterprise-only; confirm plan rights and runtime terms before committing to self-hosting. [S24]
- Actual performance depends on GPU, browser, scene, DPR, materials, shadows, and post-processing. All thresholds must be measured on representative devices.

## Version and provenance notes

- Research snapshot: 2026-11-01.
- glTF: Khronos registry lists specification 2.0.1. [S14]
- Blender exporter guidance: Blender 5.2 manual. [S13]
- R3F: current v10 deployment documentation; migration guide labels v10 alpha and states three >=0.185.0 / React >=19.0 <19.4 for that guide snapshot. [S1]
- Three.js: current official API/manual pages were consulted; the pages do not provide a single pinned npm version in the retrieved content. Pin the actual project dependency versions in the lockfile before implementation.
- meshoptimizer/gltfpack and Khronos glTF-Validator: project README pages on their current default branches were consulted; no release tag was pinned in this run. [S11][S12]
- Spline: current official documentation pages were consulted; runtime package semver was not pinned in those pages. [S21][S22][S23][S24]
- Source retrieval date for all references: 2026-11-01.

## Source index

See sources.json for URLs, publisher, version scope, evidence summary, and retrieval date.
