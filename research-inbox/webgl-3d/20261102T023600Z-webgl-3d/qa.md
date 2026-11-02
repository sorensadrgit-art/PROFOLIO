# Independent QA

Reviewed 2026-11-02, approximately 02:36 UTC. Result: **pass for a bounded, untested research note**, with one terminology clarification recommended before publication.

## Checks actually performed

- Read the complete research, sources and manifest files. Parsed both JSON files; six unique source IDs, source count, run identity and HTTPS source URLs are consistent. Pending QA/validation fields in the manifest still require the publisher's final update.
- Freshly opened official WebGLRenderer and common Info documentation. The former supports manual reset after a complete multipass frame; the latter distinguishes cumulative render calls from per-frame render calls and draw calls.
- Freshly fetched both source implementations, package manifest and actual LICENSE through GitHub at commit `8086b3fda07a70bffad7ee39265c9617994ec482`. WebGLInfo reset clears calls and primitive counters, not memory counts. Instance counts participate in primitive accumulation. Common Info reset clears frame counters rather than cumulative render calls or memory tracking.
- Confirmed the pinned common implementation's compressed-texture size helper returns 1; program size uses code-string length; automatically generated mipmaps use a 1.333 multiplier. The conclusion that tracked totals are not exact physical VRAM is appropriately limited to inspected source.
- Confirmed manifest version 0.187.0, ordinary/WebGPU/TSL exports, development dependencies and absence of a dependencies field. Fresh commit metadata confirms 2026-11-01T22:25:58Z. A snapshot is not proof of an npm release or installed application version.
- Inspected MIT terms, including notice preservation and warranty disclaimer. The report does not claim the library licence covers unrelated assets.
- Bounded sensitive-data pattern scan covered the three candidate package files: research.md, sources.json and manifest.json. No private-key/token patterns, authenticated URLs, local/private-context paths or authorization-header patterns were found. Manual reading found no credentials or private user context. This is not an exhaustive secret audit.

## Clarification and publication housekeeping

The WebGLInfo finding should call `render.calls` a **draw-call counter**, not merely “render calls”: its update function increments once per draw, unlike common Info's render invocation counter. The report's broader warning about schema differences is correct; explicit wording prevents the exact ambiguity being discussed. No other substantive factual blocker was identified.

Before publishing, update the manifest's pending QA/validation statuses truthfully and confirm all four package files are present. Publication itself was not performed by this reviewer.

## Limits

No renderer runtime, browser, GPU, performance, accessibility or integration tests were executed; the proposed fixture remains unrun. JSON parsing and source inspection are the only validation techniques used here. Raw-source web fetches returned cache misses; pinned GitHub fetches subsequently supplied the source successfully. The author's eight-package duplicate comparison and historical inventory were not independently repeated, so this review does not certify catalog completeness or exclusive novelty. Moving documentation, wrapper behaviour, npm artifacts and application lockfiles remain unverified.

## Publisher disposition

At 2026-11-02T02:37:36Z, the draw-call wording was corrected and manifest review/validation statuses updated. Final JSON parsing and bounded scan of all four package files passed. Approval is documentary only, not Gemini approval or permission to deploy.
