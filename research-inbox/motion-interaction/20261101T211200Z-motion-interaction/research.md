# Returning to a tab must not undo a user's motion pause

Run: `20261101T211200Z-motion-interaction` · Scheduled: 2026-11-01 21:12 UTC · Evaluated: 2026-11-01.

## Question and gap

How should an ambient, automatically looping portfolio animation combine a user's Pause choice, page visibility and reduced-motion preference without unexpectedly restarting?

The live catalog, six existing motion research documents and their manifests were inspected first. They cover engine ownership, route transitions, dialog exits, scroll smoothing, drag cancellation and current hover intent. The branch tree contained no same-run directory or unindexed motion package. This addendum addresses persistent **playback permission**, not another scroll engine or hover recognizer.

## Source findings

1. **User control is not background throttling.** WCAG 2.2 SC 2.2.2 (Level A) covers automatically starting moving/blinking/scrolling information that continues beyond five seconds alongside other content, with an essential-activity exception. It requires a user mechanism to pause, stop or hide. Its separate auto-updating branch has no five-second condition. [S1](https://www.w3.org/TR/WCAG22/#pause-stop-hide)
2. The W3C explanation says a pause that requires keeping focus on the animation is insufficient because the user cannot continue using the page. It also distinguishes resuming from the paused point from resuming at current real-time information. The explanation is informative, not a replacement for the criterion. [S2](https://www.w3.org/WAI/WCAG22/Understanding/pause-stop-hide.html)
3. HTML defines document visibility as `visible` or `hidden` and dispatches `visibilitychange` when the state updates. Browser rendering throttling is not uniquely determined by visibility. Therefore visibility should be treated as a scheduling input, not evidence of user consent or a guaranteed CPU-saving mechanism. [S3](https://html.spec.whatwg.org/multipage/interaction.html#page-visibility)
4. `prefers-reduced-motion: reduce` signals a preference to remove or replace nonessential motion; `no-preference` means no preference was communicated, not a request for animation. The cited Media Queries Level 5 document is a Working Draft. [S4](https://www.w3.org/TR/mediaqueries-5/#prefers-reduced-motion)
5. GSAP `pause()` without a position stops at the current playhead. `resume()` preserves playback direction; when timeScale is exactly zero, it changes that scale to one. Neither method decides whether playback is currently appropriate. [S5](https://gsap.com/docs/v3/GSAP/Tween/pause()/), [S6](https://gsap.com/docs/v3/GSAP/Tween/resume()/)
6. GSAP `matchMedia()` collects animations created in its handler for reversion. Its conditions form can revert and rerun that handler when a condition changes. An application must not assume local state inside that setup survives reconstruction. The feature was added in 3.11.0. [S7](https://gsap.com/docs/v3/GSAP/gsap.matchMedia()/)

## Proposed contract — original synthesis, NOT implemented

Keep durable user intent outside an animation instance or a media-query setup callback. Maintain independent inputs:

```text
userPaused       // changed only by the user's explicit control
pageVisible      // current document visibility
reduceMotion     // current system/media preference
mounted          // component lifetime
eligible         // this decorative effect is currently appropriate

mayRun = mounted && eligible && pageVisible && !reduceMotion && !userPaused
```

A single reconciliation function owns the animation's running state. Each input event updates only its own state, then reconciles. In particular, `visibilitychange` must never assign `userPaused = false`. A newly constructed animation starts paused, reads current inputs, and is allowed to run only after reconciliation. This avoids a brief default autoplay during rebuild.

For this proposed conservative policy, reduced motion selects a static composition, not an infinitely repeating zero-duration animation. Keep all project titles, links and meaningful information available in that composition. Toggling the preference off does not erase an explicit Pause choice. If product requirements later allow a per-effect override, specify that distinct user choice rather than silently conflating it with Resume.

Use a plainly labelled native button for the explicit Pause/Resume control and preserve the user’s pause choice while focus moves to other content; do not move or trap focus. Explain when reduced motion keeps the effect static; do not show a misleading “playing” state. The control's model should distinguish **user requested pause** from **temporarily not running**. Pausing because the tab is hidden must not make the control appear as though the user pressed Pause.

Apply pause/resume only to the owned ambient instance, not GSAP's global timeline or ticker. Otherwise an unrelated transition or essential state feedback may be frozen. On teardown remove listeners, invalidate old callbacks and dispose/revert only owned animation resources. If responsive setup creates a replacement instance, immediately reconcile it against the existing user-intent state. This is a design proposal; event cleanup and framework lifecycle handling require implementation tests.

A decorative loop can resume from its retained playhead. Do not reuse that behavior unchanged for a live countdown or data ticker: stale visual time can misrepresent actual state. This package does not propose pausing data acquisition, freezing business clocks, or claiming that a paused animation stops network traffic.

## Alternatives and operational fit

- **Prefer static art** when looping adds little information. This removes the playback-control problem rather than concealing it.
- **Use the existing animation owner.** This pattern does not require adopting GSAP if a component already has another controllable animation implementation. The adapter must expose equivalent ownership and lifecycle semantics; no cross-library adapter was tested.
- **Bounded non-looping entrance** may be a simpler design, but a shorter duration alone does not establish compliance with all accessibility requirements.

GSAP is a JavaScript animation toolset. The inspected repository package declares version **3.15.0**, ES-module/CommonJS entry points and TypeScript declarations; it lists no dependency field. Repository head `13e2b790546426a1a2e0e9b409f3f8dc6d6611f2` has commit date **2026-04-13T13:08:30Z**. These are repository observations, not an npm-latest or installed-version assertion, and not a maintenance guarantee. [S8](https://github.com/greensock/GSAP/blob/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2/package.json), [S9](https://github.com/greensock/GSAP/commit/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2)

GSAP uses its Standard No Charge licence, **not MIT**. Its terms permit website/application use but restrict competing no-code visual-animation builders and require preservation of proprietary notices. Public code availability is not unrestricted reuse; review the actual terms for the intended product. [S10](https://gsap.com/community/standard-license/)

No installation occurred. An eventual client-side implementation needs a compatible installed GSAP version if that adapter is chosen, browser visibility/media-query handling, and component-scoped cleanup. Application lockfile, browser matrix and exact build integration remain unknown. The official docs include examples/demos; none was executed or copied. No backend, API credential, MCP connection, paid service or hosting change is required by this proposed local policy. Costs are implementation, testing and existing delivery; memory, battery, bundle size and frame-rate improvements are unmeasured. Original future application: one portfolio-wide ambient-motion preference that consistently controls background flourishes while leaving navigation and essential state feedback intact.

## Acceptance matrix — all NOT RUN

| Sequence | Expected future observation |
|---|---|
| User pauses → switches tab → returns | Animation stays paused and control remains truthful. |
| Running → hidden → visible | Resumes only if every permission input still allows it. |
| User pauses → responsive/media setup rebuilds | Replacement instance never autoplays before reconciliation. |
| Reduced motion enabled at load or toggled live | Static readable composition; no zero-duration infinite loop. |
| Reduced motion disabled while userPaused remains true | Explicit Pause persists. |
| Pause → keyboard focus moves to another control | Pause persists without trapping focus. |
| Route unmount during queued callback → return | Old callback cannot restart or affect replacement instance. |
| One ambient effect pauses beside unrelated transition | Only the intended owner changes; no global freeze. |
| Real-time content prototype | Display freshness and animation state assessed separately. |

Test target browsers, keyboard and assistive-technology behavior, live preference changes, repeated mount/unmount and actual performance traces before adoption. This is documentary research only: no runtime, accessibility-conformance, integration, browser, performance or power-use test passed or was attempted. Sources and version limitations are recorded in `sources.json`; independent review is in `qa.md`.
