# Disabling orbit input does not transfer camera ownership

Run: 20261102T233600Z-webgl-3d. Scheduled: 2026-11-02T23:36:00Z. Evaluated: 2026-11-03. Documentary research only.

## Question and incremental value

How should a portfolio hand a camera between a cinematic sequence and OrbitControls without treating disabled input as a frozen camera?

The live catalog and fifteen existing WebGL reports were retrieved before selection; their questions and control-related passages were checked. They address assets, render lifecycle, picking, shaders and animation completion, not this camera-writer handoff. The topic directory contained those fifteen run directories and no same-run or unindexed run directory. The unrelated catalog omission remains untouched.

## Source findings

Implementation claims below are pinned to Three.js commit e02b502efd394f5e982103159b9815b2ed147aad, dated 2026-11-02T16:23:21Z. Its manifest says 0.187.0: this is a development-source version, not a verified installed or npm-release version. [S1,S3,S5]

1. **Input gating is not an update guard.** The inspected pointer-down, pointer-move, wheel and key handlers check enabled. The update method does not check enabled before applying motion, limits, camera position and lookAt(target). Consequently an application that continues calling update can still have its camera rewritten after setting enabled=false. This follows from source inspection, not a runtime trace. [S1]

2. **A target and a pose form a coupled state.** update derives spherical coordinates from camera position minus target, applies limits and queued movement, writes position, and points the camera at target. A cinematic writer that changes only camera rotation can therefore be overwritten by the next controls update. Setting position alone does not specify the intended focus. This does not imply every arbitrary cinematic pose is representable by an orbit camera with fixed up and configured constraints. [S1]

3. **Residual damping survives a simple input toggle.** With damping enabled, update applies a fraction of spherical and pan deltas and decays the remainder. Toggling enabled does not clear those fields in the inspected implementation. Disabling damping and calling update applies the full pending deltas before clearing them; that is not a zero-motion cancellation operation. reset restores saved target, position and zoom, then calls update, so it should not be assumed to be a pure queue flush either. These are version-pinned internal observations, not advice to mutate private fields. [S1]

4. **Interaction end is not a settled-pose receipt.** Pointer-up emits end when the active pointer list becomes empty, while damping can still be processed by later updates. The wheel handler brackets its work with start and end events. A consumer should not infer that end certifies all camera motion has ceased. The change-event/boolean condition uses displacement, rotation and target thresholds; absence of a change report is not a mathematical proof that all residual deltas are exactly zero. [S1]

5. **Timing and ownership need separate decisions.** Official documentation requires updates for damping or auto-rotation and permits delta seconds to make auto-rotation speed frame-rate independent. In the inspected implementation, deltaTime feeds auto-rotation, whereas damping uses a per-update factor. Passing delta must not be advertised as making all damping behavior frame-rate independent. The official addon import is three/addons/controls/OrbitControls.js. [S1,S2]

## Original synthesis: an explicit camera-writer protocol

The following is a proposed, untested application design, not an upstream guarantee:

- Maintain one mode owner: interactive, cinematic, handing-back, or retired. During cinematic mode, disable user input and gate the application's controls-update subscription as well. Audit wrappers and other callbacks: merely gating one of two update callers is insufficient.
- Define cancellation behavior before starting the cinematic move. Choose whether to let an existing gesture settle before takeover, preserve residual motion intentionally, or replace the controls instance at a controlled boundary. Do not reach into underscore-prefixed state as the default integration.
- For a clean-instance policy, dispose the old controls and remove application-owned listeners/subscriptions; disposal alone does not remove every callback the application registered elsewhere. Configure the new instance and intended camera position, target, zoom and constraints before allowing input or normal frame updates. The constructor itself calls update, so stage creation while no competing cinematic writer is active and account for that initial write. A fresh instance loses saved state/listeners/configuration unless the application deliberately restores them.
- Treat hand-back as a transaction owned by the frame coordinator: stop the cinematic writer, choose an orbit-compatible position/focus/zoom, establish constraints, synchronize controls, then enable input. A controls update may clamp the proposed pose; record the actual resulting pose rather than assuming the requested pose survived unchanged.
- Keep transitions interruptible by route generation. An obsolete completion callback must not re-enable controls on the next route.
- For demand rendering, ensure both scripted transitions and continuing damping have a scheduling owner. A pointer end event alone is not the signal to stop requesting frames. Exact scheduling policy and settling tolerance must be tested against the actual wrapper; no R3F/Drei wrapper was audited here.
- Keep a keyboard-operable DOM path and static/reduced-motion presentation for essential portfolio content. These are design requirements, not accessibility certification.

A useful diagnostic record distinguishes input enabled, active camera writer, controls-update subscription, target, requested pose and post-sync pose. It should expose competing owners rather than masking a camera snap with a longer easing duration.

## Proposed acceptance exercise — not executed

Use an original primitive scene and one perspective camera; no downloaded art is needed.

1. Start damping, release the pointer, then set enabled=false while continuing update. Record camera/target changes; compare with explicitly gated updates.
2. During a scripted rotation, deliberately leave controls updating. Detect the competing writer; then repeat with single ownership.
3. Hand back with a mismatched target and with an explicit intended target. Compare resulting orientation, including a pose beyond configured polar/distance limits.
4. Contrast residual-preservation and clean-instance policies. Verify listener/subscription cleanup, restored configuration, constructor-time writes and saved-state behavior.
5. Compare different update rates with auto-rotation delta seconds and damping. Do not assume identical damping trajectories; measure the actual behavior.
6. Interrupt by route change and reduced-motion preference; require obsolete callbacks not to reactivate controls. Exercise DOM keyboard navigation separately.

No scene, demo, browser, GPU or test fixture was executed.

## Practical fit, licence, maintenance, operation and alternatives

Three.js is a JavaScript 3D library; OrbitControls is its addon for orbiting, dollying and panning. The pinned manifest exports addons, ES modules and CommonJS core entry points and contains development dependencies but no runtime dependencies field. Existing Three.js projects need no separate controls package for this addon; keep core and addon on the same chosen version and verify the application's lockfile. No installation was performed. Framework compatibility, parented-camera behavior and specialized control wrappers are outside this investigation. [S1,S3]

The observed November 2 source commit shows repository activity, not a maintenance promise or a verified release publication. Release date and installed application version remain null. The official docs supply an API usage example; no demo was operated. [S2,S5]

The actual licence is MIT, requiring notice retention for copies/substantial portions and disclaiming warranty. It is not permission to reuse unrelated models, fonts or demo assets. This package contains original synthesis, not copied implementation or artwork. [S4]

No backend, API key, MCP connection or paid service is required for the proposed client-side handoff. Hosting, engineering, CPU frame updates and rendered frames still cost resources; no savings or benchmark result is asserted. An orbit-only story that animates position and target under one owner is a simpler alternative when art direction allows it. A cinematic-only camera with semantic DOM navigation avoids handing control back at all. A different controls library is another possibility but was not separately evaluated or shortlisted.

An original future application is a case-study camera director with explicit visitor takeover: the reader can leave a scripted shot, inspect an object, and return to a stable named viewpoint without an invisible second camera writer.

## Limitations and approval meaning

Independent documentary review only. No runtime, integration, visual, accessibility, performance, input-device or frame-rate tests. Findings are pinned to one development snapshot; moving docs can differ. The proposed protocol requires implementation testing. Approval means this research package's review, not Gemini approval or deployment permission.

## Sources
- [S1] [Pinned OrbitControls source](https://github.com/mrdoob/three.js/blob/e02b502efd394f5e982103159b9815b2ed147aad/examples/jsm/controls/OrbitControls.js)
- [S2] [Official OrbitControls documentation](https://threejs.org/docs/pages/OrbitControls.html)
- [S3] [Pinned package manifest](https://github.com/mrdoob/three.js/blob/e02b502efd394f5e982103159b9815b2ed147aad/package.json)
- [S4] [Pinned MIT licence](https://github.com/mrdoob/three.js/blob/e02b502efd394f5e982103159b9815b2ed147aad/LICENSE)
- [S5] [Observed source commit](https://github.com/mrdoob/three.js/commit/e02b502efd394f5e982103159b9815b2ed147aad)

