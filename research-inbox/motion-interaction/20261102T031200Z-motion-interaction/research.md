# Stopping an animation is not returning control to CSS

Run: `20261102T031200Z-motion-interaction` · Scheduled: 2026-11-02 03:12 UTC · Evaluated: 2026-11-02.

## Question and gap

When a responsive cinematic effect is removed, how should its component relinquish styles without erasing another owner's state?

The live catalog, eight previous motion documents and branch tree were inspected first. Earlier packages cover engine ownership and general cleanup; this narrow addendum distinguishes **playhead reset, animation disposal, inline-style removal and restoration of the pre-animation state**. No same-slot or unindexed motion package was observed. This is an implementation-pattern study, not a discovery shortlist.

## Source findings

- GSAP `revert()` restores the target's pre-animation state and kills the animation, including removal of inline styles that animation added. Introduced in 3.11, it differs from `progress(0).pause()`: resetting the playhead writes the starting values as inline styles, which can still override a media-query rule. [S1](https://gsap.com/docs/v3/GSAP/Tween/revert()/)
- `kill()` stops an animation and removes it from its parent; it can target only selected properties or targets. The documented contract is disposal, not pre-animation style restoration. Use a pause rather than kill when the same animation must be reused. [S2](https://gsap.com/docs/v3/GSAP/Tween/kill()/)
- CSSPlugin's `clearProps` removes named inline properties at completion; `all` clears all of them. Clearing a transform alias such as `x` removes the entire combined transform, not just an isolated translation channel. This is deletion, not a promise to restore a previously supplied inline value. CSSPlugin is included with GSAP core. [S3](https://gsap.com/docs/v3/GSAP/CorePlugins/CSS/)
- `gsap.context()` collects animations and ScrollTriggers created through its functions, optionally scopes selectors, and can run custom cleanup. Reversion clears its recorded work, but the same Context can accept new animations afterward. Thus a reverted Context is not a permanent barrier against late callbacks creating new work. [S4](https://gsap.com/docs/v3/GSAP/gsap.context()/)

## Proposed ownership handoff — original synthesis, untested

Choose a cleanup outcome before building the effect:

| Desired outcome | Candidate mechanism | What it does not establish |
|---|---|---|
| Temporarily freeze and later continue | Pause the owned animation | CSS has regained control. |
| Stop an obsolete effect without restoring its baseline | Kill the owned instance | Old inline values have disappeared. |
| Undo the owned effect's style contribution | Revert the owned tween/context | Concurrent writers were reconciled. |
| Intentionally let stylesheet values replace selected inline properties | Narrow `clearProps` after verifying ownership | An earlier inline style was restored. |

The first debugging question is not “which cleanup API removes the flicker?” It is “what should own this property after cleanup?” For an editorial hero, the normal-flow stylesheet should normally remain a complete readable composition. An optional desktop effect may borrow an inner wrapper's transform, then relinquish it on teardown. Keep the layout wrapper and unrelated interactive affordances outside that ownership boundary.

Proposed component lifecycle:

1. Establish the intended static baseline before creating the animation. Record the element/property owner, not a blanket copy of every inline style in the subtree.
2. Create one scoped context for the effect. Keep application state (selected project, expanded state, user motion preference) outside animation state.
3. On teardown, invalidate the component generation and detach owned event listeners, timers and observers so they cannot schedule new animation work.
4. Revert the old owned effect before constructing a replacement on the same properties. Do not build the replacement and then let delayed cleanup restore an older baseline over it.
5. Reconcile the current application state after the handoff. If that state has changed since setup, deliberately construct from the current baseline rather than assuming a historical snapshot is the current desired presentation.
6. Guard every delayed callback with mounted/generation checks before it creates an animation. Context recording helps cleanup, but it does not authorize work after the component's lifetime ends.

This ordering is a proposed engineering contract, not an atomic API or tested guarantee. If two live systems must write the same property, redesign the boundary or explicitly transfer ownership. Neither reverting a snapshot nor deleting inline styles resolves concurrent writers automatically.

Avoid `clearProps: "all"` as a generic component destructor. A framework or another feature may have set legitimate inline width, visibility or custom properties. Clearing a transform alias also affects the shared transform representation, so “only clearing x” can erase more visual state than expected. Prefer separate elements for independent transforms and a narrowly owned teardown plan.

Do not encode persistent application state solely in a completed tween's inline output. If an expanded panel must remain expanded, its semantic/state model should express that outcome independently. Cleanup should not accidentally collapse meaningful content or remove the only visible focus treatment. The proposed fallback keeps content readable and controls usable with no decorative animation; accessibility conformance still requires testing.

## Fit, versions, licence and operating requirements

GSAP is a JavaScript animation toolkit. Repository metadata at commit `13e2b790546426a1a2e0e9b409f3f8dc6d6611f2` declares **3.15.0**, ESM/CommonJS entry points and TypeScript declarations, with no dependency field. The commit is dated **2026-04-13T13:08:30Z**. These observations are not npm-latest, installed-version or future maintenance guarantees. [S5](https://github.com/greensock/GSAP/blob/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2/package.json), [S6](https://github.com/greensock/GSAP/commit/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2)

The actual **Standard No Charge licence is not MIT**. It permits website/application uses subject to terms, restricts competing no-code visual-animation-builder uses, and prohibits removal or alteration of proprietary notices/branding. Public source access is not unrestricted reuse. Review the applicable terms for the intended product. [S7](https://gsap.com/community/standard-license/)

No installation occurred. An eventual implementation needs a compatible GSAP package, browser DOM access for CSS targets, and framework mount/cleanup integration. Defer browser-dependent work to the client lifecycle in an SSR application. Existing lockfile, bundle behavior and target browser compatibility were not checked. Official pages contain examples; none was run or copied. No server, API key, MCP connection, paid service or hosting change is required by this proposal. Costs are implementation/testing and ordinary client delivery; memory savings and frame-time effects are unmeasured.

Alternatives: CSS transitions with class-owned states for simple local feedback; a static composition when choreography is unnecessary; or the existing framework's animation owner with its own documented cleanup contract. Do not add GSAP solely to reset one CSS property. Original future application: a responsive case-study hero can explicitly hand its decorative wrapper back to CSS while retaining the selected project, current theme and keyboard focus across breakpoint changes.

## Acceptance matrix — all NOT RUN

| Scenario | Observation required before adoption |
|---|---|
| Mid-animation teardown then narrow breakpoint | CSS baseline applies without stale inline override. |
| Element starts with a legitimate inline value | Reversion and deletion are distinguished; intended value survives. |
| Another owner controls outer layout | Inner-effect cleanup leaves outer dimensions and focus styles intact. |
| Replacement setup follows teardown | Old cleanup cannot overwrite replacement state. |
| Delayed callback arrives after teardown | No new animation or listener is created. |
| Repeated setup/cleanup and route changes | No accumulated animation resources or style drift. |
| Theme or application state changes during animation | Current intended state wins after explicit handoff. |
| Keyboard, zoom and reduced-motion use | Static content and controls remain usable. |

No runtime, browser, accessibility, performance or integration tests were performed. This is documentary review and original synthesis, not production certification. See sources.json and qa.md for evidence and review limits.
