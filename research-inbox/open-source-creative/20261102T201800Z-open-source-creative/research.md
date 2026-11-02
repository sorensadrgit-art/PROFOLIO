# Lottie: an image-load event is not a successful asset audit

Run: 20261102T201800Z-open-source-creative  
Scheduled: 2026-11-02 15:18 America/New_York (20:18 UTC). Evaluated: 2026-11-02.

## Decision — original synthesis, not tested adoption

Shortlist **lottie-web 5.13.0** for a small, original vector illustration whose authored timeline materially improves a portfolio case study. Keep a static poster and ordinary text as the baseline. Do not reveal essential content merely because a player emits a readiness event.

The live catalog contained 87 packages; eleven existing creative reports were read. This adds authored-animation asset readiness, distinct from Troika text synchronization, Atropos tilt cleanup and generic GSAP ownership. No existing same-slot package or unfinished topic directory was found. The existing COBE nested-index omission is unrelated and preserved.

Main question: **What does a Lottie readiness event actually establish before replacing a poster?** Supporting questions: how do SVG and canvas differ at initialization, and what should the application own around failures?

## Source findings

### Separate animation configuration, image settlement and rendering readiness

The pinned README distinguishes configuration, animation data, image settlement and DOM construction events. In particular, loaded_images covers both successful and failed image loads. It documents SVG, canvas and HTML renderers, instance control and repeaters requiring a deep-cloned animation object when reused. Global destroy without a name affects all registered animations; prefer instance ownership in a component. [L1]

The pinned image preloader explains why a success-only interpretation is wrong: both HTML-image and SVG-image error handlers replace the stored image with a transparent proxy and advance the loaded counter. loadedImages() compares counts, not a collection of successful responses. Its completion callback receives null, including after those error paths. Thus the source supports **settled assets**, not a per-image successful-fetch certificate. Safari’s SVG path also polls geometry; cross-browser behavior was not executed here. [L3]

AnimationItem.imagesLoaded() triggers loaded_images and invokes checkLoaded(). That gate requires loaded fonts and footage; it additionally requires the image-count condition for canvas, but the image condition is bypassed for non-canvas renderers. On passing the gate it marks isLoaded, initializes renderer items, schedules DOMLoaded with a zero-delay timer, renders the current frame and optionally starts playback. **Inference from this branch:** SVG DOM readiness should not be promoted into proof that every image has already settled. It is a source-level inference, not an observed browser trace. [L2]

The same source emits data_ready when no further segments remain **or when autoloadSegments is disabled**. Therefore its name alone should not be interpreted as proof that every potential segment was fetched. data_failed is used for segment fetch failure; this does not turn it into a general image-failure inventory. The reviewed destroy path destroys renderer and image preloader, emits destroy and clears callbacks/references. It does not establish cancellation of application-owned fetches, timers or state updates. [L2]

### Purpose, versions and maintenance

lottie-web is a JavaScript browser player for exported animation data, with TypeScript declarations. The inspected package manifest lists version 5.13.0, a player entry point and development/build tools, but no runtime dependencies field. It does not declare a React peer range; a wrapper's framework compatibility cannot be inferred from this manifest. [L4]

npm metadata reports latest 5.13.0, published **2025-05-21T17:23:47.288Z**. The observed default-branch head is **bede03d25d232826e0c9dca1733d542d8a7754fb**, committed **2024-11-19T12:25:21Z**, and identifies version 5.13.0. Those are different kinds of dates. The package tarball was not compared byte-for-byte with this source commit. GitHub's latest-release endpoint returned 404; a GitHub release publication date remains null, not inferred from npm or commit time. These observations warrant a compatibility check, not a claim of abandonment. [L6–L8]

### Licence, integration and costs

The actual pinned LICENSE.md is MIT, copyright Bodymovin: retain copyright and permission notices with distributed copies or substantial portions. This does not grant rights to arbitrary animation JSON, fonts, images, logos or demo designs. No demo assets or complete repository were copied. [L5]

A future authorized experiment would install the pinned npm package, serve an original/licensed JSON export and its referenced assets, create an instance inside a browser-owned container, and destroy that instance on teardown. It needs no API credentials, MCP server or separately hosted backend for basic playback. React/Next integration must initialize in the client and define route cleanup; exact framework compatibility is untested. The README links exporter and demo routes, but neither demos nor After Effects were operated. [L1,L4]

Player code does not impose a paid runtime service. Asset hosting/bandwidth, export-authoring tools, design time and visual QA still have costs; no price or performance estimate was measured. Treat animation input and referenced asset URLs as untrusted: constrain approved origins and asset provenance rather than accepting arbitrary third-party exports as harmless. This is an untested application recommendation, not a security audit.

## Original application contract — untested recommendation

Use two distinct records rather than one boolean called loaded:

1. **Player state:** configuration observed, renderer ready, playing/paused, retired.
2. **Content acceptance:** expected assets identified, provenance/licence checked, failures handled, poster replacement permitted.

Give each instance a generation identifier. Late events from a retired instance must not replace a new route's poster. A current DOMLoaded event may advance player state without advancing content acceptance. Do not wait forever for an image-specific event in an asset-free illustration; define a separate no-image case. Retain the poster if an approved asset cannot be served, rather than treating a transparent proxy as the intended artwork.

Use instance-scoped controls. Keep semantics in HTML and make the animation decorative unless a separately tested accessible description is appropriate. Provide explicit pause/reduced-motion behavior. A simpler SVG/CSS transition is preferable for a few state changes; a licensed pre-rendered video is an alternative for dense authored motion where vector interactivity adds little. Neither alternative was benchmarked.

Future application: a case-study illustration component could display a documented poster until its own asset acceptance policy succeeds, then reveal a bounded animation once. Its diagnostic receipt would distinguish renderer-ready, asset-failed, fallback-kept and user-paused states without collecting visitor-sensitive data.

## Proposed acceptance exercise — not executed

- Use an original export with one intentionally missing image. Compare loaded_images with the visible output and network failure in both SVG and canvas; do not label the event as success.
- Delay image responses while allowing fonts/data to arrive. Record DOMLoaded and image settlement order separately for both renderers.
- Test segmented data with autoloadSegments disabled; ensure the application does not label all segments downloaded.
- Test no-image exports, failed JSON, invalid asset origins, unmount during loading and immediate route replacement.
- Test reused repeater data, instance-only destruction, keyboard controls, reduced motion, poster meaning and screen-reader duplication.
- Measure actual transferred bytes, CPU and frame behavior on representative devices before choosing a renderer.

## Limitations

Documentary review only. No installation, browser playback, export validation, runtime, integration, visual, accessibility, security or performance test was performed. Source review is pinned; wiki/documentation or npm changes may diverge later. No guaranteed event ordering across all exports or browsers is claimed.

## Sources

- [L1] [Pinned README and usage](https://github.com/airbnb/lottie-web/blob/bede03d25d232826e0c9dca1733d542d8a7754fb/README.md)
- [L2] [Pinned AnimationItem](https://github.com/airbnb/lottie-web/blob/bede03d25d232826e0c9dca1733d542d8a7754fb/player/js/animation/AnimationItem.js)
- [L3] [Pinned image preloader](https://github.com/airbnb/lottie-web/blob/bede03d25d232826e0c9dca1733d542d8a7754fb/player/js/utils/imagePreloader.js)
- [L4] [Pinned package manifest](https://github.com/airbnb/lottie-web/blob/bede03d25d232826e0c9dca1733d542d8a7754fb/package.json)
- [L5] [Pinned MIT licence](https://github.com/airbnb/lottie-web/blob/bede03d25d232826e0c9dca1733d542d8a7754fb/LICENSE.md)
- [L6] [npm metadata](https://registry.npmjs.org/lottie-web)
- [L7] [Observed default-branch head](https://api.github.com/repos/airbnb/lottie-web/commits?per_page=1)
- [L8] [GitHub latest release lookup, returned 404](https://api.github.com/repos/airbnb/lottie-web/releases/latest)
