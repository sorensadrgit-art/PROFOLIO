# Independent documentary QA

Evaluated: 2026-11-02. Scope: research.md, sources.json, manifest.json and this QA report; official-source inspection and local text/JSON checks. No publication or remote mutation performed.

## Result: PASS — documentary review; no blocking claim issue remains

- The principal distinction is supported: safe refresh defers measurement; it is not a promise that later application layout cannot change. The official [refresh reference](https://gsap.com/docs/v3/Plugins/ScrollTrigger/static.refresh()/) documents the delay and current-DOM measurement, not an arbitrary resource barrier.
- [Update](https://gsap.com/docs/v3/Plugins/ScrollTrigger/static.update()/) and [animation invalidation](https://gsap.com/docs/v3/Plugins/ScrollTrigger/) are described without confusing scrolling state, trigger geometry and animation starting values.
- Font readiness caveats match the [W3C Working Draft](https://www.w3.org/TR/css-font-loading-3/#font-face-set-ready), including subsequent loading and non-rejection. Draft status/date are accurately identified.
- The pinned [package metadata](https://github.com/greensock/GSAP/blob/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2/package.json) supports version 3.15.0, entry points, declarations and absence of a dependency field. It is not represented as npm latest or the installed application version.
- The [licensor terms](https://gsap.com/community/standard-license/) support the limited license summary. This is a documentary check, not product-specific legal clearance.
- The coordinator is clearly identified as original, untested synthesis. No copied implementation or extended source passages were observed. The claimed prior-catalog review and broader novelty were not independently reproduced in this limited review.

## Precision correction verified

Step 5 was corrected during review and now names the global `refresh` event for a page-level receipt, distinguishing an individual instance's `onRefresh` callback. The existing warning about automatic refreshes is appropriate; event-to-revision attribution remains an implementation obligation, not a vendor transaction guarantee.

## Provenance limitation

The exact commit identity and committer timestamp, 2026-04-13T13:08:30Z, were independently verified through the GitHub connector's GET of the [Git commit resource](https://api.github.com/repos/greensock/GSAP/git/commits/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2). The earlier web-tool API access failure was recovered through that read-only connector; no permission denial occurred. Branch-head observation and prior-catalog duplicate review remain author-owned evidence, not independently repeated QA checks.

## Local checks

- sources.json and manifest.json parse successfully; source count is seven with unique IDs and consistent run IDs. Manifest review fields were still pending at inspection and may be finalized to documentary PASS by the author.
- Bounded regex/manual inspection of all four package files found no apparent private keys, common credential-token prefixes, quoted credential assignments, private filesystem paths, localhost endpoints or email addresses. This is not a comprehensive secret-detection guarantee.
- All seven cited public URLs resolved in the documentary browser tool.

## Not run / not established

No browser execution, runtime, integration, performance, accessibility-conformance, lockfile or compatibility tests were authorized or performed. The acceptance matrix remains NOT RUN. No implementation readiness certification is implied. No remote publication verification was performed.
