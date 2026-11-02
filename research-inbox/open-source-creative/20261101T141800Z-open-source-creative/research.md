# A detail viewer, not another carousel: PhotoSwipe or Yet Another React Lightbox?

Run: `20261101T141800Z-open-source-creative`  
Scheduled: 2026-11-01 09:18 America/New_York (14:18 UTC). Evaluated: 2026-11-01.

## Decision — original synthesis, not tested integration

Evaluate **Yet Another React Lightbox (YARL) 3.33.0** first when a React portfolio needs controlled image-detail viewing. Evaluate **PhotoSwipe 5.4.4** when preserving ordinary image links and a framework-independent enhancement is the stronger requirement. A linked full-size image remains the simplest alternative if zoom and multi-image navigation add little value.

This run adds image-detail/zoom selection to the existing catalog, not another Embla/Keen rail comparison. A carousel moves through project cards; a lightbox must also handle enlarged-image resolution, dismissal and return to the invoking context. The earlier dialog-lifecycle report remains relevant but does not select these image libraries.

## Verified findings

### PhotoSwipe

PhotoSwipe is a JavaScript image gallery/lightbox with separate core and lightbox entry points and CSS. Its tagged package supplies type declarations and does not declare runtime dependencies; development/build dependencies are separate. [P1]

The getting-started guide requires image dimensions in the linked-image markup. Responsive sources use `data-pswp-srcset`; dimensions describe the largest image and generated `sizes` changes with zoom. Recommended anchor markup retains a large-image destination for unsupported browsers. [P2]

The official React page illustrates enhancement of a grid, with the core dynamically imported after thumbnail activation. It is not evidence of tested compatibility with a particular Next.js release. [P3]

GitHub returned stable release v5.4.4 dated 2024-05-24 and default-branch commit `cd41cb587a460634e4cae53134f3d01d06e284a6` dated 2025-12-04. Release age is a reason to test, not proof of abandonment. Branch changes are not necessarily included in the stable release. [P5,P6]

### Yet Another React Lightbox

YARL is a React component with TypeScript declarations and optional plugin exports. Its tagged manifest declares React and React DOM peer ranges covering 16.8 through 19. The source version field is `0.0.0-semantic-release`; the observed release tag is v3.33.0, not a published version inferred from that placeholder. [Y1,Y5]

The API exposes `open`, `close`, `slides` and `index`. When a parent can rerender while open, use a stable slides reference or provide the current index: replacing the array/reference can reset state from the current props. Responsive slides can include dimensions and alternate resolutions. [Y2]

Zoom is an optional plugin, not a reason to assume every custom renderer behaves identically. The Next.js guide documents dynamic loading and custom `next/image` rendering, but explicitly warns that Zoom does not work well with that image component; it offers an alternative using optimized-image URLs and the built-in slide renderer. [Y3,Y7]

GitHub returned v3.33.0 published 2026-10-30 and default-branch commit `6a6128c4338ba5fb1cc2a7a9411425fe872f8664` dated 2026-10-30. This documents observed activity, not future support or a security audit. [Y5,Y6]

### Reuse, hosting and costs

Both tagged LICENSE files contain MIT terms; redistribution of copies or substantial portions requires retaining copyright and permission notices. These licences do not establish rights to demo photographs, fonts or branding. No third-party media or full repository was copied. [P4,Y4]

Both are application-side frontend libraries, not new backend services. PhotoSwipe uses JavaScript/CSS; YARL uses React/TypeScript/CSS and React DOM. A future install would use the named versioned package plus required styles, but nothing was installed here. Neither needs MCP for this use. No paid service is required by the reviewed setup; actual image storage, bandwidth and optional image-transformation costs depend on hosting and were not priced.

## Original adaptation recipe — recommendations to test

1. Keep a meaningful, linked project-image grid as the baseline. Store an internal image ID, full-size URL, dimensions, alt text and licensed source variants; do not mistake array position for persistent identity.
2. Open a detail viewer on explicit activation. Use restrained original typography, image captions and controls instead of copying the demo's design or media.
3. Load viewer code on demand where appropriate, but preserve a useful failure destination if loading fails. Avoid importing both viewers into the production bundle merely to compare them.
4. In YARL, stabilize slides or deliberately control index across rerenders; test reordered/removed images while open. Evaluate the documented Zoom/Next-image alternative before customizing its renderer.
5. In an imperative PhotoSwipe integration, define instance ownership and teardown before adding route transitions. Cleanup behavior is an acceptance requirement here, not a lifecycle test we performed.
6. Let the viewer own its image transforms. Animate an independent surrounding presentation only when it does not compete with zoom/pan.

## Proposed evaluation and common mistakes

| Symptom | First investigation |
|---|---|
| Viewer jumps back during state updates | Inspect YARL slides-reference stability and index ownership. |
| Zoom looks soft or mis-scaled | Verify source dimensions, available resolution and actual selected image, not just CSS size. |
| Next.js custom image zoom behaves unexpectedly | Compare with the documented built-in renderer/optimized URL approach. |
| Image cannot open after script failure | Retain a real full-image link, not only a click handler. |
| Route changes leave stale UI | Test repeated mount/unmount, focus return and listener cleanup in the exact application. |

Before adoption, compare both against a simple linked image using identical licensed media. Test keyboard entry/Escape/return focus, labels, reduced motion, touch zoom versus page scroll, slow/failed image fetches, dynamic-list changes, repeated route changes and responsive resolution selection. Measure transferred bytes and interaction latency on actual devices. These are **unexecuted tests**, not claims of accessibility or speed.

## Limitations and sources

Documentary review only. No install, browser interaction, runtime, accessibility, performance, dependency-security or production-integration test was run. Live documentation is unversioned and may differ from tagged packages; pin and validate during a separately authorized evaluation. Demos were not operated. Source metadata, licences and links are in `sources.json` and `repos.json`; independent review is in `qa.md`.

- [P1] [Tagged package manifest](https://github.com/dimsemenov/PhotoSwipe/blob/v5.4.4/package.json)
- [P2] [Official setup and API documentation](https://photoswipe.com/getting-started/)
- [P3] [Official framework integration guide](https://photoswipe.com/react-image-gallery/)
- [P4] [Tagged MIT licence](https://github.com/dimsemenov/PhotoSwipe/blob/v5.4.4/LICENSE)
- [P5] [Latest release metadata](https://api.github.com/repos/dimsemenov/PhotoSwipe/releases/latest)
- [P6] [Latest observed default-branch commit](https://api.github.com/repos/dimsemenov/PhotoSwipe/commits?per_page=1)
- [Y1] [Tagged package manifest](https://github.com/igordanchenko/yet-another-react-lightbox/blob/v3.33.0/package.json)
- [Y2] [Official setup and API documentation](https://yet-another-react-lightbox.com/documentation)
- [Y3] [Official framework integration guide](https://yet-another-react-lightbox.com/examples/nextjs)
- [Y4] [Tagged MIT licence](https://github.com/igordanchenko/yet-another-react-lightbox/blob/v3.33.0/LICENSE)
- [Y5] [Latest release metadata](https://api.github.com/repos/igordanchenko/yet-another-react-lightbox/releases/latest)
- [Y6] [Latest observed default-branch commit](https://api.github.com/repos/igordanchenko/yet-another-react-lightbox/commits?per_page=1)
- [Y7] [Official Zoom plugin documentation](https://yet-another-react-lightbox.com/plugins/zoom)
