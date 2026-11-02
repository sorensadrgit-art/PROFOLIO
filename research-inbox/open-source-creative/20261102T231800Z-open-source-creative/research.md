# Tweakpane: authoring panel state is not a versioned scene preset

Run: 20261102T231800Z-open-source-creative · Scheduled: 2026-11-02T23:18:00Z · Evaluated: 2026-11-02T23:23:05Z

## Question and decision
Can a premium interactive portfolio use Tweakpane to tune visual parameters without coupling its saved scene format to the control panel?

**Shortlist: one candidate, Tweakpane 4.0.5.** Use it as an optional authoring adapter, not as the canonical public scene format. This is original synthesis from the implementation, not an upstream prohibition. Its state export includes actual bound values: it is not merely cosmetic UI state.

## Verified source findings
- Tweakpane provides parameter bindings (numbers, strings, booleans, colors and points), monitoring and folders/tabs/buttons. The tagged README describes a compact dependency-free library. Its manifest has no runtime dependencies field, but does list development dependencies and TypeScript declarations. Version 4 uses ES modules; the README points CommonJS users to 3.x. [README](https://github.com/cocopon/tweakpane/blob/4.0.5/README.md), [manifest](https://github.com/cocopon/tweakpane/blob/4.0.5/packages/tweakpane/package.json).
- The documented `exportState()` / `importState()` API transfers blade state. Programmatic changes to bound parameters are not detected by default; `refresh()` updates controls. `dispose()` removes a pane or component. These are documentary API observations, not a tested framework integration. [Misc](https://tweakpane.github.io/docs/misc/).
- The tagged container implementation imports into **existing child controllers by array index**, using `every()`; it exports the child states as an array. The binding export includes key/value, and the input import injects the saved value and fetches it. Thus export carries data, while import follows the current control structure. [container](https://github.com/cocopon/tweakpane/blob/4.0.5/packages/core/src/blade/common/controller/container-blade.ts), [binding](https://github.com/cocopon/tweakpane/blob/4.0.5/packages/core/src/blade/binding/controller/binding.ts), [input](https://github.com/cocopon/tweakpane/blob/4.0.5/packages/core/src/blade/binding/controller/input-binding.ts).
- Latest GitHub release observed: 4.0.5, published 2024-11-03; latest default-branch commit observed: 2026-03-15. The gap alone proves neither abandonment nor a support guarantee. npm registry retrieval failed, so no npm-latest assertion. [release](https://github.com/cocopon/tweakpane/releases/tag/4.0.5), [commit](https://github.com/cocopon/tweakpane/commit/50a18c3dba67cb78431d7248dbe4d5a76336885a).

## Original synthesis: separate two contracts
A saved panel snapshot and a durable scene document serve different purposes. Index-based restoration means reordering or changing control types deserves explicit migration testing; do not assume a property-key based remapper or transactional rollback. The source alone does not establish the exact visible outcome of every mismatch.

Proposed design, **untested**:
1. Own a small scene schema such as `{schemaVersion:1, exposure:1, accent:"#ffffff"}`. Validate finite numbers, explicit ranges, known keys and allowed color syntax before applying it. This example is original, not a required upstream schema.
2. Bind controls to a mutable adapter object, not the renderer's entire object graph. A single apply function copies validated values into the scene.
3. Keep optional Tweakpane snapshots for the same authoring-panel version only. Save a separate panel-version marker. Discard or migrate incompatible snapshots rather than treating `importState()` as application validation.
4. After applying external presets, update the adapter and refresh the pane. Dispose the pane on editor teardown; avoid accumulating duplicate panels during remounts.
5. Ship the finished portfolio with the chosen scene preset; load the authoring interface only in an intentional editing surface. Hiding controls is not authentication or a security boundary.

Future application: a lighting/art-direction workbench shared by designers, producing small reviewed JSON presets for multiple portfolio scenes. A preset diff can be reviewed without distributing the control layout or copying demo artwork.

## Adoption requirements, compatibility and costs
Official setup supports an npm package or an ES-module browser import. For reproducible evaluation, pin `tweakpane@4.0.5`; the docs additionally require `@tweakpane/core` for TypeScript development. Its matching version was not resolved here. No install was performed. Source uses TypeScript; tagged build scripts include Rollup and Sass. A browser DOM is required for the demonstrated pane UI; server-rendered/React applications should instantiate it in a client lifecycle with cleanup. That lifecycle recommendation has not been integration-tested. Browser/version, React/Next and plugin compatibility matrices remain unverified. [setup](https://tweakpane.github.io/docs/getting-started/), [manifest](https://github.com/cocopon/tweakpane/blob/4.0.5/packages/tweakpane/package.json).

No API key, MCP server or hosted backend is needed for the documented local browser setup. The recommended static authoring adapter has no mandatory paid service; normal hosting, maintenance and test labor remain costs. CDN availability/CSP requirements apply only if choosing CDN delivery; vendoring/bundling avoids that runtime CDN dependency. No cost quote or paid product was evaluated.

## Licence, demos and alternatives
The actual tagged licence is MIT: retain copyright and permission notices in copies/substantial portions; warranty is disclaimed. Public visibility is not unrestricted reuse, and this review does not grant rights to third-party plugin assets, example artwork or the separately linked design kit. [licence](https://github.com/cocopon/tweakpane/blob/4.0.5/LICENSE.txt).
Official interactive examples are linked from the documentation; they were read, not operated or copied.

Alternative decision paths (not separately audited shortlist entries): native HTML inputs for a tiny fixed control set; a bespoke accessible editor for production end-user editing; dat.GUI is the upstream inspiration and migration reference, but its current release/licence was not independently reviewed. Prefer no panel at all if the page only needs a fixed approved visual preset.

## Acceptance exercise before adoption — not executed
Build two numeric bindings, export a snapshot, reorder controls, then try importing into both original and changed layouts. Observe return behavior, values and partial mutation rather than assume compatibility. Test schema validation with missing/extra keys and non-finite/out-of-range values. Exercise external preset refresh, mount/unmount cleanup, keyboard-only control and screen-reader labels. Measure actual frame/interaction cost with the intended scene and browsers. None of runtime, performance, accessibility, security, build or integration testing was performed.

## Novelty and scope
Read the live catalog and all 12 existing creative research reports before choosing this question. They cover rails, lightboxes, command menus, text splitting, flowmaps, annotations, freehand strokes, morphing, globes, tilt, 3D text and Lottie readiness—not this panel/preset contract. No same-run directory existed at intake. Existing package files and the pre-existing COBE topic-index omission are preserved.
