# A flowmap is an effect primitive, not a production interaction system

Run: 20261101T231800Z-open-source-creative  
Scheduled: 2026-11-01T23:18:00Z. Evaluated: 2026-11-01. One-project discovery.

## Decision — original synthesis

Shortlist **OGL** for a small, original pointer-driven shader accent when the team can own GLSL and GPU lifecycle code. Do not adopt its full-window mouse-flowmap example unchanged as a portfolio component. Prefer a static image or CSS treatment when distortion adds no useful expression; reuse an existing renderer rather than introducing a second graphics engine merely for this effect.

The live catalog, topic directory and all four previous creative packages were read first. They cover carousels, lightboxes, command menus and text splitting. No same-run or unfinished topic directory was observed. This adds a shader-effect candidate and example-to-component boundaries, not another general renderer comparison.

## Verified findings

### Purpose, package and maintenance

OGL uses JavaScript ES modules and exposes TypeScript declarations. The inspected source manifest says **1.0.11**, marks sideEffects false and declares no dependencies or React peer contract. That is not a verified npm release or a Next.js compatibility certification. [O2]

Its minimal WebGL abstraction is intended for developers writing shaders. The README demonstrates a screen-space effect without a camera or scene hierarchy and recommends pinning CDN versions. Treat its size figures as maintainer estimates, not measured savings in this application. [O1]

The latest observed default-branch commit is **385ce65c352c70734a36dc98c787fdd1d30ddb3b**, dated **2025-04-13**. The latest GitHub release endpoint returned 404; release version/date remain unknown. Age alone does not prove abandonment. Published npm metadata was inaccessible through the research tool. [O6]

### Flowmap mechanics and useful pattern

The inspected Flowmap class allocates two square render targets, defaults to size 128, writes one while reading the other, then swaps them and updates a shared uniform's value. It uses float/half-float texture paths and selects filtering based on WebGL version/extensions. These branches are not evidence that every GPU can render the effect. [O3]

The fragment shader multiplies previous field values by dissipation on each update before mixing in the current stamp. **Inference from code:** equal dissipation with different update counts is not a time-normalized decay guarantee. A future implementation must evaluate how pausing or varying frame cadence affects the intended trail. [O3]

The example passes flowmap.uniform directly to the material, preserving the container whose texture value changes after swapping. It updates pointer position, velocity and aspect separately, and resets input when no new movement arrives. Capturing a single initial texture instead of following the uniform would violate this demonstrated ownership pattern. [O4]

### Why the demo is not a drop-in component

The example appends a canvas to the document body, listens on window, normalizes input against renderer width/height and uses a repeating animation loop. Its layout is full-window, with DPR fixed at 2; no component-unmount teardown appears in the inspected example. Its viewport declaration disables user scaling. These are source observations, not browser-test results. [O4]

It credits an external water texture. The code licence does not independently establish permission to redistribute that image. Do not copy it into a portfolio merely because the example is public. [O4]

## Licence, installation and costs

The README contains actual **Unlicense** text, including a public-domain dedication and warranty disclaimer; the source manifest declares Unlicense. A separate root LICENSE fetch returned 404, so provenance here points to the README text rather than a nonexistent file. Preserve that provenance and inspect the exact distributed package and any third-party assets before reuse. No legal clearance of unrelated media is implied. [O1,O2]

For a later authorized prototype, verify and pin an available ogl package, import its modules into the frontend build and supply original shaders/media. No package was installed here. This is a browser library: the reviewed integration requires no backend service, authentication, API account or MCP connection. It does require a working WebGL context and compatible texture/render-target capabilities. React/Next ownership and client initialization must be tested in the exact app.

No paid service is required by this proposed local frontend use. Hosting transfer, shader work, GPU battery use and engineering maintenance remain costs; no price, bundle or frame-time benchmark was measured. The official index supplies learning examples, not certified production components. [O5]

## Original adaptation plan — untested recommendations

1. Keep semantic HTML, navigation and the real image outside the decorative canvas. Retain a readable static fallback for script, context or asset failure.
2. Bound the effect to one nonessential project-cover region. Convert pointer coordinates using that region's actual rectangle rather than copying full-window normalization; test scrolling, offsets and resize.
3. Give one owner the animation loop, input handlers, render targets, image callbacks and teardown. Cancel work before route removal and prevent late asset callbacks from updating a retired instance.
4. Preserve the swapping uniform container. Treat flow-field resolution and display DPR as separate quality controls; do not scale both indiscriminately.
5. Make reduced-motion, explicit pause, hidden/offscreen behavior and resumed-time policy deliberate. Compare a still treatment before adding continuous simulation.
6. Do not suppress page zoom or scrolling to make the effect easier to implement. Keep the visual enhancement out of the way of links and focus.
7. Use original procedural colour/noise or separately licensed media. A future application could show a restrained ink-like trail around a project cover, then settle to the unchanged artwork. This is a design proposal, not a built result.

Alternatives: plain CSS/image presentation for no application-owned WebGL lifecycle; an effect implemented inside an already-owned renderer when a scene already exists; custom native WebGL only when maintaining the extra low-level code is justified. These alternatives were not benchmarked or shortlisted as additional repositories.

## Proposed acceptance and limitations

Before adoption, test exact framework mount/unmount, nested/scrolled coordinate mapping, zoom, touch and hybrid input, context loss, unavailable float render targets, delayed/failed images, reduced motion and pause/resume. Compare 60/120 Hz behavior and low-end devices with the same original content. Inspect retained listeners, pending frames and GPU resource ownership after repeated navigation.

**No installation, demo interaction, runtime, browser, accessibility, performance, dependency-security or integration tests were performed.** Static source review establishes implementation patterns only. Exact npm artifact and release metadata remain unverified. Independent documentary QA and bounded structural/privacy checks are recorded separately.

## Sources
- [O1] [OGL README and embedded Unlicense](https://github.com/oframe/ogl/blob/385ce65c352c70734a36dc98c787fdd1d30ddb3b/README.md)
- [O2] [OGL package manifest](https://github.com/oframe/ogl/blob/385ce65c352c70734a36dc98c787fdd1d30ddb3b/package.json)
- [O3] [OGL Flowmap implementation](https://github.com/oframe/ogl/blob/385ce65c352c70734a36dc98c787fdd1d30ddb3b/src/extras/Flowmap.js)
- [O4] [OGL mouse-flowmap example source](https://github.com/oframe/ogl/blob/385ce65c352c70734a36dc98c787fdd1d30ddb3b/examples/mouse-flowmap.html)
- [O5] [OGL official example index](https://oframe.github.io/ogl/examples/)
- [O6] [Observed latest OGL default-branch commit](https://github.com/oframe/ogl/commit/385ce65c352c70734a36dc98c787fdd1d30ddb3b)

