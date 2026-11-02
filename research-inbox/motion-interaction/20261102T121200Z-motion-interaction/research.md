# A refreshed loop is not a retimed loop

Run: `20261102T121200Z-motion-interaction` · Scheduled: 2026-11-02T12:12:00Z · Evaluation began: 2026-11-02T12:08:41Z · Author: Dot

## Question and gap

When a generative portfolio accent repeats, which inputs does GSAP resample, and which require a separate timing decision?

The live catalog, all eleven prior motion research documents and recursive branch tree were inspected first at head `3a1478b6d1a3541bdfdcc23cb20876edae1eb69e`. No same-run directory or unindexed motion package was found. Earlier studies cover initialization, geometry refresh, cleanup and playback permission; none addresses `repeatRefresh`. This is a narrow implementation-pattern study, not a repository shortlist. Work began before its nominal slot; that slot is identity metadata, not a claimed retrieval time.

## Source findings

- Function-based destination values are evaluated per target when a tween first renders. They are not a documented per-frame reactive binding. [S1](https://gsap.com/docs/v3/GSAP/gsap.to()/)
- `repeatRefresh: true` invalidates and resamples recorded start/end values on full repeating iterations, excluding yoyo iterations. It supports changing relative, random or function-based values. **Duration, delay and stagger do not refresh.** [S1](https://gsap.com/docs/v3/GSAP/gsap.to()/)
- `invalidate()` discards initialization data and reparses vars on the next render without changing duration, start time or delay. A timeline invalidation also invalidates its children. The documented relative-value example distinguishes replaying cached endpoints from resampling against the current value. [S2](https://gsap.com/docs/v3/GSAP/Tween/invalidate()/)

## Proposed sampling contract — original synthesis, untested

Define a loop specification before picking an animation option:

| Design intention | Proposed implementation decision | Explicit non-guarantee |
|---|---|---|
| Replay exactly the same visual route | Keep a stable, explicit set of endpoints | Restart is not a request for fresh randomness |
| Pick another decorative destination between cycles | Consider repeatRefresh with a bounded destination generator | Timing does not become dynamic |
| Return along the same outbound route | Specify the intended yoyo pair | Do not assume a fresh target at the turnaround |
| Change pacing or number/order of beats | Let a sequence owner revise the timing specification deliberately | Endpoint invalidation is not a timeline editor |
| Follow continuously changing input | Design a current-input adapter separately | A destination function alone is not a live subscription |

Keep **value policy**, **timing policy** and **playback permission** in separate fields. A debug receipt could record scene generation, intended cycle identity, chosen destination and timing-spec revision. This is an original proposal, not a claim that GSAP exposes an atomic cycle transaction or guarantees callback order under every seek/reverse operation.

For decorative randomness, generate only within an approved visual envelope. Store the selected input sequence in a test fixture when reproducing a defect; do not promise deterministic replay merely because the same tween instance was restarted. Randomness should never select business state, navigation destinations, permission changes or required text.

A typography flourish should leave the semantic heading and reading order stable while decorative wrappers vary. Avoid assigning a new textual meaning to every repeat. Keep essential content available in the static composition, and retain the already-defined user pause and reduced-motion policy. This proposal does not establish accessibility conformance.

If product design asks for variable pacing, treat that as a new sequence specification, not a flag added to a value refresh. Decide how to reconcile the currently visible position, whether to finish the current beat, and how a replacement owner will clean up obsolete work. Do not globally invalidate or restart unrelated scenes. A new target set or changed choreography requires its own lifecycle review; this report does not claim repeatRefresh discovers new DOM nodes.

## Tool fit, provenance and licence

GSAP is a JavaScript property-animation and sequencing toolkit. Pinned repository metadata reports **3.15.0**, ESM/CommonJS entry points and TypeScript declarations, and contains no dependency field. This is not an npm artifact, installed lockfile or universal optional-plugin dependency assertion. [S3](https://github.com/greensock/GSAP/blob/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2/package.json)

The observed master head is `13e2b790546426a1a2e0e9b409f3f8dc6d6611f2`, committed 2026-04-13T13:08:30Z. That is a commit date, not an independently verified release date or maintenance commitment; release date remains null. [S4](https://github.com/greensock/GSAP/commit/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2)

The actual Standard No Charge GSAP License is **not MIT**. Website/application uses are subject to its terms, including restrictions on competing no-code visual-animation builders and removal/alteration of proprietary notices or branding. Commercial use is described as no-charge under those terms. Public source access is not unrestricted reuse. Observed licence effective date: 2025-04-30; modified date: 2025-05-30. Review the full terms for the intended product. [S5](https://gsap.com/community/standard-license/)

## Integration, alternatives and costs

For an existing GSAP application this is a loop-policy review, not a new service. A later prototype needs an approved compatible package and owned component lifecycle; DOM-targeting setup belongs in the browser/client lifecycle rather than server rendering. No plugin is required by the core APIs discussed. Exact browser/build/framework compatibility, npm integrity and application lockfile were not tested.

Official API pages offer demonstrations, including replay versus invalidation; none was executed or copied. No installation, hosting change, backend, API credential, MCP connection or paid service is needed by this proposed local pattern. Costs are engineering/testing and existing client delivery; CPU, memory, bundle size and frame time were not measured.

Alternatives: fixed CSS keyframes for a predetermined decorative loop; a finite authored sequence; or static artwork when variation adds no value. Reuse an existing animation owner rather than adding GSAP only for random movement. Original future application: a case-study accent with reproducible sampled destinations and an independently reviewable pacing sheet, allowing a designer to debug variation without changing the user's playback choice.

## Acceptance matrix — all NOT RUN

1. Compare fixed endpoints, function-based endpoints and repeatRefresh across several full cycles; log actual samples.
2. Add yoyo and verify outbound/return pairing rather than assuming a new sample on every direction change.
3. Vary requested duration, delay and stagger; confirm that a separate timing update is required and review the chosen transition.
4. Change the input source mid-cycle and inspect when values are actually sampled.
5. Seek, reverse, restart and invalidate independently; compare visual discontinuities against design intent.
6. Replace the component or target set during a cycle; stale receipts must not control the replacement.
7. Exercise pause, reduced motion, keyboard navigation and failed enhancement with readable static content.

Documentary approval is not runtime, performance, accessibility or integration certification. No executable example, browser test or deployment was performed.
