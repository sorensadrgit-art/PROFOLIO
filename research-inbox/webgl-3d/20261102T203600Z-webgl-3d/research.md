# A finished animation event is not a committed final pose

Run: 20261102T203600Z-webgl-3d. Scheduled: 2026-11-02T20:36:00Z. Evaluated: 2026-11-02. Documentary research only.

## Question and incremental value

When a Three.js clip reaches its end, what can a portfolio sequence safely infer before changing UI state or retiring its animation owner?

The live catalog and fourteen prior WebGL reports were retrieved before selection. The earlier independent-character report mentions restart versus resume and clampWhenFinished. This addendum does not repeat that ownership setup: it examines the ordering of the finished event relative to pose application, and separates playback, influence and retirement. No same-run or unindexed topic run directory was found at intake. The unrelated COBE topic-index omission is preserved.

## Source findings

Implementation findings are pinned to Three.js e02b502efd394f5e982103159b9815b2ed147aad, committed 2026-11-02T16:23:21Z, whose package identifies 0.187.0. That is a development source snapshot, not a verified installed or npm-release version. [S1,S2,S5,S7]

1. **The event occurs inside the update, before binding application.** In the pinned AnimationAction, _updateTime dispatches finished when a final loop reaches its boundary. Its caller then computes effective weight and, only when that weight is positive, evaluates track interpolants and accumulates contributions. AnimationMixer.update runs active actions first and applies bindings afterward. Therefore the event is not a post-update pose barrier. Reading an animated object's final property values inside the callback is not guaranteed by this ordering to observe the newly applied pose. This is a source-order inference, not a browser trace. [S1,S2]

2. **Completion and influence are different.** Natural final-loop completion either pauses the action when clampWhenFinished is true or disables it when false. A paused enabled action can continue contributing its held pose when the mixer updates; zero playback advancement does not imply zero weight. Conversely, a disabled action has no influence. A final-loop event alone does not establish which pose should be visible after blending. The public documentation says clamping does not apply to interruption. [S1,S3]

3. **isRunning is not a final-pose or disposal test.** The pinned predicate checks enabled, paused, timeScale, scheduled start and mixer activity. It does not test weight. isScheduled checks mixer activity. A zero-weight action may be running while contributing nothing; a clamped paused action may be scheduled while not running. Use these predicates for their particular state, not as a single application “done” flag. [S1]

4. **stop is not freeze.** The pinned stop deactivates the action and resets it. reset restores time to zero, clears pause/scheduling and fading/warping state, but it is not a promise to reset every configurable field such as weight or timeScale. play activates an action without itself resetting its timeline. On replay, explicitly restore the intended playback and blend policy rather than assuming play or reset establishes all of it. [S1]

5. **Interruption needs an application outcome.** The inspected stop/reset paths do not dispatch finished. Waiting solely for that event can leave an application transition unresolved when navigation or cancellation interrupts it. Mixer documentation also requires deactivating actions before uncaching their resources; completion notification is not equivalent to cleanup. [S1,S4]

## Original synthesis: observe, then transition at an owned boundary

The following is an untested application protocol, not an upstream API addition:

- Give each playback attempt a generation ID, expected action identity and explicit outcome: playing, naturally completed, interrupted or retired. A cached action object can be reused; object identity alone does not identify a replay attempt.
- Subscribe on the owning mixer, filter by the expected action and current generation, and have the finished handler record a pending outcome. Do not stop/uncache actions or recursively update the mixer inside that callback as the default design.
- After the owning mixer.update call returns, process pending outcomes at the application's known frame boundary. If there are multiple animation writers, “after this mixer” is only a local boundary; coordinate all relevant writers before reading a final aggregate pose.
- Choose one completion policy explicitly: retain a clamped contribution, transition/blend to another action, or retire the animation and return ownership to the base state. Retention can affect later clips that animate the same properties; holding a pose is not equivalent to baking it into a new independent baseline.
- If the design must show the terminal pose before advancing the story, separately require the chosen pose policy and a rendering opportunity. Even a render call does not certify that the browser presented a frame to the user. A callback is not a visual acceptance receipt.
- On interruption, record cancellation explicitly, remove the listener for that attempt and retire only resources owned by that component. Do not fabricate a natural-completion event to make a waiting UI proceed.
- For replay, configure loop mode, direction/time scale, weight, clamping and initial time deliberately. Reverse playback and ping-pong sequences need their own endpoint expectations; do not blindly equate completion with the clip's positive-time last frame.
- Keep one owner advancing the mixer in seconds. A React Three Fiber/Drei adapter may already own advancement; adding a second update risks double advancement. This run did not inspect any wrapper version, so integration requires checking that adapter's actual lifecycle.

A useful diagnostic receipt would distinguish event observed, mixer update returned, pose policy selected, frame requested and owner retired. These are proposed application states, not engine guarantees.

## Proposed acceptance exercise — not executed

Use two short, original clips that animate the same object property, plus a DOM button that advances a case-study section.

1. Compare the property's value in finished with the value after mixer.update returns. Record event/action state and actual property values; do not infer ordering from a timeout.
2. Run LoopOnce with clampWhenFinished false and true. Observe influence after completion and after another action starts. Verify the chosen UI policy rather than expecting both cases to look identical.
3. Set weight to zero while playback advances; contrast isRunning with visible influence. Pause a clamped action and contrast isScheduled with advancement.
4. Interrupt before the end using the application's cancellation path. Require one interrupted outcome, no hang waiting for finished, and no transition from an obsolete generation.
5. Replay a reused action after changing weight/time scale. Verify explicit configuration, and test reverse playback separately.
6. Request a route change during the completion callback. Require deferred cleanup after the update boundary and no effects on the new route's owner.
7. Test retained-pose blending, listener cleanup, keyboard navigation, reduced-motion alternatives and the actual frame-loop adapter independently.

No fixture, browser or renderer was executed. The exercise specifies future evidence, not passed tests.

## Practical fit, alternatives, licence and operation

Three.js is a JavaScript 3D library. Its inspected package exposes ES-module/CommonJS entry points and has development tooling but no runtime dependencies field. No additional package is required for an existing Three.js mixer owner to implement the proposed state protocol. Pin the application's actual Three.js and wrapper versions; source internals are not a cross-version compatibility guarantee. [S5]

The observed development commit demonstrates recent repository activity, not a release publication date or long-term maintenance promise. Release date and installed application version remain null. The official AnimationAction and AnimationMixer pages are API references; no linked animation demo was run. [S3,S4,S7]

The actual source licence is MIT, with notice-retention requirements and a warranty disclaimer. It does not grant rights to third-party animation clips, models, fonts or demo artwork. This package includes original synthesis, not copied implementation or assets. [S6]

No API key, MCP connection, backend or paid service is needed for this client-side pattern. Ordinary hosting, CPU animation work, render cost and engineering/QA time remain; no cost or performance savings were measured. A semantic DOM transition with a static scene is a simpler alternative when animation is decorative. Another option is an application-owned timeline that explicitly drives pose and UI together, but it still needs cancellation and rendering boundaries. Neither alternative was benchmarked.

An original future application is a cinematic portfolio sequencer that allows a visitor to skip a clip without mistaking interruption for successful natural completion, while preserving a stable DOM navigation path. It should expose a static/reduced-motion route rather than requiring animation playback to access essential content.

## Limitations

Documentary review only. No runtime, integration, animation, visual, accessibility, performance or wrapper tests performed. Source-order observations are version-pinned; renderer presentation, callback reentrancy under arbitrary application mutations and all blend modes are not certified. Approval means this package's documented review, not deployment permission or Gemini approval.

## Sources

- [S1] [Pinned AnimationAction](https://github.com/mrdoob/three.js/blob/e02b502efd394f5e982103159b9815b2ed147aad/src/animation/AnimationAction.js)
- [S2] [Pinned AnimationMixer](https://github.com/mrdoob/three.js/blob/e02b502efd394f5e982103159b9815b2ed147aad/src/animation/AnimationMixer.js)
- [S3] [AnimationAction API](https://threejs.org/docs/pages/AnimationAction.html)
- [S4] [AnimationMixer API](https://threejs.org/docs/pages/AnimationMixer.html)
- [S5] [Pinned package manifest](https://github.com/mrdoob/three.js/blob/e02b502efd394f5e982103159b9815b2ed147aad/package.json)
- [S6] [Pinned MIT licence](https://github.com/mrdoob/three.js/blob/e02b502efd394f5e982103159b9815b2ed147aad/LICENSE)
- [S7] [Observed development commit](https://api.github.com/repos/mrdoob/three.js/commits/e02b502efd394f5e982103159b9815b2ed147aad)
