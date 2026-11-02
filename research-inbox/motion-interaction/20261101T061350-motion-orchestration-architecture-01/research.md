# A coherent motion system for PROFOLIO: choosing the right scroll engine, owning animated properties, and making motion resilient

**Run ID:** `20261101T061350-motion-orchestration-architecture-01`  
**Topic:** Motion and Interaction  
**Research date:** 2026-11-01  
**Status:** Evidence-based engineering research; no application code changed

## Executive summary

The valuable unanswered question for this category is not “which animation library is best?” It is: **how should a React portfolio combine native CSS timelines, Motion, GSAP/ScrollTrigger, SVG, and a 3D render loop without creating competing sources of truth, fragile pins, inaccessible text, or expensive per-frame React work?**

The strongest answer is a layered motion architecture with explicit ownership:

1. **CSS first** for simple decorative transitions and, where supported, simple scroll-progress or view-progress effects. Treat CSS scroll-driven animation as progressive enhancement, not a universal dependency.
2. **Motion for React** for component-local interaction, layout transitions, entry state, and composable scroll values where React state and animation belong together.
3. **GSAP + ScrollTrigger** for complex, choreographed timelines: pinning, scrubbed multi-scene narratives, directional snapping, sequencing across SVG/text/media, and detailed scroll geometry.
4. **A dedicated 3D frame loop** for canvas/WebGL animation. Feed it a small normalized progress signal; do not drive React reconciliation on every rendered frame.
5. **One owner per animated property on a given element.** When two systems need to affect the same visual object, separate them with nested wrappers or expose a single shared progress value rather than letting multiple engines write the same `transform`.

This is an architecture recommendation synthesized from current official documentation, not a benchmark against PROFOLIO code. The catalog had no prior motion-interaction package, so this is a new topic rather than a duplicate.

## 1. Select the smallest engine that can express the interaction

| Interaction | Preferred tool | Why | Guardrail |
|---|---|---|---|
| Hover, focus, pressed, small component transitions | CSS transitions or Motion | Local, state-driven and easy to keep declarative | Provide keyboard/focus parity; avoid hover-only meaning |
| Simple reveal when an element enters view | CSS view timeline when supported; otherwise IntersectionObserver/Motion `useInView` | Visibility threshold is enough; no continuous pixel scrub is needed | Keep content visible without JS; avoid hiding essential text before animation |
| Reading progress bar or simple image drift | CSS scroll timeline when supported, or Motion `useScroll` | Progress maps directly to one or two visual properties | Feature-detect CSS timelines; keep the scroll experience native |
| Multi-beat hero / pinned case-study chapter / scrubbed story | GSAP timeline + ScrollTrigger | Explicit sequencing, scrub, pin, snap and geometry controls | Create triggers in document order; keep pinning limited and test layout changes |
| Shared-element/card-to-detail layout transition | Motion `layout` / `layoutId` | Layout projection is a first-class declarative capability | Avoid measuring every render unnecessarily; use `layoutDependency` when appropriate |
| SVG stroke reveal or shape morph | CSS stroke dash for simple cases; GSAP DrawSVG/MorphSVG for complex choreographies | Dedicated SVG controls and path morph tooling | Keep a static SVG fallback; verify plugin licensing and build inclusion |
| 3D scene reacting to scroll | Three.js/R3F frame loop | GPU scene updates belong in the renderer loop | Mutate refs/uniforms; do not call React `setState` on every frame |

**Evidence.** Motion's `useScroll` exposes absolute and normalized scroll motion values and documents that suitable animations can use the browser ScrollTimeline API. Motion's `useInView` is based on visibility and offers an explicit `once` option. GSAP ScrollTrigger supports scrub, pin, snap, velocity and callbacks. Motion's layout animation uses `layout` and `layoutId`. [S01–S03, S08–S10, S23]

**Synthesis.** Do not choose a library by the number of effects it can produce. Choose by the interaction's control model: state transition, visibility threshold, continuous scroll progress, choreographed timeline, or renderer frame loop.

## 2. Use native scroll timelines as progressive enhancement

CSS scroll-driven animations define two useful models:

- A **scroll-progress timeline** maps the scroll position of a scroller from its start to its end.
- A **view-progress timeline** maps an element's traversal through a scrollport.

These are a good fit for low-complexity effects such as a reading-progress indicator, subtle image scale, a decorative underline, or a small entry/exit movement. The animation remains tied to the browser's scroll timeline instead of requiring application code to continually measure scroll position. [S11]

However, MDN currently marks the `scroll()` function as limited availability / not Baseline because it is not supported in some widely used browsers. Chrome's modern guidance recommends feature detection with both `animation-timeline` and `animation-range`, and a fallback for effects that must work everywhere. [S12–S14]

Recommended progressive-enhancement pattern:

- The unanimated layout is already complete and legible.
- CSS timeline effects enhance only decoration or nonessential emphasis.
- Use `@supports ((animation-timeline: view()) and (animation-range: entry))` for view-entry effects, or the corresponding scroll-timeline test for progress effects.
- If the feature is absent, leave the content static or use a small IntersectionObserver-based reveal when a reveal is genuinely useful.
- Avoid adding a large polyfill solely to reproduce decorative scroll motion; Chrome's guidance explicitly discourages the scroll-timeline polyfill as a fallback because it is not feature-complete and has known issues. [S13, S14]

A subtle implementation trap: the CSS `animation` shorthand resets `animation-timeline` to `auto`. Declare `animation-timeline` after any `animation` shorthand, or the intended scroll timeline may silently be lost. [S11]

## 3. Reserve ScrollTrigger for real choreography

ScrollTrigger is most valuable when the page has a designed narrative rather than a collection of independent reveals:

- A case-study chapter pins while a sequence advances.
- Several visual layers move at different rates against one shared timeline.
- A sequence scrubs forward and backward with the reader's scroll.
- Timeline labels define meaningful snap points.
- SVG, copy, image masks and interface elements need a synchronized order.

The documentation describes `scrub` as linking animation progress to scrollbar progress, with a numeric value smoothing the playhead's catch-up. Pinning holds a selected element while content continues. Snap can settle at progress points or timeline labels. ScrollTrigger calculates start/end positions up front, refreshes them on resize, and updates animation progress in sync with GSAP's animation loop. [S01]

Engineering rules:

1. **Create triggers in page order.** An upstream pin changes downstream document geometry; creating triggers out of order can produce stale start/end measurements.
2. **Animate children of a pinned wrapper, not the pinned element itself.** The pin's geometry is part of the trigger calculation.
3. **Understand pin spacing.** The default padding spacer usually preserves document flow, but flex/absolute layouts can behave differently; inspect `pinSpacing` rather than assuming it.
4. **Refresh after genuine geometry changes.** ScrollTrigger's `refresh()` recalculates start/end positions. Use it after known content changes (for example, after font/layout completion), not on every scroll event.
5. **Avoid transformed ancestors around fixed pins where possible.** GSAP notes that transforms or `will-change` on ancestors can alter fixed-position behavior.
6. **Avoid `content-visibility: auto/hidden` on geometry that ScrollTrigger must measure.** GSAP documents this as a source of incorrect measurements.
7. **Use pins sparingly on small screens.** Replace a long pinned sequence with ordinary stacked content or a simpler local reveal when viewport height is constrained.

These are not aesthetic preferences: they directly address the precomputed geometry on which ScrollTrigger relies. [S01]

## 4. Define animation ownership in React

The main failure mode in mixed animation stacks is not that two libraries exist. It is that two systems write the same element/property at different times.

For example, Motion may project a card's `transform` during a layout transition while GSAP writes `x`, `y`, or rotation to that same card, and CSS also applies a transform on hover. The resulting transform composition can be order-dependent and hard to debug.

Adopt this rule:

> **One animation owner per element/property pair. Separate independent motion layers in the DOM.**

A practical structure:

- Outer wrapper: layout/position owned by React layout or CSS.
- Middle wrapper: scroll-linked translation owned by Motion or ScrollTrigger.
- Inner visual: hover/press scale owned by CSS or Motion.
- SVG/canvas child: its own path or renderer-level state.

If one story needs GSAP choreography and Motion component state, expose one normalized progress value or put the systems on separate wrappers. Do not let both engines animate the same transform on the same node.

For React integration:

- GSAP's `useGSAP()` hook from `@gsap/react` scopes selector targets and automatically reverts animations, ScrollTriggers and SplitText instances created during the hook.
- Animations created later inside click handlers, timers or asynchronous callbacks are not automatically context-safe; wrap them in `contextSafe()` and remove manually registered event listeners during cleanup.
- When dependencies change and the GSAP setup must be rebuilt, use `revertOnUpdate: true` deliberately.
- React's own `useEffect` requires cleanup that mirrors setup. Strict Mode intentionally performs an extra development setup/cleanup cycle to expose missing cleanup. [S03, S04, S16]

This is especially important for route transitions, modal mount/unmount, hot reload and responsive reinitialization. Treat an animation instance, observer, event listener and pin spacer as resources with an explicit owner and teardown path.

## 5. Make responsiveness and reduced motion part of the animation definition

GSAP `matchMedia()` can build and revert animations when viewport or preference media queries change. Its context automatically reverts animations and ScrollTriggers created in the matching setup; custom event listeners still need manual removal. The official examples include both desktop/mobile conditions and `prefers-reduced-motion`. [S02]

Motion provides `MotionConfig reducedMotion="user"` and `useReducedMotion()`. The documented policy disables transform and layout animations while preserving less spatial properties such as opacity and background color. Motion's accessibility guidance recommends replacing large transform movement with opacity, disabling autoplaying video and disabling parallax for reduced-motion users. [S09, S10]

Recommended motion tiers:

| Experience | Default preference | Reduced-motion alternative |
|---|---|---|
| Hero entrance | Short opacity + small translation | Immediate content, optional opacity only |
| Parallax layers | Small, bounded depth | No parallax; preserve composition |
| Pinned chapter | Short, optional narrative sequence | Ordinary document flow; no forced pin |
| Large-scale zoom/pan | Restrained and brief | Static framing or simple crossfade |
| Text line reveal | Brief line-level reveal | Fully visible text |
| Background video | User-controlled or nonessential | Poster image; no autoplay |
| Hover/focus feedback | Color/border/opacity and small scale | Color/border/opacity only |

Do not interpret reduced motion as “remove all feedback.” Keep focus indicators, selected-state changes, progress information, and understandable state transitions. Remove spatial movement that is not necessary to communicate state. [S15]

## 6. Typography and SVG require semantic fallbacks

### Text

GSAP SplitText's v3.13.0+ documentation adds automatic screen-reader accessibility, responsive re-splitting and masking. Its default `aria: "auto"` places an accessible label on the original split element and marks generated line/word/character wrappers as hidden. The documentation warns that this does not preserve the semantics/functionality of nested elements such as links; those cases need a separate accessible representation or a different splitting strategy. `autoSplit` and `onSplit()` can re-split when fonts load or line wrapping changes. [S05]

Recommendations:

- Prefer line-level or word-level reveal over per-character animation unless the effect clearly benefits from character timing.
- Never make the only copy of a heading dependent on successful JS splitting.
- Keep the semantic heading in the DOM and verify the accessibility tree after splitting.
- Do not split interactive nested links into decorative wrappers without a preserved semantic link.
- Wait for the intended font metrics before calculating line splits; use `document.fonts.ready` when geometry depends on loaded fonts.
- Re-test after responsive width changes because line boundaries change. [S05, S18]

### SVG

For a simple stroke draw, CSS `stroke-dasharray` and `stroke-dashoffset` can be enough. GSAP DrawSVG provides a higher-level way to reveal portions of supported SVG strokes. MorphSVG interpolates SVG path data and documents `curveMode` as a feature added in GSAP 3.14.0. [S06, S07]

Keep decorative SVG animation separate from essential labels and icon meaning. Provide a static final state when animation is disabled or scripting fails.

## 7. Coordinate Canvas and 3D without turning React into a frame loop

A 3D story can respond to scroll, but the DOM scroll system and the WebGL renderer have different update lifecycles.

Recommended bridge:

1. Calculate a normalized story progress value in the DOM layer (0–1).
2. Write that value to a ref or renderer uniform, not React state on every frame.
3. In React Three Fiber, read the ref in `useFrame` and mutate the relevant object/uniform using the frame delta.
4. If using on-demand rendering, invalidate/request a frame when progress changes.
5. Pause or reduce scene work when the canvas is offscreen or the document is hidden.

R3F's hooks documentation says `useFrame` runs before render and unsubscribes when the component unmounts; it explicitly warns against calling React `setState` inside the frame loop. The performance guide recommends on-demand rendering for scenes that can rest, reusing geometry/materials, and adapting quality to observed device performance. If imported glTF clips are used, Three.js `AnimationMixer.update(delta)` belongs in the same renderer loop; stop actions before uncaching them. [S20, S21, S24]

This creates a clean boundary: GSAP/Motion/CSS own the scroll narrative; the 3D scene consumes a scalar or small set of parameters. Avoid coupling every DOM scroll tick to a full React component-tree update.

## 8. Performance and layout stability

- Prefer compositor-friendly `transform` and `opacity` for high-frequency motion. Properties that trigger layout or paint should be profiled before use.
- Use `will-change` sparingly; layer promotion can have memory and compositing costs.
- Blur, large shadows, masks and filters may be visually expensive even if the animated element is transformed.
- Use DevTools Performance recordings and paint flashing to identify actual paint/layout work rather than assuming an effect is cheap. [S13, S22]
- Use IntersectionObserver for visibility transitions, lazy-starting media or pausing non-visible work; it is not a replacement for a continuous pixel-accurate scroll timeline. Its callback runs on the main thread and should remain short. [S17, S23]
- Use `document.fonts.ready` before measuring line wraps and `ResizeObserver` to detect genuine element-size changes. If those changes affect pinned geometry, schedule a debounced ScrollTrigger refresh after layout settles; do not create a resize-refresh feedback loop. [S18, S19]

## 9. Suggested implementation blueprint (recommendation, not implemented)

1. Establish a motion token sheet: durations, easing curves, distance tiers, stagger limits and reduced-motion variants.
2. Define an ownership table for every animated section: CSS, Motion, GSAP or R3F.
3. Build small interaction primitives first: focus/hover/press, nav transition, modal/card layout and simple entry reveal.
4. Add one ScrollTrigger-driven case-study narrative only after static content order and mobile layout work without JavaScript.
5. Add typography splitting only after semantic markup, font loading and screen-reader output are tested.
6. Bridge a future 3D scene through normalized progress and refs, keeping frame updates inside R3F.
7. Add a route-level cleanup test and a reduced-motion test before expanding the effect library.

### Acceptance checklist

- [ ] Every content section remains readable with JavaScript disabled.
- [ ] No two animation engines write the same transform on one DOM node.
- [ ] Keyboard focus and hover states communicate the same essential interaction.
- [ ] Reduced-motion mode removes parallax, large pans/zooms and forced pins.
- [ ] Motion setup and teardown survive React Strict Mode and route changes.
- [ ] ScrollTrigger pins survive resize, font load, image load and responsive breakpoints.
- [ ] Mobile layout has a non-pinned alternative for long stories.
- [ ] SVG and split text retain accessible semantics.
- [ ] Canvas updates do not trigger React state updates per frame.
- [ ] Performance is profiled on a low/mid-tier mobile device; no FPS claim is made without measurement.

## 10. Version and evidence notes

- GSAP documentation is served under the v3 docs. The current SplitText documentation marks its major rewrite and accessibility/auto-splitting features as v3.13.0+; MorphSVG marks `curveMode` as new in v3.14.0. Confirm the exact installed package and plugin availability before adopting these features.
- Motion documentation is the current live React documentation as accessed on 2026-11-01; the docs do not pin a release number on the cited pages. Verify the project lockfile and migration guide before implementation.
- React's official `useEffect` reference identifies the documentation version as React 19.3.
- MDN currently marks CSS `scroll()` / scroll-driven animation support as limited or not Baseline. Browser support must be checked at implementation time.
- R3F and Three.js documentation is live documentation; this repository-only research run did not inspect a local package manifest or execute a benchmark.

## Conclusion

For PROFOLIO, the strongest motion system is not “GSAP everywhere” or “CSS only.” It is a progressive, ownership-driven stack: CSS for small native effects, Motion for React-local state/layout and scroll values, GSAP for high-control cinematic choreography, and R3F for renderer-frame work. Keep the page understandable without motion, and treat responsive geometry, reduced motion, cleanup and property ownership as core architecture—not finishing polish.

---

## Source key

See `sources.json` for full provenance, retrieval dates, version notes and claim mapping. References [S01]–[S24] are official vendor, framework or browser documentation. No third-party copied content or benchmark claims are included.
