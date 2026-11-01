# A portable shader and material architecture for PROFOLIO: TSL, WebGPU/WebGL backends, and the Blender-to-glTF boundary

- Run ID: 20261101T123218Z-tsl-material-bridge-03
- Topic: 3D and WebGL Engineering
- Research date: 2026-11-01
- Status: Documentary research; implementation and device benchmarks not performed
- Target: PROFOLIO research-data branch only

## Executive summary

The most useful unanswered engineering question in the existing 3D collection is not another renderer migration overview. It is how to keep custom visual effects portable across the new Three.js WebGPURenderer, its WebGL 2 fallback backend, the older WebGLRenderer, React Three Fiber (R3F), and the Blender-to-glTF asset pipeline.

The central finding is a distinction that is easy to miss:

**WebGPURenderer falling back to a WebGL 2 backend does not make it compatible with legacy GLSL ShaderMaterial.** WebGPURenderer uses Three.js Shading Language (TSL) and node materials; it can compile TSL to WGSL on WebGPU or GLSL on its WebGL 2 backend. The separate legacy WebGLRenderer is still the route for ShaderMaterial, RawShaderMaterial, onBeforeCompile patches, and libraries tied to that renderer. R3F v10 makes this renderer choice explicit through the Canvas entry point.

This leads to a practical PROFOLIO architecture:

1. Use glTF/GLB as the portable asset interchange contract for geometry, animation, and supported PBR material properties.
2. Keep Blender materials within the documented glTF-exportable Principled BSDF patterns and extensions; do not assume arbitrary procedural Blender node graphs survive export.
3. Use TSL node materials for new custom runtime effects that should target WebGPU and WebGL 2 through WebGPURenderer.
4. Isolate any unavoidable legacy GLSL effects behind a deliberate legacy-renderer boundary, rather than mixing them into a supposedly portable TSL scene.
5. In R3F, separate changing uniform values from shader-graph structure so ordinary React state changes do not force graph reconstruction or shader recompilation.
6. Treat R3F v10 TSL compute/storage hooks as experimental and optional, not as a baseline dependency for the first production scene.

## 1. Research question and novelty

The existing catalog already covers:
- general Three.js/R3F and WebGL/WebGPU selection;
- Blender-to-glTF optimization and GLB delivery;
- broad R3F performance and resource lifecycle;
- WebGL context restoration and WebGPU device loss;
- a separate narrow addendum about stale GLB loads after route changes.

This run therefore focuses on a different gap: **shader-language and material portability across authoring, interchange, renderer, and React integration boundaries**. It does not repeat the stale-load, general compression, or renderer-loss reports.

## 2. Evidence and current version context

The official R3F v10 documentation currently labels the v10 deployment as an alpha release. Its migration guide states a Three.js minimum of 0.185.0 and React 19.0 through versions below 19.4. It also records that the default R3F import now uses WebGPURenderer, while the legacy entry uses WebGLRenderer. Treat these as version-sensitive migration facts, not a statement that PROFOLIO's own dependencies already meet them.

The R3F v10 Render Pipeline documentation says Three.js renamed PostProcessing to RenderPipeline in r183. Pin compatible versions of three, @react-three/fiber, @react-three/tsl, and React together; do not copy examples from the moving v10 documentation into an unpinned application.

## 3. Critical renderer distinction: backend fallback is not renderer interchangeability

### Verified facts

- Three.js describes WebGPURenderer as a universal renderer that can use a WebGPU backend or fall back to a WebGL 2 backend.
- TSL is compiled to WGSL or GLSL depending on the backend.
- Three.js explicitly says ShaderMaterial, RawShaderMaterial, and onBeforeCompile-based customizations are not supported by WebGPURenderer and must be ported to node materials/TSL.
- R3F v10's default @react-three/fiber Canvas uses WebGPURenderer. Its @react-three/fiber/legacy entry uses WebGLRenderer for GLSL and WebGL-only libraries.
- R3F's v10 migration guide says a GLSL material on the default WebGPU Canvas can render as a blank/default material with a development warning.

### Engineering interpretation

There are two independent decisions:
1. Which Three.js renderer API is used: WebGPURenderer or WebGLRenderer.
2. Which backend WebGPURenderer uses at runtime: WebGPU or WebGL 2.

A WebGL 2 backend selected by WebGPURenderer still expects the TSL/node-material path. It is not the same compatibility mode as selecting WebGLRenderer with GLSL ShaderMaterial.

### PROFOLIO recommendation

Use the default R3F v10 WebGPURenderer path for new TSL-authored effects only after dependency and browser validation. If an existing effect genuinely requires ShaderMaterial, RawShaderMaterial, onBeforeCompile, or a WebGL-only post-processing library, isolate it in a separately reviewed legacy Canvas/scene boundary. Avoid assuming one material instance can be used unchanged by both renderer families.

## 4. TSL graph architecture: dynamic values versus structural changes

### Verified R3F v10 behavior

The @react-three/tsl hooks have distinct roles:

- useUniforms carries changing values. The documentation says values are updated in place and reach the GPU without rebuilding or recompiling the graph.
- useNodes registers shared TSL graphs once per generation. Components can reuse the same graph objects.
- useLocalNodes creates a component-local graph, analogous to memoized graph construction.
- A changing scalar, vector, color, or other uniform value should flow through a uniform.
- A change to the graph's structure uses rebuildNodes (or the documented hot-update generation mechanism).
- Scoped stores allow effects such as fog, particles, terrain, and character materials to keep names separate.

### PROFOLIO recommendation

Define a stable graph for each reusable effect and expose only intentional controls as uniforms:
- color tint;
- roughness/metalness modulation;
- emissive strength;
- displacement amplitude;
- time and normalized interaction progress;
- particle density or force strength.

Do not rebuild a TSL graph on every pointer movement or React render. Treat graph topology changes as an explicit operation with a visible lifecycle and a targeted validation pass.

For reuse, give each effect a stable scope (for example, scene-fog, hero-surface, particles, or project-materials). Document which component registers a node and which components consume it; the R3F docs note that registration order matters for readers that access a resource before it exists.

## 5. The Blender-to-glTF boundary: portable PBR is a contract, not a full Blender shader export

### Verified Blender 5.4 exporter behavior

The Blender 5.4 manual describes glTF material export as being constructed from recognized nodes in the Blender material. Its documented core workflow centers on Principled BSDF inputs and supported glTF extensions.

Examples include:
- Base color from Principled BSDF Base Color or a connected image;
- metallic and roughness from Principled BSDF values or the expected packed texture channels;
- emissive maps and emissive-strength extension handling;
- clearcoat;
- transmission;
- volume via Volume Absorption and the glTF Material Output thickness input;
- sheen, specular, IOR, and other listed extensions where the exporter supports them.

This does not mean arbitrary Blender procedural shader graphs, arbitrary nodes, or all Eevee/Cycles appearance can be reproduced by every glTF viewer. The exporter maps recognized patterns into glTF's portable material model and extensions.

### Verified glTF conventions

The Khronos glTF 2.0 specification defines a metallic-roughness PBR material model. Its texture conventions matter:
- base-color texture RGB uses the sRGB transfer function;
- metallic-roughness texture uses the green channel for roughness and blue for metalness, with linear transfer;
- normal textures use linear values and the tangent-space +Y-up convention;
- extension names used by an asset are declared in extensionsUsed, and extensions required to correctly load/render it are declared in extensionsRequired.

### PROFOLIO recommendation

Treat GLB as the portable asset/material baseline, not as a container for arbitrary runtime shader logic. Author a stable Principled BSDF material that survives export, then layer TSL runtime effects on top in Three.js. Keep visual effects that are essential to identity from depending on unsupported Blender node graphs.

For each hero material, maintain:
1. the Blender source file;
2. the exported GLB;
3. a short material feature sheet listing core PBR values and each glTF extension used;
4. a Three.js/R3F render check;
5. a fallback appearance for consumers that do not support a non-core extension.

## 6. Material extension support must be checked at both ends

The current Three.js GLTFLoader documentation lists support for extensions including:
- KHR_materials_anisotropy;
- KHR_materials_clearcoat;
- KHR_materials_dispersion;
- KHR_materials_emissive_strength;
- KHR_materials_ior;
- KHR_materials_specular;
- KHR_materials_transmission;
- KHR_materials_iridescence;
- KHR_materials_unlit;
- KHR_materials_volume;
- KHR_texture_basisu;
- KHR_texture_transform;
- EXT_meshopt_compression;
- EXT_mesh_gpu_instancing;
- EXT_texture_avif and EXT_texture_webp.

The Blender 5.4 exporter list is not identical to the loader list. A feature listed by GLTFLoader is not automatically proof that the installed Blender exporter emits it, and exporter support is not proof that every target browser/device path renders it identically.

### Practical compatibility rule

Check three separate questions for each extension:
1. Does the authoring/exporter version emit it?
2. Does the exact installed GLTFLoader version parse it?
3. Does the selected renderer/backend implement the visual behavior needed by the scene?

Record extensionsUsed and extensionsRequired from the actual exported asset. Do not mark an extension required merely because it improves appearance if a deliberate fallback can be rendered without it.

## 7. Post-processing is part of the shader migration, not a drop-in swap

Three.js documents that EffectComposer's legacy effect passes are not supported by WebGPURenderer. Its newer pipeline expresses effects as TSL node compositions and replaces EffectComposer with RenderPipeline.

The R3F v10 useRenderPipeline hook:
- creates a RenderPipeline and default scene pass;
- configures outputNode and optional MRT;
- frees its pipeline and scene pass when its owning component unmounts;
- supports dynamic values through uniforms;
- can register additional passes;
- requires the application to set outputNode when replacing the default scene-pass output.

The documentation also warns that passes returned from callbacks are not disposed by the hook. This is an ownership detail that should be explicitly covered by cleanup tests.

### PROFOLIO recommendation

Keep the first cinematic scene's post-processing modest. Introduce one owner component for the render pipeline. Avoid mounting multiple competing pipeline owners. If using custom passes, document their resource ownership and dispose behavior. Validate color-space/tone-mapping order and GPU memory cost, particularly for MRT attachments.

## 8. Advanced compute and particle systems: useful, but experimental

The R3F v10 compute documentation labels useBuffers and useGPUStorage as experimental and warns that APIs may change between releases.

It demonstrates GPU storage buffers, storage textures, TSL compute functions, and an instanced particle system. It also provides explicit disposeBuffers and disposeStorage operations.

### PROFOLIO recommendation

Do not make GPU compute a prerequisite for the first portfolio 3D scene. Start with CPU-driven or instanced effects whose complexity is easy to bound. Introduce GPU compute only when a measured workload justifies it, and keep a non-compute fallback for devices or versions where the feature is unavailable.

A high particle count in an example is not a performance guarantee. Actual limits depend on device, shader workload, memory, browser, resolution, and thermal behavior.

## 9. Suggested architecture for PROFOLIO

| Layer | Responsibility | Portability target |
|---|---|---|
| Blender source | Meshes, UVs, animation, Principled BSDF | Editable authoring |
| GLB/glTF | Geometry, animation, PBR and supported extensions | Standard interchange |
| Three.js GLTFLoader | Parse asset and extension data | Version-pinned loader |
| Shared scene model | Camera, object hierarchy, interaction state | Renderer-agnostic where practical |
| TSL node materials | New procedural surface effects | WebGPURenderer on WebGPU or WebGL 2 backend |
| Legacy GLSL island | Existing GLSL-only shader/library | WebGLRenderer via R3F legacy entry |
| RenderPipeline | TSL-based post-processing | WebGPURenderer |
| Fallback scene | Static or simplified material presentation | No custom shader dependency |

This is a recommended separation of concerns, not an implementation already present in PROFOLIO.

## 10. Implementation and validation sequence

1. Inspect the actual PROFOLIO package manifest and lockfile before choosing R3F v9 or v10. No application lockfile was inspected during this research run.
2. Inventory all ShaderMaterial, RawShaderMaterial, onBeforeCompile, and EffectComposer dependencies in any future 3D feature.
3. Classify each custom effect as: built-in material, TSL-portable, legacy GLSL-only, or not yet understood.
4. Build a tiny TSL material proof that runs on WebGPURenderer with WebGPU enabled and with forceWebGL enabled.
5. Build a separate legacy ShaderMaterial proof using the R3F legacy Canvas.
6. Export a representative Blender Principled BSDF material to GLB and inspect the actual material JSON and extension declarations.
7. Load that GLB with the exact pinned GLTFLoader version.
8. Compare base color, metalness, roughness, normal orientation, emissive, clearcoat, transmission, and volume behavior.
9. Add one TSL overlay effect using stable graph structure and changing uniforms.
10. Add one post-processing pass and verify pipeline ownership/cleanup.
11. Only then evaluate GPU compute for a measured use case.
12. Record browser/device/backend results rather than inferring support from API availability.

## 11. QA matrix

| Test | Expected result |
|---|---|
| TSL material on WebGPU backend | Correct compiled material and expected appearance |
| Same TSL material on WebGPURenderer WebGL 2 fallback | Same intended shader behavior within documented precision/rendering differences |
| GLSL ShaderMaterial on WebGPURenderer | Rejected by design; do not accept blank output |
| GLSL ShaderMaterial on R3F legacy Canvas | Renders using WebGLRenderer |
| Dynamic uniform changes | Values update without rebuilding graph |
| Structural graph change | Explicit rebuild and material revalidation |
| Blender export of recognized Principled BSDF material | Expected glTF PBR properties emitted |
| Arbitrary procedural Blender nodes | Confirmed not assumed portable; compare exported result |
| KHR material extension | Exporter, loader and renderer support verified separately |
| RenderPipeline unmount | Pipeline owner cleans up; no orphaned pipeline |
| Custom returned post-process pass | Explicit disposal verified |
| Experimental compute disabled | Scene remains usable with fallback effect |

## 12. Limitations and confidence

- This is documentary research based on official documentation and specifications accessed on 2026-11-01.
- R3F v10 documentation explicitly identifies v10 as alpha; its APIs and version requirements can change.
- The R3F v10 docs currently specify Three.js >=0.185.0 and React >=19.0 <19.4. These are not confirmed PROFOLIO dependency versions.
- Three.js describes WebGPURenderer as experimental and says some scenes may still perform better or have better feature support under WebGLRenderer.
- Blender 5.4 documentation was retrieved through search indexing because the manual page itself returned an access error when opened directly; the indexed official manual content was readable.
- No shader was compiled, no GLB was exported, and no visual/device benchmark was run in this research task.
- No website, application, deployment, or external memory was modified.

## 13. Sources

See sources.json for source URLs, publisher, version context, retrieval date, evidence summaries, and claims supported.
