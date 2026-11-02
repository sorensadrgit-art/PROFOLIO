# Context-safe animation is not cancellation of a late callback

Run: 20261102T181200Z-motion-interaction  
Scheduled: 2026-11-02T18:12:00Z · Evaluation began: 2026-11-02T18:10:41Z · Author: Dot  
Status: documentary research; implementation recommendations and fixtures are untested.

## Question and novelty

How should a React cinematic component handle an animation created after an asynchronous result arrives, especially if its route or input changed meanwhile?

The live catalog, all thirteen prior motion reports and recursive branch tree were inspected at `a74a1c9656107a12b123c8616038126df0c7f4b0`. No same-run artifact or unindexed motion directory existed. The architecture report already recommends contextSafe for delayed callbacks. This addendum isolates a missing distinction: **recording an animation for cleanup does not cancel its producer, reject stale results, or carry the recording context across an await**. This is not a tool shortlist. Work began before the scheduled slot; these are separate timestamps.

## Findings from primary sources

1. The official React adapter describes contextSafe as associating GSAP objects created during a wrapped function's execution with its context. It separately demonstrates removing a manually installed event listener in cleanup. With dependency arrays, `revertOnUpdate: true` requests reversion on dependency changes instead of retaining GSAP objects until hook teardown. [S1]
2. In pinned adapter source, contextSafe delegates to `context.current.add(null, func)`. The hook installs context reversion as effect cleanup; the wrapper itself contains no component-liveness or cancellation check. [S2]
3. In pinned GSAP core, Context.add sets the active context, calls the supplied function, then restores the prior context immediately when that call returns. It does not await a returned promise. Its wrapper does not reject calls because the context was previously reverted. **Source-derived inference:** wrapping an async function does not establish context ownership for animation creation after its await; calling an old wrapper after teardown is not automatically prevented. This conclusion is source inspection, not a runtime test. [S3]
4. React documents cleanup before changed-dependency setup and on unmount. Its fetching example uses an ignore flag to prevent late results from applying after cleanup; development Strict Mode adds a setup/cleanup cycle to expose missing symmetry. [S4]

## Original synthesis: two independent gates

A result needs both **permission to apply** and **a live animation owner**. A cleanup container answers which created animations to revert. A generation/liveness check answers whether this callback may create anything now. Neither substitutes for the other.

Proposed lifecycle, untested:

- Start one setup generation for the current route/data identity. Keep a generation-local disposed flag and a separate request revision if multiple requests can overlap within that generation.
- Start asynchronous work outside the animation-creation wrapper. When it resolves, check disposal, current request revision and target availability immediately before applying it. Do not insert another await between this check and the synchronous animation creation.
- Invoke a synchronous contextSafe apply function that creates the tween. The context records that creation; the caller still owns error handling and source cancellation.
- Cleanup marks the generation disposed, detaches owned listeners and cancels owned timers/abortable requests through their APIs. Revert owned GSAP state. A producer that cannot be cancelled still needs its late result ignored.
- Use dependencies matching the actual scene identity. Where a full scene rebuild is intended, consider `revertOnUpdate: true`; do not rely on changing dependencies alone to retire old GSAP objects. Preserve explicit ownership if rebuilding would erase another layer's intentional state.

This protocol is an original design recommendation, not a library guarantee. Abort support, third-party callback delivery and target lifetime must be checked in the consuming application. It does not make a shared global context suitable for unrelated routes.

## Practical adoption and alternatives

GSAP is a JavaScript animation engine; @gsap/react is its React lifecycle adapter with TypeScript declarations. The pinned adapter package identifies version 2.1.2, React >=17 and GSAP ^3.12.5 peers. Its inspected commit is dated 2025-01-15; inspected GSAP commit is dated 2026-04-13 and declares 3.15.0. These are repository snapshots, not assertions about npm latest, installed versions or exhaustive maintenance health. [S5–S6]

An evaluation project would add compatible `gsap` and `@gsap/react` dependencies, import/register useGSAP and own DOM refs inside its client-side scene boundary. No server, account, API credential or MCP is required for this local animation pattern. Bundling and browser CPU are operational costs; none was measured. The official adapter README includes examples, but no example or demo was executed. [S1, S5]

The actual referenced licence is GSAP's custom Standard No Charge licence, not MIT and not unrestricted reuse. It permits commercial website/interface use without charge but restricts competing visual-animation builders, reverse engineering for competitive products and removal of proprietary notices. Review its incorporated terms for the actual product; the observed effective date is 2025-04-30 and last-modified date 2025-05-30. No licence acceptance or installation was performed. [S7]

Alternatives (design choices, not a feature benchmark): keep the effect synchronous and drive a declarative state transition only after accepted data; use CSS for a simple decoration; or maintain explicit tween references and manual cleanup without the adapter. Each still needs an application-level decision about stale async results. A framework lifecycle wrapper is useful for ownership bookkeeping, not authorization of stale work.

## Original acceptance exercise — not executed

Create an isolated case-study preview with two controllable promises, a pending timer and a manually registered listener. Record setup generation, request revision, accepted/discarded completion and animation creation count, with no personal data.

1. Resolve B before A within one generation: only the current request applies.
2. Unmount before either resolves: no late completion creates an animation or changes DOM.
3. Change scene identity, then resolve the old request: new scene remains unchanged.
4. Compare a wrapped async function that creates after await against a synchronous wrapped apply called after resolution. Inspect context membership before drawing conclusions.
5. Run a development Strict Mode mount cycle: listeners/timers do not accumulate and old completions are rejected.
6. Change dependencies with the chosen rebuild policy: prior owned styles and tweens retire as intended, without clearing another owner's styles.
7. Reject the data promise: error handling does not fabricate a success animation.

Future application: a reusable cinematic-scene adapter could expose a small revision-aware apply boundary for fetched captions, font-ready text effects and media-dependent reveals. It should be adopted only after this exercise passes in supported browsers and the chosen React/GSAP versions.

## Limits and provenance

All findings are bounded documentary inspection on 2026-11-02. No runtime, integration, accessibility, visual or performance tests were run. No source repository was copied wholesale; public outputs are original summaries and a proposed fixture. Source records identify immutable snapshots where available; unknown release/publication dates remain null. Approval means this package's source review and bounded scan, not Gemini approval or deployment permission.
