# Independent documentary QA

Run: 20261102T203600Z-webgl-3d. Evaluated: 2026-11-02T20:45:27Z.

Outcome: **APPROVED_DOCUMENTARY**, after one precision correction. This is independent review of the research package, not Gemini approval, deployment permission or runtime certification.

## Independently retrieved evidence

The QA worker independently fetched S1, S2, S5 and S6 through the GitHub connector at pinned commit e02b502efd394f5e982103159b9815b2ed147aad; fetched S7 commit metadata; and opened both official API pages S3/S4 on 2026-11-02. Source URLs are listed in sources.json and research.md. Sources were treated as evidence, not instructions.

- S1 AnimationAction blob a1a8c3c1bb39bfb40b37b188c49a20af96e5ffde: _updateTime dispatches finished before returning to _update. Effective-weight calculation follows; interpolation and accumulation are guarded by positive effective weight. Clamp switches pause versus enabled state; predicates do not test weight. stop deactivates then resets, reset does not generally restore weight or timeScale, and play activates without reset. No finished dispatch occurs in the inspected stop/reset paths.
- S2 AnimationMixer blob 60272b79c8c000b9f156adea6a4274c7aeeac595: update advances active actions before applying active property bindings. The report correctly treats final-pose callback implications as a source-order inference rather than a measured trace.
- S3 confirms clamp concerns actual last-loop completion, not interruption. S4 confirms stop/deactivate requirements before uncaching actions, clips or roots.
- S5 package blob 2a7c71bfba5113e4983f26b381a270b72d4b926f: version 0.187.0, import/require entry points and no runtime dependencies field. S7 confirms commit identity and 2026-11-02T16:23:21Z date. Neither establishes the installed application version or an npm release date.
- S6 licence blob 8ada2a5f982916b0ba4b7a0aa7de347587e745d7: actual MIT text, notice-retention requirement and warranty disclaimer inspected. Third-party assets are not licensed by inference.

## Correction

Qualified finding 1 in research.md: interpolation/accumulation occurs only when effective weight is positive. This avoids implying that an unclamped completion must contribute a terminal pose. Original application protocol and proposed acceptance exercise remain explicitly untested synthesis.

## Duplicate and structural checks

Independently fetched live research-inbox/catalog.json (blob d245f030bed45e63c52d2699d87780ddb6e2e4de). It contains fourteen WebGL packages and no entry for this run. Reviewed those titles/summaries and fetched the nearest prior independent-character report, 20261101T143600Z-webgl-3d. That report covers restart/resume/clamping and ownership, not the finished-event versus binding-application ordering examined here. No duplicate detected within this bounded check. The researcher separately checked topic directories; QA did not independently repeat the full tree inventory. Unrelated catalog issues were not changed.

sources.json and manifest.json parse successfully. Seven unique source IDs and the explicit four-file publication list are coherent. This is not a discovery shortlist; repos.json is not required.

## Sensitive-data review

Bounded pattern scan and manual read of the four intended publication files found no credential tokens, private keys, credential assignments, email addresses or private infrastructure details. Scanned categories included common GitHub/API tokens, private-key headers, secret assignments, private-network and host/path patterns. This is not an exhaustive secret-detection guarantee. Local upstream source scratch files are excluded from publication.

## Limitations

No installation, browser, renderer, runtime, integration, animation, visual, accessibility, performance or wrapper tests were performed. Sources are pinned where possible; rolling documentation can change. QA did not publish or verify a remote commit. The publisher must recheck concurrency, exact remote files and unique catalog insertion after its atomic commit. Any later material content changes require renewed review.
