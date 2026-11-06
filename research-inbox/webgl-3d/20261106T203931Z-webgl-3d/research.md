# An asynchronous pixel read is not a current selection

## Decision
For a Three.js WebGLRenderer GPU-picking path, treat every readback as a request with captured scene and pointer identity. A resolved promise means that read's work finished, not that its result still represents the currently displayed scene. Limit pending work and reject stale results before changing selection. This is an original proposed application protocol, not a Three.js built-in guarantee.

## Scope and novelty
The live catalog and all 16 existing webgl-3d reports were retrieved before topic selection. Earlier reports discuss instance-slot identity, CPU versus shader picking and hidden-object eligibility; none develops asynchronous readback acceptance and buffer ownership. This addendum does not replace those policies. It targets the legacy WebGLRenderer API only, at development snapshot 2c916de7ad5a7995e345509fbcf8ee5ae161c05c, inspected 2026-11-06. It is not an installed-version or WebGPU audit.

## Source-backed findings
1. The pinned WebGLRenderer documents readRenderTargetPixelsAsync as the recommended asynchronous alternative when possible. Its implementation submits readPixels into a pixel-pack buffer, restores the current framebuffer, inserts a fence, flushes, awaits probeAsync, then copies with getBufferSubData into the caller-supplied buffer. Thus application state can change while the promise is pending. [S1]
2. The implementation receives a caller-owned TypedArray and returns that same buffer on the successful read path. It does not assign application request IDs, capture a scene revision, or check whether the pointer moved. These are application responsibilities. [S1]
3. Invalid render-target kind, unsupported readable format/type, or out-of-range bounds can throw. If the target has no initialized framebuffer, this snapshot can fall through without returning pixel data. Do not equate promise fulfillment alone with a valid sample. [S1]
4. probeAsync polls clientWaitSync with zero timeout, schedules another probe on TIMEOUT_EXPIRED, rejects on WAIT_FAILED, and otherwise resolves. This is asynchronous waiting, not a latency bound or proof of zero GPU/CPU cost. [S2]
5. In this snapshot deletion of the temporary buffer and sync follows the awaited probe on the success path, without a surrounding finally. Therefore do not assert that every rejection performs that cleanup. This is a narrow source observation, not a measured leak or a claim about every release. [S1]

## Proposed acceptance protocol
- On submission, capture a monotonically increasing request ID, scene revision, pointer revision, selected render-target dimensions and an immutable instance-ID-to-entity mapping or mapping revision.
- Render the ID pass for that captured scene, validate the read region and compatible format/type, then initiate the read. Use a fresh or exclusively leased result buffer per pending request.
- Set a small explicit pending-work limit. For hover, one pending request plus one coalesced latest pointer sample is a reasonable starting policy, not a benchmarked optimum. Never issue an unlimited read on every pointer event.
- On completion, verify the returned value is the expected buffer and that the request, scene, pointer and mapping revisions still match the acceptance policy. Only then decode and apply selection. Do not reuse an old mapping simply because a numeric slot still exists.
- On scene replacement, resize, pointer exit or unmount, invalidate pending application results. Ignoring a stale result is not cancellation of submitted GPU work.
- A click may use a separate explicit snapshot policy rather than the hover rule. Capture that policy at submission so a late hover cannot override an intentional click.
- Release the application buffer lease after settlement and stop submitting when the owner is retired. Do not dispose or repurpose dependencies while an operation still needs them unless the installed renderer's lifecycle behavior has been verified.
- On rejection or an absent result, record failure, clear pending application state and keep an intentional fallback. Do not silently treat a zero-filled buffer as a valid hit.

## Trade-offs and alternatives
CPU raycasting avoids this particular readback queue but has its own geometry/material equivalence limits. A DOM project list provides an explicit selection alternative. GPU ID picking can be useful when rendered coverage is the desired interaction model, but this note makes no speed, precision or portability claim. Color-ID encoding, antialiasing, tone mapping, transparency, coordinate conversion and device-loss recovery each need separate validation.

## Acceptance exercise (not executed)
Use a synthetic scene with two original solid objects and stable entity IDs:
1. Move the pointer while a read is pending; an obsolete hover must not select the old target.
2. Replace or reorder the instance mapping before completion; an old slot must not select a new entity.
3. Resize and retire the scene during pending work; late results must not update retired UI.
4. Burst pointer events; pending reads must remain within the declared bound.
5. Trigger invalid bounds, uninitialized targets and rejected reads; no fulfilled-but-empty or failed read may become a valid selection.
6. Confirm buffers are not reused concurrently and capture rejection behavior for the exact installed version.
7. Measure end-to-end input-to-selection latency and frame timing separately on target devices before choosing the queue limit.

No runtime, performance, accessibility or integration tests were performed. No source code or third-party assets are reproduced.

## Sources
- [S1] Three.js WebGLRenderer, pinned source: https://github.com/mrdoob/three.js/blob/2c916de7ad5a7995e345509fbcf8ee5ae161c05c/src/renderers/WebGLRenderer.js
- [S2] Three.js probeAsync utility, pinned source: https://github.com/mrdoob/three.js/blob/2c916de7ad5a7995e345509fbcf8ee5ae161c05c/src/utils.js
