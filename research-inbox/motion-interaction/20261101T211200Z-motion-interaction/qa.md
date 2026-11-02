# Independent documentary QA

**Verdict: PASS — documentary research only, with verification limits below.**

Reviewed 2026-11-01 UTC by a separate reviewer. Scope: `research.md`, `sources.json`, `manifest.json`; no publication or application modification performed by this reviewer.

## Checks performed

- Independently retrieved all ten cited primary URLs. Verified WCAG 2.2 SC 2.2.2's Level A applicability and the distinction between the moving-content five-second condition and the separate auto-updating branch. Confirmed that the Understanding document is informative and rejects focus-dependent pause. [WCAG](https://www.w3.org/TR/WCAG22/#pause-stop-hide), [Understanding](https://www.w3.org/WAI/WCAG22/Understanding/pause-stop-hide.html).
- Confirmed reduced-motion/no-preference semantics and the Working Draft status/date. [Media Queries Level 5](https://www.w3.org/TR/mediaqueries-5/#prefers-reduced-motion).
- Confirmed current-playhead pause, resume direction and zero-timeScale exception, conditions-based reversion/rerun, and matchMedia introduction in 3.11.0. These API mechanics do not establish the proposed application's correctness. [pause](https://gsap.com/docs/v3/GSAP/Tween/pause()/), [resume](https://gsap.com/docs/v3/GSAP/Tween/resume()/), [matchMedia](https://gsap.com/docs/v3/GSAP/gsap.matchMedia()/).
- Confirmed pinned package version 3.15.0, import/require/type metadata, and absence of a dependencies field in the inspected package file. Independently retrieved GitHub API master-commit metadata: SHA `13e2b790546426a1a2e0e9b409f3f8dc6d6611f2`, author and committer date `2026-04-13T13:08:30Z`. This does not establish npm latest or any installed version. [Package](https://github.com/greensock/GSAP/blob/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2/package.json), [commit](https://github.com/greensock/GSAP/commit/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2).
- Confirmed custom Standard No Charge licence, restrictions concerning competing visual-animation builders and proprietary notices, effective date 2025-04-30, and modified date 2025-05-30. This is source checking, not legal advice. [Licence](https://gsap.com/community/standard-license/).
- Independently confirmed displayed WCAG publication date 2024-12-12, Media Queries draft date 2026-02-19, and HTML Living Standard displayed update date 2026-10-30. The HTML date is a displayed document update, not an immutable release date. Retrieval windows are author provenance, not independently reconstructed tool timestamps.
- Parsed both JSON files successfully, checked ten source records against source_count, and matched research citations S1–S10 to the registry. All proposed acceptance cases are explicitly NOT RUN.
- Read local prior-document material for engine ownership, route navigation, dialog exits, drag cancellation and hover intent. The nearest overlap is hover's composition of independent state inputs; this package adds durable user pause across tab visibility and media reconstruction, a materially distinct bounded contract. The sixth scroll-smoothing topic and live duplicate/tree check were reported by the author; this reviewer did not independently retrieve that package or audit the live catalog/tree.
- Manually reviewed package text and ran bounded regex checks across all three files for common credential prefixes, private-key headers, email addresses and IPv4 literals. No findings. This is not exhaustive secret detection; no private payload or third-party code was identified in the reviewed deliverables.

## Correction closed

Requested replacement of ambiguous wording about preserving control focus while using other content. Re-read the changed text: it now preserves the pause choice while focus moves elsewhere and explicitly prohibits moving or trapping focus. No remaining required content correction identified.

## Exact limits

The HTML primary URL initially returned its header/update date, but subsequent attempts to retrieve the visibility section timed out. Its detailed visibility/rendering statement was therefore not independently re-established in this review; this source-check limitation is not a runtime finding. [HTML source](https://html.spec.whatwg.org/multipage/interaction.html#page-visibility).

No application source/lockfile inspection, installation, build, runtime, browser, integration, accessibility-conformance, performance, battery or assistive-technology testing was performed. The permission equation and lifecycle contract remain original unimplemented synthesis. Documentary PASS must not be reported as any of those tests passing. Manifest review status may be finalized by the publishing author; this reviewer did not change the manifest or publish the package.
