# Scheduling an animation is not deferring its first style write

Run: `20261102T091200Z-motion-interaction` · Scheduled: 2026-11-02 09:12 UTC · Evaluated: 2026-11-02 · Author: Dot

## Question and decision

How should a cinematic sequence prevent a later GSAP `fromTo()` from changing the element before its scheduled scene?

**Recommendation (original, untested): review initialization policy separately from timeline position.** Use explicit `immediateRender: false` on later `fromTo()` steps whose starting values must not be applied during construction. Keep a deliberate initial state and one owner per animated property. Do not indiscriminately disable immediate rendering on every entrance.

Novelty: the live catalog and ten motion packages were read before selection; none contains `immediateRender`. This adds a construction-time contract, not another scroll, refresh, interruption, or style-cleanup package. Live topic directory showed ten indexed packages and no same-slot or unindexed run.

## Source findings

1. **Delay is not a first-write barrier.** GSAP documents that `fromTo()` normally applies its starting state immediately even with a delay; `immediateRender: false` defers that render until the tween begins. For this API the option belongs in the destination/options object, not the starting-values object. [S1](https://gsap.com/docs/v3/GSAP/gsap.fromTo()/)
2. **Immediate rendering is intentional.** The maintainer's lesson explains that `from()` and `fromTo()` use this behavior for useful entrance/stagger scenarios, while multiple such tweens on the same object's same property require care. An early starting-state write is not itself proof of a GSAP defect. [S2](https://gsap.com/resources/immediaterender/)
3. **Timeline insertion does not remove the choice.** The timeline `fromTo()` reference repeats the default and override, and exposes a position parameter for scheduling. Its interactive position demo illustrates timing, not an executed verification of this package. [S3](https://gsap.com/docs/v3/GSAP/Timeline/fromTo()/)
4. **A scheduled state change has its own primitive.** `Timeline.set()` inserts a zero-duration step at a chosen position and documents `immediateRender: false` by default. Use it when the design calls for a discrete state change at that point rather than an interpolated entrance. [S4](https://gsap.com/docs/v3/GSAP/Timeline/set()/)

## Proposed initialization contract — original synthesis

For each scene, record `(target, property, baseline owner, intended first write, scheduled start)`. A review should reject a later scene that silently owns the baseline merely because its tween was constructed last.

| Intended behavior | Candidate construction policy | Review risk |
|---|---|---|
| Entrance begins from an intentionally hidden/offset initial state | Deliberate initial render, or explicit baseline followed by `to()` | Content should not remain unavailable if enhancement fails |
| Later scene uses explicit endpoints without altering the opening scene | Later `fromTo()` with `immediateRender: false` | Starting endpoint must agree with prior scene or a jump may be intentional but visible |
| Discrete change exactly at a scene boundary | `timeline.set(..., position)` | Seeking/reversing across boundary needs verification |
| Scene must continue from the value present when it starts | Consider `to()` instead of another explicit `fromTo()` | Cached values, rebuilding, and competing writers still need review |

Minimal illustrative construction, **not executed**:

```js
// gsap is supplied by the application's approved client-side integration.
const sequence = gsap.timeline({ paused: true });
sequence.to(card, { x: 120, duration: 1 }, 0);
sequence.fromTo(
  card,
  { x: 120 },
  { x: 240, duration: 1, immediateRender: false },
  2
);
// The application decides when playback is permitted.
```

This example is not a hydration strategy, first-paint guarantee, reduced-motion implementation, or cleanup recipe. It assumes an approved baseline and an existing element. Do not add automatic playback or hide readable content merely to conceal an initialization defect. Preserve the existing motion ownership and teardown policy.

## Tool evaluation and operational fit

GSAP is a JavaScript animation/sequencing toolkit. The actual repository's pinned `package.json` reports **3.15.0**, ESM and CommonJS entry points and bundled TypeScript declarations, with no dependency field. This does not prove an npm tarball matches the repository or that all optional plugins have no external requirements. The inspected master head is `13e2b790546426a1a2e0e9b409f3f8dc6d6611f2`, committed 2026-04-13T13:08:30Z; that observation is not a release date or a maintenance SLA. Release date remains unknown. [S6](https://github.com/greensock/GSAP/blob/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2/package.json)

For an application already using GSAP, this is an API-option review, not a new service. A future prototype can use its approved pinned package and ordinary frontend build/hosting. No backend, authentication, API key or MCP connection is required by the illustrated core timeline calls. No installation, plugin registration, build, deploy, npm integrity check, framework compatibility test or performance measurement occurred. Client DOM availability, component lifecycle and SSR boundaries remain integration responsibilities. Costs are existing build/hosting plus engineering/testing time; runtime CPU cost is unmeasured.

The actual terms are the **Standard No Charge GSAP License**, not MIT or unrestricted reuse. Permitted website/app/interface uses are subject to restrictions, including competing no-code visual animation builders and removal of proprietary notices. Commercial use is described as no-charge under those terms. Check the full current terms for the intended product; a public repository is not blanket reuse permission. Observed effective date: 2025-04-30; modified: 2025-05-30. [S5](https://gsap.com/community/standard-license/)

## Alternatives and future use — untested recommendations

- Prefer explicit baseline plus `to()` for a simple continuous sequence rather than repeating explicit starting values. This simplifies review but does not establish first-paint or reverse-playback correctness.
- For a simple visual state change, consider CSS transitions instead of adding a timeline. No cross-engine initialization equivalence is claimed.
- Build a scene-authoring review table that exposes first-write ownership beside timeline labels. A future static check could flag multiple `fromTo()` steps for the same target/property lacking an explicit initialization decision; aliasing/dynamic targets would need human review.
- A typography sequence could use the same ownership table per word wrapper, without distributing fonts or animation assets. Font readiness and accessible text remain separate concerns.

## Acceptance matrix — all unperformed

1. Capture computed and inline styles immediately before and after sequence construction, before playback.
2. Compare default and explicit-false later steps; confirm the intended opening scene rather than merely absence of console errors.
3. Seek before, at and after the second scene; reverse, restart and rebuild. Check discontinuities against design intent.
4. Test delayed construction, an initially paused sequence and competing style writes independently; do not generalize one result to all cases.
5. Exercise failed enhancement, reduced-motion rendering and component teardown/remount without leaving essential content hidden.

Documentary approval is not runtime, performance, accessibility or deployment certification. Exact browser support, installed application versions, npm artifact provenance and compatibility are unverified. The source manifest records known dates and null unknowns; no protected assets or repository code were copied.
