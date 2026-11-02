# Scroll progress is not the rendered animation playhead

Run: `20261102T061200Z-motion-interaction` · Scheduled: 2026-11-02 06:12 UTC · Evaluated: 2026-11-02.

## Question and gap

Which signal should drive a caption or canvas layer beside a numerically scrubbed ScrollTrigger animation?

The live catalog, nine prior motion documents and full branch tree were inspected first. Earlier work explains smoothing and property ownership, but not the **two-progress contract** between a scroll target and a lagging animation playhead. No same-run directory or unindexed motion package was observed. This is a narrow interaction-engineering study, not a repository shortlist.

## Source findings

- A numeric `scrub` smooths the associated animation toward the scroll position; the animation can continue after scrolling stops. ScrollTrigger's `onUpdate` follows trigger progress changes. The official recommendation for following animation changes is an `onUpdate` on the animation itself. [S1](https://gsap.com/docs/v3/Plugins/ScrollTrigger/)
- `onScrubComplete` concerns completion of numerical scrub; it is not the same event as `onSnapComplete`. An interrupted snap does not call its completion callback. These callbacks describe their respective operations, not a universal page-complete state. [S1](https://gsap.com/docs/v3/Plugins/ScrollTrigger/)
- `eventCallback()` gets, replaces or removes an animation callback; passing null removes it. Each animation has only one callback per event type, so installing another `onUpdate` overwrites the previous one. It is not an additive subscription API. [S2](https://gsap.com/docs/v3/GSAP/Timeline/eventCallback()/)

## Proposed two-progress contract — original synthesis, untested

Before wiring a second visual, decide what it represents. A reading-position indicator may intentionally reflect where the reader scrolled. A label describing the image currently being animated should normally reflect that animation's playhead. Name the two signals explicitly, for example `scrollTargetProgress` and `visualPlayheadProgress`, instead of exporting an ambiguous `progress` variable.

| Consumer | Proposed owner | Failure to look for |
|---|---|---|
| Reading-position marker | Current scroll target | A deliberately delayed marker looks like input lag. |
| Caption for a moving scene | Associated animation update | Caption jumps ahead while the scene catches up. |
| Canvas layer intended to match the same scene | Same visual playhead, with a defined mapping | Two independently smoothed signals drift apart. |
| Scrub-settled diagnostic | Numeric scrub completion receipt | Receipt mistaken for permanent completion or user acknowledgement. |
| Snap-settled diagnostic | Snap completion when it actually occurs | Interrupted snap leaves a caller waiting forever. |

A proposed implementation sequence:

1. Define the timeline and its semantic scene intervals together. Keep an explicit mapping from playhead position to scene/caption identity. Do not assume equal-sized intervals if the designed sequence uses unequal durations.
2. Route visual dependents through the animation owner's update callback. Give that owner one dispatcher if several consumers need the same signal; do not let each component replace the callback independently.
3. Keep the dispatcher lightweight. Forward a numeric value to the appropriate render owner; update discrete caption state only when its scene identity changes. This is a design proposal, not a measured performance claim.
4. Make callback registration and disposal owner-specific. If borrowing an existing animation, coordinate with its owner instead of silently replacing or nulling a callback another feature relies on.
5. On route teardown or replacement, invalidate the instance generation before delayed work can publish state. An old completion receipt must not label the new scene settled.
6. Model forward, reverse and interrupted movement. A chapter boundary can be crossed repeatedly. Do not attach an irreversible application command to a decorative progress threshold without a separate command/idempotency contract.

A scrub-completion receipt should identify the scene instance and observed target, not permanently unlock unrelated content. New input can establish another target. Similarly, a caption is a description of a chosen visual state, not proof that a user read the content. Keep important text accessible outside optional animation; do not announce every numerical frame as a live-region message. The semantic behavior needs explicit keyboard and assistive-technology review.

For a canvas integration, the render loop may consume the latest playhead value rather than perform its own additional easing. If additional easing is an artistic requirement, document that intentional lag instead of calling it synchronization. Avoid changing global ticker policy merely to conceal a mismatch between two different progress meanings.

## Tool fit, licence and operating requirements

GSAP is a JavaScript animation toolkit; ScrollTrigger supplies scroll-linked choreography. The pinned repository package declares **3.15.0**, ESM/CommonJS entry points and TypeScript declarations, with no dependency field. The inspected commit is `13e2b790546426a1a2e0e9b409f3f8dc6d6611f2`, dated **2026-04-13T13:08:30Z**. This is repository metadata, not a verification of npm latest, a downloaded artifact, an application's lockfile or continuing maintenance. [S3](https://github.com/greensock/GSAP/blob/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2/package.json), [S4](https://github.com/greensock/GSAP/commit/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2)

The actual **Standard No Charge licence is not MIT**. It permits website/application use subject to terms and restricts competing no-code visual-animation-builder uses; proprietary notices/branding cannot be removed or altered. Effective April 30, 2025; last modified May 30, 2025 on the inspected page. Public source access does not mean unrestricted reuse. Review the terms for the intended product. [S5](https://gsap.com/community/standard-license/)

An implementation needs a compatible GSAP/ScrollTrigger installation, plugin registration and browser-side lifecycle ownership. SSR applications must keep DOM-dependent setup in the client lifecycle. Official documentation includes demos; none was executed or copied. No installation, API key, MCP server, backend or paid service is required by this proposed change. Existing hosting/client delivery and engineering/testing are the operational costs; bundle size, memory and frame time were not measured.

Alternatives: remove numeric smoothing when immediate coupling is the desired interaction; use a static scene and ordinary document text; or use a single existing framework animation owner. Do not introduce a second animation library solely to mirror progress. Original future application: a cinematic case-study chapter can show a crisp reading-position rail while its image/caption pair intentionally follows one smoothed visual timeline, with diagnostics exposing both values when debugging perceived drift.

## Acceptance matrix — all NOT RUN

| Scenario | Required observation before adoption |
|---|---|
| Fast scroll then stop | Caption/canvas continues following the visual playhead during catch-up. |
| Reverse before catch-up completes | All visual dependents follow the current playhead, not stale target order. |
| Two consumers attach | Neither overwrites the other's callback; teardown preserves the surviving owner. |
| Snap interrupted by new scroll | No false completion and no indefinitely pending application operation. |
| Route replacement during smoothing | Old callbacks cannot update the replacement scene. |
| Unequal timeline sections | Scene/caption mapping matches designed intervals in both directions. |
| Reduced motion, keyboard and no decorative animation | Essential text and controls remain usable. |

No browser, runtime, synchronization, accessibility, performance or integration tests were performed. This is documentary review and original synthesis, not production certification.
