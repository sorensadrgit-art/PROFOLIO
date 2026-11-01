# Which open-source carousel is worth evaluating for an original editorial portfolio rail?

Run: 20261101T124229Z-open-source-creative-smoke  
Scheduled execution: 2026-11-01T12:42:29.430845Z (manual smoke run). Evaluated: 2026-11-01.  
Scope: two projects, one decision; documentary research only.

## Decision — original synthesis, not an installation approval

Evaluate **Embla 8.6.0 first** for a conventional React case-study rail with custom editorial styling. Keep **Keen Slider 6.8.6** as an alternative when its rendering controls are genuinely needed. Start with a static responsive list if a rail does not improve navigation; visual novelty alone does not justify a dependency.

The catalog read contained no creative-discovery packages; this shortlist adds component selection and version discipline rather than repeating the existing general motion/navigation reports.

## Verified source findings

### Embla Carousel

The stable React guide documents a hook, automatic unmount cleanup, an overflow viewport and scroll container. Controls belong outside the drag viewport. Its v8 example uses `scrollPrev`/`scrollNext`; the current v9 RC guide uses `goToPrev`/`goToNext`. Do not combine snippets across those documentation versions. [E1,E2]

The pinned 8.6.0 React package depends on matching core/reactive-utils versions and declares React 16.8, 17, 18 and 19 peer ranges. That is a package declaration, not a tested Next.js compatibility guarantee. [E3]

GitHub's latest stable release endpoint returned v8.6.0, published 2025-04-04. The observed default-branch commit is dated 2026-10-12; a branch commit does not mean a stable release includes it. [E5,E6]

### Keen Slider

The official guide documents `useKeenSlider`, a container callback ref and instance ref, stylesheet import, media-query breakpoints and custom rendering. When several breakpoint queries match, the last matching configuration applies. A rendering mode called performance is an option, not a benchmark result. [K1]

The observed package version is 6.8.6. It lists React 18 as a development dependency; do not infer React 19 support from that. GitHub's latest release endpoint returned v6.8.6, published 2023-07-05; the observed default-branch commit is dated 2025-10-06. The release age warrants compatibility testing, not an unsupported claim that the project is abandoned. [K2,K4,K5]

### Reuse and delivery

Both actual LICENSE files contain MIT terms: retain copyright and permission notices when redistributing copies or substantial portions. This does not establish rights to unrelated photographs, logos, fonts or demo media. No third-party assets were copied. [E4,K3]

Both can be evaluated as browser libraries within the application, not deployed research services. Embla's React package and Keen's documented React import are the relevant integration surfaces; MCP is not required for this frontend use. Pin versions in a later authorized evaluation. Neither was installed here.

## Original adaptation recipe — untested recommendation

1. Build a readable linked case-study list first: title, short role, year, and locally licensed cover image.
2. Enhance only the list viewport. Keep navigation controls outside the draggable region; retain ordinary links and visible focus treatment.
3. Use an asymmetric card grid and original typography instead of copying a demo's visual identity.
4. Keep slide-track transforms owned by the carousel. If adding decorative motion later, animate a nested card wrapper, not the engine-owned track.
5. Avoid autoplay initially. Provide a static/reduced-motion alternative and explicitly test keyboard, focus, touch, zoom and screen-reader behavior.
6. Reserve image dimensions, then test delayed images, resize and route remounts. Do not assume a React hook removes listeners created by application code.

## Troubleshooting and proposed acceptance tests

| Symptom | Check before adding another library |
|---|---|
| Embla method missing | Verify installed major and documentation version; v8 and v9 examples differ. |
| Buttons also begin dragging | Place controls outside drag viewport and inspect event ownership. |
| Unexpected responsive settings | Inspect overlapping Keen media queries and their order. |
| Janky card animation | Profile actual device/media load and competing transform writers; an option name is not evidence. |
| Modern React route failure | Reproduce with the exact framework/React versions in an isolated project. |

A future evaluation should test no-JavaScript readability, keyboard navigation, focus visibility, no automatic focus jumps, reduced motion, mobile swipes with vertical page scrolling, repeated mount/unmount, delayed assets and responsive changes. Compare a static list with both libraries using identical licensed content. These are proposed tests: **none were executed**.

## Limitations

No package installation, runtime, performance, accessibility, browser, security dependency audit or production integration test was performed. Demos are discovery links, not executed evidence. Release metadata is observed at evaluation time, not a claim about future versions. Independent documentary review and local structural/privacy checks are recorded separately in qa.md.

## Sources

- [E1] [Embla React v8](https://www.embla-carousel.com/docs/v8/get-started/react)
- [E2] [Embla React current documentation](https://www.embla-carousel.com/docs/get-started/react)
- [E3] [Embla React package manifest](https://github.com/davidjerleke/embla-carousel/blob/v8.6.0/packages/embla-carousel-react/package.json)
- [E4] [Embla licence](https://github.com/davidjerleke/embla-carousel/blob/master/LICENSE)
- [E5] [Embla latest stable release](https://api.github.com/repos/davidjerleke/embla-carousel/releases/latest)
- [E6] [Embla latest observed default-branch commit](https://api.github.com/repos/davidjerleke/embla-carousel/commits?per_page=1)
- [K1] [Keen Slider documentation](https://keen-slider.io/docs)
- [K2] [Keen package manifest](https://github.com/rcbyr/keen-slider/blob/master/package.json)
- [K3] [Keen licence](https://github.com/rcbyr/keen-slider/blob/master/LICENSE)
- [K4] [Keen latest release](https://api.github.com/repos/rcbyr/keen-slider/releases/latest)
- [K5] [Keen latest observed default-branch commit](https://api.github.com/repos/rcbyr/keen-slider/commits?per_page=1)
