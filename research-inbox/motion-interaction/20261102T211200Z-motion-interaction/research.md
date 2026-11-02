# Overwrite resolves a current conflict, not the lifetime of an interaction

Run: 20261102T211200Z-motion-interaction  
Scheduled: 2026-11-02T21:12:00Z · Evaluation began: 2026-11-02T21:16:22Z · Author: Dot  
Status: documentary research; recommendations and acceptance exercise are untested.

## Question and gap

Can `overwrite: "auto"` make a new interaction the permanent owner of a target, including its delayed or paused animations?

No. Treat overwrite as conflict handling at a particular point in animation initialization, not as an ownership registry. If a new state invalidates an old sequence, explicitly retire the old owner's scheduled work instead of depending on the next tween to discover it.

The live catalog, fourteen existing motion reports and repository tree were read first. Prior reports address hover intent, style reversion, initial writes, scrubbed playheads and asynchronous context cleanup. This addendum isolates the scope and timing of overwrite, especially already-created future or paused tweens. No same-run package or unfinished same-topic directory was observed. A separate pre-existing COBE catalog-index discrepancy is preserved, not repaired here.

## Source findings

1. **Three policies, different scope.** GSAP's Tween documentation says the default is no overwrite. `true` removes other tweens on the same targets immediately, irrespective of property. `"auto"` acts at first render on conflicting properties in active animations; unrelated properties remain. This is not equivalent to “cancel every older animation for this component.” [S1](https://www.gsap.com/docs/v3/GSAP/Tween/)

2. **Pinned implementation supports a time-specific interpretation.** In GSAP 3.15.0, `_initTween` passes its property lookup and global initialization time into the root timeline's cancellation routine. The numeric-time selection path requires initialized, non-paused candidates spanning that time. The constructor's `overwrite === true` path instead calls target-wide cancellation before adding the new tween. These observations describe the inspected source, not a guarantee about all plugin internals or future versions. [S2](https://github.com/greensock/GSAP/blob/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2/src/gsap-core.js)

3. **Explicit cancellation reaches pending work.** `gsap.killTweensOf(target)` also affects tweens whose delay has not elapsed. A property list can narrow cancellation. A GSAP delayed call uses its callback function as its target; cancelling tweens of the DOM element is therefore not the same as cancelling a delayed callback that may later create a tween. [S3](https://gsap.com/docs/v3/GSAP/gsap.killTweensOf()/)

4. **A timeline can narrow the search domain.** `timeline.killTweensOf(targets, props, onlyActive)` addresses tweens inside that timeline. Choosing `onlyActive: true` intentionally excludes pending/inactive work. It is the wrong option when the requirement is retiring the owner's already-scheduled future writes. [S4](https://gsap.com/docs/v3/GSAP/Timeline/killTweensOf()/)

**Derived inference, not an executed test:** a paused or future tween that survives the new tween's auto-overwrite check can later become eligible to write. A newer tween's creation does not by itself establish a permanent exclusion rule. Explicitly clearing existing work still does not prevent a timer, listener or async continuation from creating more work later; the previous context-safe package covers that producer boundary.

## Original synthesis: an ownership protocol

Use four distinct records for a cinematic card or gallery detail: current user intent, owner identity, scheduled animation handles, and producer cleanup handles. Do not encode all four in an overwrite flag.

1. Decide which state wins using application state, including keyboard and pointer intent. A tween's creation timestamp is not a reliable product policy.
2. If old state becomes invalid, retire that state's timeline or retained tween handles. Use target-wide cancellation only when this component owns all relevant target properties. If sharing is unavoidable, name the exact properties and use an owner-local timeline.
3. Stop timers/listeners and reject stale async continuations belonging to the retired state. Cancelling an animation does not cancel its creator.
4. Start the replacement visual from the appropriate current value or explicitly restored baseline. Cancellation is not CSS restoration; follow the earlier reversion report for that separate decision.
5. Reserve `overwrite: "auto"` for defensively resolving concurrent property writers within the intended model. Do not promote it to authorization, cleanup or state-transition logic.

An outer layout wrapper and inner interaction wrapper can separate scroll-driven transforms from hover transforms. That is a proposed composition pattern, not a universally correct DOM structure. Check resulting focus rings, hit areas and clipping independently.

### Decision shortlist

- **One reversible timeline:** useful when enter/leave traverse the same choreography. Retains a clear owner and direction; still needs teardown and reduced-motion policy.
- **Retained tween handles or owner-local timeline cancellation:** preferred when replacing one complete scene with another. Requires explicit bookkeeping; avoids destroying unrelated target effects.
- **Global `killTweensOf` with selected properties:** useful only with known target/property ownership. Can still affect another component using those properties.
- **Target-wide `overwrite: true`:** deliberate broad replacement, not a safer version of auto. It may cancel an unrelated opacity tween while replacing position.
- **CSS transitions or Web Animations API:** alternatives for smaller state changes. They do not eliminate the need to define interruption and ownership behavior; APIs were not separately evaluated here.

## Tool evaluation and integration

GSAP is a JavaScript property-animation engine; its package includes ESM/CommonJS entry points and TypeScript declarations. Both current npm metadata and pinned repository package metadata report 3.15.0; the inspected repository commit is dated 2026-04-13. This is evidence of a specific maintained source state, not an uptime/support promise. Package metadata declares no runtime dependency map. Official documentation contains embedded demonstrations, but none were executed. [S5](https://github.com/greensock/GSAP/blob/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2/package.json) [S6](https://registry.npmjs.org/gsap/latest)

A future implementation could pin `gsap@3.15.0`, import GSAP in the application's client lifecycle and create DOM-targeted animation only when those targets exist. No server, hosted API, MCP registration, credential or account connection is required for this local animation use. No packages were installed. React adapters are optional and their separate dependency contract should be checked when selected. Actual browser/framework compatibility remains untested; avoid interpreting general vendor compatibility claims as a project acceptance result.

**Licence and cost:** the Standard No Charge GSAP License permits ordinary commercial websites/interfaces, subject to its terms. It is not an unrestricted MIT-style grant: competing no-code visual-animation builders are restricted, proprietary notices must remain, and the referenced terms apply. Effective date is April 30, 2025; last modified May 30, 2025. Do not copy showcase assets or substantial documentation into an implementation. No new paid service is needed for the proposed use; engineering, rendering and QA costs remain workload-dependent and unmeasured. [S7](https://gsap.com/community/standard-license/)

## Original acceptance exercise — not executed

Build one plain object with independently observable position and opacity, then a real DOM fixture. Record event time, owning handle, progress and resulting properties without assuming every completion callback will fire after cancellation.

| Scenario | Proposed evidence to collect |
|---|---|
| Active older position+opacity tween, new position with auto | Does only position lose the old writer while opacity continues? |
| Older delayed position tween, replacement auto starts before delay | Does the delayed writer survive and later conflict? |
| Older paused position tween, new auto, later resume old | Does explicit ownership cleanup prevent a resumed stale writer? |
| Same setup with true overwrite | Are unrelated same-target properties also cancelled? |
| Owner-local cancellation with and without onlyActive | Is future work removed only with the intended scope? |
| Delayed callback creates new tween after retirement | Does callback/producer cleanup prevent recreation? |
| Two components sharing a target | Does cancellation stay within the agreed owner/property contract? |

Repeat on route teardown, reduced-motion changes and rapid alternating input. Test nested timelines, seeking, repeats and CSS-plugin aliases before extending these conclusions beyond this narrow contract. An original future application is a development-only “motion ownership ledger” that flags unretired handles when a component changes state; it would diagnose scheduling mistakes, not certify performance or accessibility.

## Limits

No runtime, integration, visual, accessibility or performance tests were run. No claimed speed or frame-rate gain. Source inspection is pinned; documentation and npm latest are mutable. Release publication time was not established and remains null. Initial malformed documentation URLs failed; corrected official URLs were successfully read. Only original research and short API identifiers are published, not source-code copies.
