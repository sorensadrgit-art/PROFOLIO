# Smooth scroll is a cost, not a default: a native-first orchestration policy for PROFOLIO

**Run ID:** `20261101T130953Z-native-scroll-smoothing-03`  
**Topic:** Motion and Interaction  
**Research date:** 2026-11-01  
**Status:** Documentary engineering research; no application code changed

## Executive decision

The new question selected after inspecting the catalog and all three existing motion reports was:

**Should PROFOLIO add a smooth-scroll layer such as Lenis, or should it keep browser-native scrolling and coordinate CSS scroll timelines, Motion, and GSAP around that native source of truth?**

The answer is **native scroll first; smooth scrolling only when a specific art-direction requirement justifies its runtime and accessibility costs**.

This is a distinct addition to the existing collection. Prior packages cover animation-engine ownership, ScrollTrigger choreography and cleanup, React route transitions, and animated-dialog lifecycle. They do not provide a current, cross-engine decision about Lenis/Motion/GSAP frame ownership, the cost of main-thread wheel smoothing, or the October 27, 2026 Firefox 159 scroll-timeline release.

The recommended PROFOLIO policy:

1. Keep browser-native scrolling as the default source of truth.
2. Use CSS scroll/view timelines for simple progress and reveal effects; use Motion for React-owned scroll-linked values and component interactions.
3. Reserve GSAP + ScrollTrigger for deliberately choreographed, pinned or scrubbed narratives.
4. Add Lenis only after a measured prototype shows a meaningful improvement in the intended experience.
5. If Lenis is adopted, assign it exactly one frame-loop driver and explicitly synchronize downstream consumers.
6. Treat reduced motion, touch, nested scroll, route navigation and long main-thread tasks as acceptance criteria, not optional polish.

This is a synthesis of the official sources listed in `sources.json`, not a benchmark of PROFOLIO source code.

## 1. The platform changed: native scroll timelines now have a new stable-browser milestone

Mozilla's Firefox 159 release notes say scroll-driven animations became enabled by default in Firefox 159, released **October 27, 2026**. Chrome and Edge have supported the core feature since version 115; WebKit documents support in Safari 26, with Safari 26.4 adding threaded scroll-driven animations on the compositor thread on **March 24, 2026**. [S09–S11]

The CSS Scroll-Driven Animations specification distinguishes:

- **Scroll-progress timelines**: progress follows a scroll container from its start to its end.
- **View-progress timelines**: progress follows an element as it moves through a scrollport. [S12]

This materially changes the default decision for simple effects. A reading-progress bar, restrained image drift, or nonessential section emphasis can now be expressed declaratively without an application scroll listener in current stable releases of the major browser engines.

Important qualification: the specification allows user agents to sample scroll-driven effects asynchronously, but does not require every implementation or every animated property to run off the main thread. Safari 26.4 explicitly documents threaded execution; do not generalize that statement into a guarantee that every CSS scroll animation is compositor-driven in every engine. Prefer simple properties, inspect actual browser behavior, and profile the target devices. [S10–S12]

The prior motion report correctly treated CSS timelines as progressive enhancement at the time it was written. Firefox 159 is a new compatibility milestone, but the implementation should still retain a static, fully usable baseline and verify exact property/feature support. A browser-version milestone is not a substitute for testing the combinations PROFOLIO actually uses.

## 2. What Lenis changes — and what it does not

The Lenis project describes itself as a smooth-scroll library that operates on **native scroll**, preserving browser scroll position, sticky positioning and anchor behavior. The repository README observed on this research date documents stable package version **1.3.27**. The project is MIT-licensed. [S01–S02]

That distinction matters. Lenis is not the same architectural category as a virtual scroller that replaces the browser's scroll position with a transformed content surface. It smooths wheel-driven movement while continuing to update native scroll. Consequently, native consumers such as CSS timelines and Motion's `useScroll` can continue to observe the page scroll position. Motion's own Lenis integration explicitly confirms that `scroll()` and `useScroll` continue to work unchanged. [S01, S03]

However, native position does **not** mean zero cost. Motion's integration guide calls out the trade-off: Lenis moves wheel scrolling onto the main thread, whereas native scrolling can continue on the compositor while JavaScript is busy. A long task can therefore pause Lenis-driven scrolling and every effect synchronized to that scroll, including effects whose visual property could otherwise be hardware accelerated. [S03]

This is the key decision factor for PROFOLIO: smoothing may improve the perceived pacing of a cinematic sequence, but it adds a main-thread dependency to the act of scrolling itself. On a portfolio with large imagery, route transitions, shader-heavy canvas scenes or other expensive work, that dependency should not be added globally without evidence.

### Version and reduced-motion detail

Lenis 1.3.26 introduced default handling of `prefers-reduced-motion`; the current 1.3.27 README documents `respectReducedMotion` as enabled by default. When the preference is active, smoothing is disabled and programmatic `scrollTo()` becomes immediate; changes to the preference are observed without a reload. [S01–S03]

Do not assume this behavior for older pinned versions. The project's actual lockfile must be checked before relying on it. The current repository did not expose an application lockfile during this documentary run, so no installed PROFOLIO version was verified.

## 3. Frame-loop ownership: the integration detail most likely to cause subtle drift

Lenis needs one animation loop. Its README documents a GSAP integration:

- Listen to Lenis's `scroll` event and call `ScrollTrigger.update`.
- Drive `lenis.raf()` from the GSAP ticker.
- Convert GSAP ticker seconds to Lenis milliseconds. [S01]

Motion documents a different integration when Motion is also consuming scroll:

- Set Lenis to `autoRaf: false`.
- Call `lenis.raf(timestamp)` from Motion's `frame.setup` phase.
- Cancel that frame callback and destroy the Lenis instance during cleanup. [S03]

The reason for Motion's `frame.setup` recommendation is concrete: Motion measures scroll in its later `read` phase. If Lenis updates in `frame.update`, after that read, main-thread scroll-linked values can render one frame behind. Driving Lenis in `setup` writes the native scroll position before Motion measures it. Hardware-accelerated browser-timeline animations are not affected by that specific ordering issue. [S03]

### PROFOLIO integration rule

**Choose one Lenis driver per page. Never combine `autoRaf: true`, a custom requestAnimationFrame loop, a Motion frame callback and a GSAP ticker callback for the same Lenis instance.**

For a page where Motion and GSAP both consume scroll, a practical candidate is:

1. Let Motion's `frame.setup` drive Lenis.
2. Subscribe to Lenis's scroll event and call `ScrollTrigger.update`.
3. Let GSAP's own ticker continue to drive GSAP timelines, but do not also call `lenis.raf()` from that ticker.
4. Pass a small normalized progress value or ref to a WebGL/R3F scene; do not route per-frame scroll through React state.
5. On unmount, cancel the Motion callback, unsubscribe listeners and destroy the Lenis instance.

This mixed-consumer arrangement is a **synthesis** of the official Motion and Lenis integration instructions. It should be tested in a small prototype before becoming a shared site-wide provider. If the page is GSAP-only, the documented GSAP ticker integration is the simpler supported example. [S01, S03, S05]

## 4. Do not add `scrollerProxy()` by reflex

GSAP documents `ScrollTrigger.scrollerProxy()` as an adapter for cases where a third-party scroller needs custom `scrollTop`/`scrollLeft` getters and setters or a custom bounding rectangle. [S06]

Lenis's documented model updates native scroll and its official GSAP example uses `lenis.on('scroll', ScrollTrigger.update)` without a proxy. Therefore, for a root-page Lenis setup that retains native scroll position, **start without `scrollerProxy()`**. Add a proxy only if the chosen setup genuinely uses a custom scroll container or virtual position that ScrollTrigger cannot read through the browser's normal scrolling APIs. This is a documentation-based integration inference, not a claim that every Lenis configuration is proxy-free. [S01, S06]

This avoids adding a second scroll-position abstraction where none is needed.

## 5. The global ticker setting deserves an explicit decision

Lenis's GSAP example calls `gsap.ticker.lagSmoothing(0)`. GSAP's ticker documentation explains that lag smoothing is global to GSAP: the default threshold/adjusted-lag behavior protects synchronization after a long frame gap, and changing it affects tweens, timelines and delayed calls across the engine. Disabling it does not improve actual frame rate. [S01, S07]

Recommendation: **do not copy `lagSmoothing(0)` into a global PROFOLIO bootstrap without testing the consequences for every GSAP animation**. If the documented Lenis integration requires it for a chosen setup, treat it as a deliberate global timing policy, record the reason, and test background-tab return, long tasks and delayed calls. Do not present it as a performance optimization.

## 6. Engine choice for each scroll-linked effect

| Requirement | Default choice | Reason / guardrail |
|---|---|---|
| Reading progress, small progress line, simple view reveal | CSS `scroll()` / `view()` timeline | Declarative and tied to native scroll; keep the no-animation state usable. |
| React component parallax or section progress | Motion `useScroll` | Motion values compose with `useTransform` / `useSpring`; suitable values may use browser ScrollTimeline. |
| Pinned case-study chapter, multi-beat scrubbed story, velocity-aware snap | GSAP + ScrollTrigger | Designed for sequencing, pinning, scrub, callbacks and geometry. Keep pins intentional and limited. |
| Shared WebGL/R3F scene progress | One shared scroll value/ref feeding the renderer | Keep GPU updates in the render loop; do not trigger React reconciliation on every frame. |
| Smoothly eased wheel input | No extra library by default; evaluate Lenis only if the design needs it | Adds a main-thread wheel-processing dependency and extra lifecycle/loop ownership. |
| Smooth anchor navigation only | Native `scroll-behavior: smooth` or the router/browser API | This affects navigation/CSSOM scrolling, not ordinary user wheel/touch scrolling. Respect reduced motion. |

Motion's `useScroll` exposes absolute and normalized scroll values and documents browser ScrollTimeline acceleration for eligible animations. Its `trackContentSize` option is off by default because observing content-size changes has overhead; enable it only when the tracked scrollable content genuinely changes size dynamically. [S04]

ScrollTrigger does not require smooth scrolling to create cinematic effects. GSAP describes it as synchronized with its ticker and browser rendering cycle, and supports custom scrollers where needed. [S05–S06]

GSAP ScrollSmoother is another option within a GSAP-only stack. Its documentation says it retains native scrolling and moves a content wrapper with transforms; it is built on ScrollTrigger and is a Club/members-only plugin. Evaluate its licensing and wrapper implications before comparing it with Lenis. [S08]

## 7. Input, nested-scroll and navigation safeguards

Lenis defaults `anchors` to false; enable it if the application expects Lenis to handle anchor links. Its docs provide `stopInertiaOnNavigate` to stop residual inertia on internal navigation. For nested scroll regions such as a dialog, code block or long menu, use the documented `data-lenis-prevent` mechanism. The alternative `allowNestedScroll` option scans the DOM during scroll and the README warns that this can affect performance; prefer explicit opt-outs where possible. [S01, S03]

For native scroll containers, MDN documents `overscroll-behavior` to control scroll chaining at boundaries. Use it narrowly on actual nested scroll surfaces, such as modal content, rather than globally suppressing expected platform behavior. [S14]

Keep browser scroll anchoring as the default unless a measured layout-shift bug requires a targeted exception. Scroll anchoring exists to keep the user's visible position stable when content above the viewport changes. A blanket `overflow-anchor: none` can remove that useful browser behavior. [S16]

The native `scroll-behavior` property is a simpler option for anchor and programmatic navigation. It is widely available, but applies to navigation/CSSOM-triggered scrolling rather than ordinary user scrolling, and browsers are permitted to ignore it. [S15]

## 8. Reduced motion is an input policy, not only an animation override

The W3C understanding document for WCAG 2.2 Success Criterion 2.3.3 (Level AAA) says interaction-triggered motion should be disableable unless essential; it specifically identifies parallax as a possible vestibular trigger. [S13]

For PROFOLIO:

- Keep native scroll available in every mode.
- With reduced motion, do not smooth wheel or touch input.
- Make programmatic anchor navigation immediate.
- Remove parallax and large spatial drift.
- Replace long pinned sequences with ordinary document flow.
- Keep essential state changes and focus feedback understandable without animation.
- Respond to a live preference change, not only the preference at first page load.

Lenis 1.3.26+ supplies a useful baseline for its own smoothing behavior, but it does not automatically disable GSAP, Motion, CSS or custom WebGL effects. Those systems still need their own reduced-motion policy. [S03, S13]

## 9. Proposed PROFOLIO decision and prototype plan

### Decision

**Do not make Lenis a global dependency yet.** First prototype the desired scroll feel using native scroll plus CSS timelines, Motion and ScrollTrigger. Only compare Lenis if the design still requires eased wheel input after those native mechanisms are evaluated.

If a Lenis prototype is justified, use the current stable 1.3.27 baseline for evaluation (not an unverified prerelease), keep `respectReducedMotion` enabled, keep `syncTouch` off until device testing justifies it, set explicit anchor/nested-scroll behavior, and implement one frame-loop owner.

### Prototype acceptance matrix — NOT RUN

1. Chrome/Edge current stable: native scroll timeline and Lenis/no-Lenis comparison.
2. Firefox 159 stable: verify `scroll()`, `view()` and the exact animated properties used.
3. Safari 26.4+ desktop: inspect threaded scroll animations and pinned scenes.
4. iOS Safari: touch momentum, address-bar resize, low-power behavior and fixed-position UI.
5. Reduced motion: toggle while the page is open; verify smoothing and all custom motion respond.
6. Nested dialog/code region: verify inner scrolling, background lock and scroll chaining.
7. Anchors and route changes: no blocked links, stale inertia or incorrect restored position.
8. Long main-thread task: compare native scroll continuity with Lenis.
9. Background tab return: check GSAP ticker timing and scroll synchronization.
10. Dynamic content/font/image load: check progress measurement and ScrollTrigger geometry.
11. WebGL scene: confirm one normalized scroll signal reaches the renderer without React state churn.
12. Teardown/remount: confirm only one RAF driver, no stale listeners and no duplicate Lenis instance.

Record real devices, browser versions, CPU throttling, frame traces and interaction observations before making a performance claim. This research contains no such benchmark.

## 10. Limitations and source/version notes

- No PROFOLIO application source or lockfile was inspected in this run; installed GSAP, Motion, React and Lenis versions are unknown.
- Lenis 1.3.27 is the stable version observed in the current repository README and package metadata on 2026-11-01. The Motion integration page documents the 1.3.26 reduced-motion change.
- GSAP references are current v3 documentation; exact package/plugin version is not pinned here.
- Motion references are current official documentation; the page does not provide a package version.
- Firefox 159 release notes are dated 2026-10-27; Safari 26.4 WebKit feature notes are dated 2026-03-24.
- No runtime, accessibility, battery, input-latency, scroll-jank or cross-device benchmark was performed.
- No code, dependencies, licenses or assets were installed or changed.

## Sources

See `sources.json` for 16 source records, exact URLs, publisher, retrieval date, version/scope notes and claim mapping.
