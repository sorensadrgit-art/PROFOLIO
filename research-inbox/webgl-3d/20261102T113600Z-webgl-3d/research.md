# Transparency is a material contract, not a sorting switch

Run: `20261102T113600Z-webgl-3d` • Scheduled: 2026-11-02 11:36 UTC • Evaluated: 2026-11-02 11:38 UTC

## Question and incremental value

How should an interactive portfolio distinguish cutouts, fades and glass without treating `depthWrite=false` or `renderOrder` as a universal transparency repair?

The live catalog and all eleven existing WebGL research packages were read before selection. None has a dedicated transparency classification or sorting acceptance contract. This complements the prior GPU-deformation, output-color and measurement packages; it does not repeat their bounds, color-space or profiling prescriptions. The same-slot run and unindexed WebGL package directories were absent at inspection. Repository head was `7ee6e6d1de96f0044eb4642d7228b0100ba746a7`.

## Source findings

Evidence is pinned to Three.js commit `7e89746a687bb88271253698fa108b1dd59825d0`, observed commit timestamp 2026-10-30 07:56:52 UTC. Its package declares `three` 0.187.0. This is an upstream snapshot, not an audit of the application's installed version. Release date was not checked. All numbered references resolve through `sources.json`.

1. **Opacity and classification are separate.** `Material` defaults to opaque classification, opacity 1 and depth writes enabled. Its documentation distinguishes ordinary blended transparency from alpha testing (threshold rejection) and alpha hashing (stochastic rejection). Alpha hashing trades grain for avoiding the documented blending sorting problem; the source suggests TAA to reduce noise, not that a temporal pipeline is free or required. [S1]

2. **Sorting is per render item, not a general geometric solution.** The WebGL render list places positive-transmission materials in a transmissive list, otherwise transparent materials in a transparent list, otherwise opaque. Its transparent default comparator prioritizes group order, then render order, then descending projected depth and finally object ID. The renderer draws each resulting item using its geometry/group. This inspected path is not a per-pixel ordering or intersecting-triangle repair algorithm. `Object3D.renderOrder` explicitly retains separate opaque/transparent sorting. [S2–S4]

3. **Depth testing and depth writing answer different questions.** The former controls depth comparison; the latter controls changes to the depth buffer. Disabling writes alone therefore does not change the render-list comparator. That is a source-derived inference, not a tested visual outcome. The material documentation also says disabling depth testing implicitly disables depth writes. Avoid conflating either flag with correctly compositing intersecting surfaces. [S1–S3]

4. **Double-sided blending has a specific cost.** For `transparent=true`, `DoubleSide` and `forceSinglePass=false`, the inspected WebGL path renders back faces then front faces using two direct draw submissions. The flag can avoid this for cases where the second pass adds no visual value, such as flat vegetation, according to the material documentation. It is not a blanket quality-preserving optimization for arbitrary glass geometry. [S1,S3]

5. **Glass transmission is not an opacity fade.** `MeshPhysicalMaterial` documents physically based transmission as a more realistic thin-glass option and instructs keeping opacity at 1 when transmission is nonzero. Its complex features add per-pixel cost. The WebGL renderer contains a separate transmission rendering pass and target. Do not promise that adding transmission solves all nested-glass/intersection cases or that its cost equals ordinary alpha blending. [S3,S5]

## Original synthesis: classify the visual intent first

The following decision table and process are recommendations, not measured or integrated results.

| Visual intent | First candidate | Acceptance risk and alternative |
|---|---|---|
| Leaf, logo or screen with binary holes | Opaque classification plus a chosen `alphaTest` threshold | Edge aliasing and loss of semi-transparent detail; adjust art/threshold. MSAA-dependent alpha-to-coverage is a separate candidate, not guaranteed by post-process AA. |
| Soft fade on a few nonintersecting surfaces | Ordinary blending; explicitly decide depth-write behavior | Orbit and overlapping geometry can expose ordering errors. Simplify layer geometry before introducing fixed render orders. |
| Many soft coverage layers where noise is acceptable | Evaluate alpha hashing as an alternative, not a combined magic flag set | Inspect grain at rest and during motion; adding temporal accumulation changes memory, latency and testing requirements. |
| Glass-like physical transmission | Physical material with transmission and opacity 1 | Additional shading/pass cost, thin-versus-thick appearance and nested surfaces need separate tests. Use an opaque glossy approximation if it satisfies the art direction. |
| UI caption or essential label | DOM layer rather than transparent 3D geometry | Keep content readable independently of the GPU; this is a product/accessibility recommendation, not certification. |

**Debugging order:** record renderer/version and material flags → isolate one surface against a contrasting opaque object → distinguish missing coverage from wrong order → rotate camera and move objects through each other → change one classification/depth flag at a time → record intentional concessions. Do not scatter arbitrary `renderOrder` values across components. A deliberate fixed overlay order may be valid for a constrained composition, but document its camera and occlusion assumptions.

For imported assets, inspect the material actually produced by the loader before overriding it. This report does not verify Blender/glTF exporter mapping, custom shaders, R3F prop compatibility, or WebGPURenderer equivalence. A custom fragment shader may implement coverage differently from built-in materials.

## Practical fit, operation, licence and alternatives

Three.js is a JavaScript 3D library with ES-module and CommonJS build exports; the inspected package lists development tooling but no runtime dependency block. This research adds no library requirement beyond the application's selected Three.js renderer/material path. A future implementation should pin the existing compatible version rather than upgrade just to follow this note. R3F is optional and no React/R3F version is asserted. [S7]

No API key, backend, MCP service, agent, paid service or runtime account is required for these renderer decisions. Browser assets can be served through the existing static hosting path. No install or hosting change was performed. Financial cost is not quantified: relevant operational costs are developer visual QA, extra draw submissions, fill/overdraw, transmission targets and any optional temporal pipeline. No GPU-memory or frame-time savings were measured.

The inspected upstream `LICENSE` is MIT: reuse of the software requires retaining its copyright/permission notice in copies or substantial portions; it is supplied without warranty. Public demo images, models, fonts and unrelated assets are not granted unrestricted reuse by that fact. No demo assets or implementation source are copied into this package. [S6]

Maintenance evidence is limited to the observed upstream commit date and package version. Issue responsiveness, release cadence and support guarantees were not assessed. An official physical-transmission example exists at the pinned source URL [S8]; its source presence was checked, but it was not launched. The former manual transparency URL returned 404 and was excluded as evidence rather than treated as current documentation.

Alternative strategies are intentionally small: simplify/split nonintersecting geometry, replace glass with opaque glossy art, use cutouts instead of soft blends, evaluate hashing, or move editorial UI to DOM. Advanced order-independent transparency is outside this bounded package and is not recommended as a drop-in dependency.

## Original future application and proposed acceptance checks

An asset manifest could include `coverageIntent`, expected overlap behavior, depth-write ownership, allowed camera range and visual fallback. This would let reviewers reject an accidental blended material before it reaches the cinematic scene. This schema is an original proposal, not an existing project feature.

Proposed fixture, **not executed**:
- One cutout card, one blended card, two intersecting planes and a transmission object; inspect against both light and dark backgrounds.
- Orbit through front/back and grazing angles; reorder scene insertion; move planes through one another. Record known artifacts rather than only a favorable screenshot.
- Compare depth writes on/off while retaining depth testing; confirm opaque occluders still satisfy the intended design.
- Compare double-sided two-pass versus single-pass appearance and draw counts on the actual renderer; do not infer total frame cost from one flag.
- Inspect hashing at rest and in motion with the actual AA/post-processing path; verify any claimed noise improvement.
- Verify keyboard-readable DOM labels, reduced-motion presentation and static fallback separately; these are not properties conferred by material settings.

Documentary QA is the only intended approval level. Runtime, performance, device, accessibility and integration tests remain unperformed.
