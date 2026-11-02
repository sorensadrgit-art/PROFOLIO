# Hover motion should follow current intent, not a queue of old events

Run: `20261101T181200Z-motion-interaction` · Scheduled slot: 2026-11-01 18:12 UTC · Actual research began: 2026-11-01 18:08 UTC.

## Question and novelty

How should a project-card accent behave when a pointer enters, leaves and returns before an animation finishes, while keyboard focus remains on the card?

The live catalog and all five existing motion packages were read first. They cover motion-engine ownership, navigation, dialog exits, scroll smoothing and drag cancellation. This is a narrower missing contract: **combine independent input states before choosing an animation target**, rather than letting the last raw event win. No prior package or unfinished same-slot directory existed at inspection.

## Verified findings

- **GSAP continuous retargeting:** `quickTo` reuses a tween for repeated updates to one numeric property. New values redirect its destination. It skips conveniences such as unit conversion, relative values and general plugin parsing. It uses its internally tracked value rather than re-reading the target on every call, so another animation owner can invalidate the assumption. This is appropriate for a bounded pointer-following accent, not a general replacement for every tween. [S1](https://gsap.com/docs/v3/GSAP/gsap.quickTo()/)
- **GSAP reversible accents:** `reverse()` without a position starts from the current playhead; `reverse(0)` first jumps to the end. Thus the latter is not interchangeable with reversing an interrupted entrance. [S2](https://gsap.com/docs/v3/GSAP/Tween/reverse()/)
- **Motion hover:** `whileHover` animates to its target during a hover gesture and back to the previous state afterward. Its hover handling filters emulated touch-hover events. The standalone `hover()` recognizer returns cleanup. These are gesture mechanics, not a complete keyboard or popup interaction specification. [S3](https://motion.dev/docs/react-hover-animation)
- **Additional content is different from decoration:** WCAG 2.2 SC 1.4.13 is Level AA. For covered hover/focus content, it requires dismissibility with specified exceptions, pointer movement onto the added content without disappearance, and persistence until the trigger is removed, the content is dismissed, or the information is invalid. The Understanding page is explanatory; the Recommendation supplies the criterion. This is not a rule requiring every decorative scale/underline effect to become a popup. [S4](https://www.w3.org/WAI/WCAG22/Understanding/content-on-hover-or-focus.html), [S5](https://www.w3.org/TR/WCAG22/#content-on-hover-or-focus)

## Proposed interaction contract — original synthesis, not executed

For a purely decorative project-card accent, track `pointerInside` and `focusWithin` independently. Derive `active = pointerInside || focusWithin`. Pointer leave must not reverse the accent while focus remains within the card; focus leave must not deactivate a still-hovered card. Moving focus between children should not be treated as leaving the entire card. Keep the native link/button and its visible focus indication independent of animation completion.

Use one owner for each visual property:

1. A simple underline/color accent can remain CSS-only. Do not add two libraries for it.
2. An existing Motion component can map the derived intent to a target. Do not assume combining arbitrary `whileHover`/`whileFocus` properties automatically expresses the desired compound state; verify the pinned implementation.
3. An existing GSAP accent with fixed endpoints can keep one paused tween/timeline and change direction from its current position. Do not create a fresh entrance animation on every event or force the playhead to an endpoint.
4. A continuously changing pointer coordinate can use one `quickTo` controller per numeric property. Clamp the target; reset toward the neutral location on exit. Keep any scroll-owned transform on a different element so it does not fight the pointer controller.

Treat delayed starts and callbacks as potentially stale. Increment an interaction generation when intent changes; a delayed callback may act only if its generation still matches and the component remains mounted. Teardown cancels owned timers/listeners/animations. No callback should navigate, submit, or decide whether a user action is authorized simply because a visual transition ended.

For reduced motion, use a stable accent rather than spatial tracking; preserve clear current-state and focus feedback. This is a proposed design policy, not evidence of tested conformance.

### If the accent reveals extra information

Prefer keeping essential project title, category and destination visible without hover. If an optional preview panel is justified, it needs a separate interaction state machine, not merely a longer exit animation. Account for pointer presence over both trigger and panel, keyboard focus, and explicit dismissal. After Escape, remember dismissal for the current activation so an unchanged hover state does not instantly reopen it; choose and test a deliberate re-entry/re-focus reset. This dismissal latch is our proposed implementation strategy, not a requirement to use a particular algorithm.

A timeout is not a substitute for hoverability or persistence. Do not build a menu or interactive dialog from a decorative tooltip effect. Select the appropriate semantic pattern before implementing its animation.

## Debugging checklist and unexecuted acceptance tests

| Scenario | Required observation in a future test |
|---|---|
| Enter → leave → enter faster than the duration | Visuals converge to the latest derived intent, without queued replay or endpoint jumps. |
| Focus enters, then pointer leaves | Focus indication remains; the compound active state is not accidentally cleared. |
| Focus moves between card children | No false whole-card exit. |
| Touch taps a project link | Navigation is usable without hover or a stuck visual prerequisite. |
| Scroll effect and pointer accent coexist | Separate property owners; no transform contention. |
| Component unmounts during a delayed reveal | No late update, orphan listener, or navigation side effect. |
| Optional preview: pointer moves onto it; Escape pressed | Panel remains reachable, dismisses, and does not immediately reopen from unchanged input. |
| Reduced-motion preference and keyboard-only navigation | Stable content, visible focus, and no essential information lost. |

Test real mouse, touch, hybrid input, keyboard, zoom and target browsers. Capture interaction traces before asserting smoothness or frame-rate improvements. None of these tests were run here.

## Version, licence and operational boundaries

Repository snapshots observed GSAP **3.15.0**, commit `13e2b790546426a1a2e0e9b409f3f8dc6d6611f2` dated April 13, 2026, and Motion **14.9.0**, commit `7415b6cd14dfdb35d91dfc5b9758994444cc228b` dated October 30, 2026. These are repository observations, not npm latest assertions or installed application versions. The Motion package declares `framer-motion` 14.9.0 and `tslib` ^2.4.0; its React/React DOM peers cover 18 or 19 and are optional at package level. A React implementation still needs its actual compatible React setup. GSAP exposes JavaScript modules and TypeScript declarations. No application lockfile was inspected. [S6–S9](sources.json)

Motion's repository licence is MIT, with notice-retention conditions. GSAP uses its own Standard No Charge licence, not MIT: commercial use is covered, but competing no-code visual-animation builders are restricted and proprietary notices must be preserved. The observed licence page is effective April 30, 2025 and modified May 30, 2025. Review the actual applicable terms before a different product use. Public source access is not unrestricted reuse. [Motion licence](https://github.com/motiondivision/motion/blob/7415b6cd14dfdb35d91dfc5b9758994444cc228b/LICENSE.md), [GSAP licence](https://gsap.com/community/standard-license/)

This is an implementation-pattern study, not a discovery shortlist. The documentation includes example/demo material, but no demo was executed or copied. Choose the library already owned by the component, or CSS for a simple accent. No new server, API, MCP connection, paid service, asset purchase or hosting change is required by the proposed pattern. Costs are ordinary implementation, testing and existing site delivery; exact bundle/runtime cost remains unmeasured. Future application: an original project rail whose pointer accent yields cleanly to keyboard focus and whose previews never become a navigation prerequisite.

## Limits

Documentary research only. No dependency installation, application edits, runtime tests, accessibility audit, performance benchmark, or integration validation occurred. Live docs can change; release dates not observed remain null in source records. Recommendations and the state-combination algorithm are original synthesis, not vendor guarantees. See `sources.json` for provenance and `qa.md` for independent review.
