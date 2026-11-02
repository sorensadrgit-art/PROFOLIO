# Hiding a scene object is not an interaction policy

Run: 20261102T173600Z-webgl-3d. Scheduled: 2026-11-02T17:36:00Z. Research began at 17:33 UTC on 2026-11-02 after early delivery of the scheduled request. Documentary research only.

## Question and incremental value

When a Three.js portfolio hides a group or changes camera layers, what must its picking owner do separately to prevent unintended selection?

The live catalog and thirteen prior WebGL reports were retrieved before selection. Existing instance-slot identity and GPU-deformation reports address different picking failures. None defines visibility-versus-picking traversal and layer ownership. No same-run package or unindexed WebGL run directory was present. The initial branch head was c7fd061882f3cd0f874f1fd25e8b1ba47d59cf5f. This is not a framework event-system audit.

## Source findings

The implementation observations below are pinned to Three.js commit e02b502efd394f5e982103159b9815b2ed147aad, committed 2026-11-02T16:23:21Z. Its package declares 0.187.0, which identifies a development snapshot rather than the application's installed version. See numbered URLs in sources.json.

1. **The two traversals answer different questions.** Raycaster's internal traversal tests an object's layers before calling its raycast method, but does not test Object3D.visible. Its recursive traversal continues through children even when that parent's layer does not match. A called custom raycast returning false can stop propagation in this snapshot; that implementation detail is not a recommendation to rely on undocumented pruning across versions. [S2]

2. **WebGL rendering has a different visibility gate.** WebGLRenderer.projectObject immediately returns for visible=false, pruning that rendering subtree. It tests the object's layers against the camera for its own render processing, then traverses children outside that layer test. Therefore a parent layer mismatch is not a general subtree exclusion for either inspected traversal. This is a source comparison, not a measured screen result. [S3]

3. **A camera-derived ray does not inherit the camera's complete filtering policy.** setFromCamera updates ray origin/direction and the camera reference in the inspected code. It does not copy camera layers or clipping distances into raycaster.layers, near or far. These remain separate inputs. Raycaster's API documents its own distance range and selective layers. Geometric inference, not a quoted API guarantee: blindly copying camera near/far is not an exact perspective-frustum clipping solution: ray distances and camera-space clip planes describe different geometry, especially off-axis. [S1,S2]

4. **A geometry intersection is not proof of a visible pixel.** The inspected ordinary Mesh path checks bounds and triangles, honors material side and ray distance, and does not sample alpha textures or test material.visible. Consequently matching object visibility and layers alone does not reproduce fragment-level coverage or final occlusion. Custom raycasts and specialized objects may behave differently. [S4]

5. **Layer assignment has destructive and additive forms.** Layers.set replaces membership, while enable adds it. Each object has its own membership. A picking-only layer must be deliberately applied to the intended descendants rather than assumed to flow from a Group. Matrix updates remain another independent responsibility when application code disables automatic updates. [S5,S8]

## Original synthesis: separate eligibility from intersection

The following protocol is an untested application recommendation:

- Maintain an explicit registry of selectable semantic targets, with a stable project ID and a current interaction-enabled flag. Visual state may inform this flag but is not its sole source of truth. Remove obsolete owners when routes change.
- Choose whether hidden ancestors suppress selection. For a conventional portfolio they should; for intentional invisible hit proxies they may not. Encode that choice explicitly, rather than changing visible and hoping picking follows.
- For visible-only interaction, reject candidates whose ancestor chain is hidden before expensive intersections where feasible. Do not filter only a root list and then recursively reintroduce excluded children. One simple candidate design is an explicitly collected list of eligible mesh leaves queried non-recursively; specialized objects require their own adapter.
- Set picking layers independently and document their relation to camera layers. If camera-visible-only selection is required, test each candidate against the camera as well as the picking mask. Keep intentional proxies as a named exception.
- Define occlusion separately. Casting only at selectable objects allows selection through a non-selectable wall. Include intentional blockers in the intersection policy if the nearest visible surface must win. Alpha-cutout holes need a dedicated geometry/coverage policy; do not claim that an ordinary CPU triangle query exactly follows transparent pixels.
- Re-evaluate hover and clear selection when eligibility changes, even if the pointer stays still. Keep the existing frame/event owner responsible for that refresh; this note does not prescribe R3F event APIs.
- Keep essential project navigation in semantic DOM controls so pointer picking is an enhancement, not the only route to content. This is a product recommendation, not an accessibility test result.

### Original acceptance exercise, not executed

Create a parent Group, a selectable child mesh, a non-selectable opaque blocker and an intentionally invisible proxy in a small local scene. Record renderer/version, camera layers, raycaster layers, recursion setting and each object's visibility.

1. Hide the parent. Compare raw recursive Raycaster results with the application's visible-only policy. Require the application to suppress the child, without assuming raw raycasting does so.
2. Put the parent on an excluded layer but keep the child on an included layer. Confirm the policy does not rely on parent mismatch pruning.
3. Change camera layers while leaving the raycaster unchanged. Require the chosen camera-visible-only mode to update its eligibility independently.
4. Change a material to invisible or add an alpha-cutout texture. Document whether the interaction surface intentionally differs from appearance; test any coverage-aware alternative separately.
5. Place the blocker in front of a selectable object. Require occlusion mode to block selection, while an explicitly labeled through-selection mode may select it.
6. Toggle eligibility under a stationary pointer, remove a selected route, and exercise keyboard navigation in the DOM fallback.

These are proposed assertions, not passed tests. No browser, GPU, screen reader or pointer simulation ran.

## Practical fit, licence, maintenance and costs

Three.js is a JavaScript 3D library with ES-module and CommonJS entry points. The inspected package has development tooling but no runtime dependency block. The protocol can fit an existing Three.js application without another package; wrappers such as React Three Fiber may add their own event filtering and were not audited here. Verify behavior against the actual lockfile and custom raycasts before integration. The inspected recent commit demonstrates recent repository activity, not a maintenance guarantee or verified release date. [S6]

The pinned code's MIT licence requires preservation of copyright and licence notices for copies or substantial portions and disclaims warranty. This report reproduces no implementation or protected scene assets. A code licence does not establish rights to unrelated demo models, fonts or textures. [S7]

No API key, MCP server, agent connection, backend or paid service is needed for this client-side policy. Ordinary hosting, engineering and CPU query costs remain; no cost or performance measurements were made. For dense scenes, a purpose-built coarse proxy may reduce geometry work but must be validated; this report makes no measured acceleration claim.

Alternatives: a DOM project list avoids ray-picking ambiguity; explicit coarse hit proxies favor forgiving interaction; GPU ID picking can be designed around rendered coverage but adds rendering/readback and synchronization requirements and is not automatically pixel-equivalent for every material. Choose based on the intended interaction contract, not a blanket faster/better claim.

An original future application is a gallery's debug overlay that shows semantic eligibility, ancestor visibility, camera/picker masks and the selected blocker separately. It could explain why an item is selectable even when hidden, or visible but intentionally inactive. No overlay was built.

## Limitations

Source inspection and independent documentary QA only. No runtime, integration, visual, accessibility, usability or performance tests. WebGPURenderer, R3F propagation, custom shaders, accelerated raycasters and every specialized object are outside this bounded investigation. Findings do not grant deployment permission or certify a production interaction system.
