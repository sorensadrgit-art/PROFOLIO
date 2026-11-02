# A safe refresh is not proof that layout has settled

Run: `20261102T001200Z-motion-interaction` · Scheduled: 2026-11-02 00:12 UTC · Evaluated: 2026-11-02.

## Question and novelty

When an accordion, delayed font or fetched case-study section changes page geometry, what should own ScrollTrigger remeasurement?

The live catalog and seven previous motion research documents were inspected first. Earlier architecture guidance recommends refresh after geometry changes; this addendum supplies the missing **measurement lifecycle**: distinguish requesting a refresh, completing that refresh, and observing a later layout revision. The branch tree had no same-run directory or unindexed topic package. This is an implementation-pattern study, not a repository discovery shortlist.

## Source findings

- `ScrollTrigger.refresh()` recalculates trigger positions from current natural-flow geometry, temporarily reverting pins and resetting scrubbed animations before restoring their state. It reevaluates function-based start/end values, performs an update and fires refresh callbacks. `refresh(true)` delays initiation by at least a requestAnimationFrame tick and up to approximately 200 ms, intended to accommodate DOM rendering. It is not documented as awaiting arbitrary network requests or application transitions. [S1](https://gsap.com/docs/v3/Plugins/ScrollTrigger/static.refresh()/)
- `ScrollTrigger.update()` reconciles scroll position, progress/direction, linked animation and callbacks. It does **not** recalculate start/end from changed DOM geometry and is normally automatic on scroll. Repeated update calls cannot repair an obsolete measurement. [S2](https://gsap.com/docs/v3/Plugins/ScrollTrigger/static.update()/)
- `invalidateOnRefresh: true` invalidates the associated animation's internally recorded starting values. Function-based start/end values are evaluated during refresh. These address related but different cached data: trigger geometry and animation values. Neither means an arbitrary JavaScript number calculated before setup will automatically be recomputed. [S3](https://gsap.com/docs/v3/Plugins/ScrollTrigger/)
- CSS Font Loading's `ready` promise resolves after font-loading-related layout completes; further fonts may load afterward. A given promise fulfills once and never rejects, so fulfillment is not a guarantee that every desired face succeeded or that later content cannot alter geometry. The cited W3C document is a Working Draft dated April 6, 2023. [S4](https://www.w3.org/TR/css-font-loading-3/#font-face-set-ready)

## Proposed refresh coordinator — original synthesis, untested

Treat measurement as a revisioned page service, not a side effect scattered across components. Suggested state: current route generation, monotonically increasing layout revision, pending request, and last observed refresh receipt. A receipt records the revision that initiated the measurement, not a permanent “page ready” flag.

1. **Commit content first.** A click may request an accordion expansion, but the event callback is not necessarily the point at which the framework committed the new DOM or an animated height reached its chosen resting state. Choose a lifecycle signal owned by that component. Do not assume a fixed sleep knows when it is complete.
2. **Mark actual geometry changes.** Increment the revision when content insertion, a known width change, an expansion endpoint or a relevant font cycle changes measurable layout. Pure opacity changes do not inherently require this workflow.
3. **Coalesce requests.** Queue one page-level refresh for a burst of eligible changes instead of one global refresh per component. Preserve a dirty revision if another change occurs while the request is pending. This is a scheduling proposal, not a promise that GSAP exposes a transactional layout API.
4. **Measure current ownership only.** Check the route generation and mounted state before executing queued work. A late promise from the previous route must not use disconnected nodes or declare the replacement route measured.
5. **Record completion, then reconcile.** Observe the global `refresh` event for completion of the page-level refresh (an individual `onRefresh` only concerns its instance), associate it conservatively with the requested revision, and schedule another pass if genuinely newer geometry arrived. Do not label the page permanently settled from one callback. An automatic GSAP refresh may also occur, so the coordinator must not assume it exclusively controls all refreshes.
6. **Avoid feedback.** Do not mutate layout unconditionally from a refresh callback or request another refresh just because refresh itself fired. Compare the meaningful input revision. If an observer is later added, distinguish actual content geometry changes from temporary pin/revert bookkeeping; its behavior needs dedicated tests.
7. **Clean up.** Remove owned listeners and queued callbacks on route teardown. The page coordinator can request global refresh because upstream pin geometry affects later sections, but individual components must not globally kill unrelated triggers.

For an animated expansion, choose explicitly between refreshing at the final resting geometry and a separately engineered continuously changing layout experience. A single delayed refresh during a long height transition can measure an intermediate state. Do not repeatedly refresh every animation frame as a default “fix”; inspect the actual problem and measure the cost first.

For fonts, wait on the relevant current loading cycle when needed, then mark the revision. Keep readable fallback text and reserve media dimensions where possible. Neither font readiness nor a safe refresh is an all-assets-loaded barrier. If resources fail, settle on a usable fallback layout rather than holding essential content invisible indefinitely.

Use function-based geometry inputs where appropriate, and distinguish values calculated during setup from values calculated during refresh. Enabling animation invalidation cannot revive a stale closed-over constant. This last point is ordinary JavaScript evaluation reasoning, not a new GSAP feature claim.

## Alternatives, product fit and costs

The simplest alternative is stable normal-flow content with no pinning. Reserve image space, reduce unnecessary expanding regions before a pinned story, or remove choreography whose geometry cost exceeds its value. A small visibility reveal may not need a continuous scrub system. Reuse the existing animation owner rather than adding another framework solely for refresh scheduling.

GSAP is a JavaScript animation toolkit; ScrollTrigger adds scroll-linked choreography. Repository metadata observed version **3.15.0**, ES-module/CommonJS entry points and TypeScript declarations, with no dependency field. The observed repository head is `13e2b790546426a1a2e0e9b409f3f8dc6d6611f2`, committed **2026-04-13T13:08:30Z**. These are repository observations, not npm latest, installed-version or maintenance guarantees. [S5](https://github.com/greensock/GSAP/blob/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2/package.json), [S6](https://github.com/greensock/GSAP/commit/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2)

GSAP uses the **Standard No Charge licence**, not MIT. Website/application use is permitted subject to its terms; competing no-code visual-animation-builder uses are restricted, and proprietary notices must remain. Public repository access is not unrestricted reuse. Review the actual terms for the intended product. [S7](https://gsap.com/community/standard-license/)

An eventual implementation requires an existing compatible GSAP/ScrollTrigger install, plugin registration, browser DOM access and framework lifecycle integration. No installation occurred. SSR code must defer browser-dependent work to its client lifecycle. Exact application lockfile and browser compatibility remain unverified. Official docs contain demos; none was executed or copied. No new API, credentials, MCP server, backend, paid service or hosting change is required by this proposal. Operational costs are development/testing and existing client delivery; refresh duration, frame cost and bundle size were not measured.

Original future application: an editorial case study can record which content revision its chapter markers were measured against, making “pins shifted after opening details” diagnosable without turning every scroll event into a remeasurement.

## Acceptance matrix — NOT RUN

| Scenario | Observation required before adoption |
|---|---|
| Expand section above two pinned chapters | Both downstream ranges match the new flow; native reading remains usable. |
| Expansion duration exceeds safe-refresh delay | Measurement uses the chosen completed state, not accidental intermediate geometry. |
| Several components finish together | Requests coalesce without losing the latest dirty revision. |
| New geometry arrives while refresh is pending | A later reconciliation measures it; no false settled receipt. |
| Font fails, then different content introduces another face | Readable fallback; later relevant layout cycle handled separately. |
| Route unmounts before promise resolves | Old completion cannot refresh disconnected component state or certify the new route. |
| Refresh callback/observer sees pin bookkeeping | No self-sustaining refresh loop. |
| Resize with cached animation values | Function-based inputs and invalidation are verified separately. |
| Keyboard, zoom, reduced motion and narrow viewport | Content order, focus and static fallback remain usable. |

No browser, runtime, accessibility-conformance, performance or integration tests were run. The scheduling contract is original synthesis requiring implementation validation, not vendor-certified code. See sources.json for provenance and qa.md for independent documentary review.

