# Independent documentary QA

Run: `20261102T113600Z-webgl-3d`

Reviewer: separate QA worker, `qa_transparency`.
Review date: 2026-11-02, approximately 11:40–11:41 UTC.
Outcome: **APPROVED_DOCUMENTARY** for this bounded research package; not deployment or Gemini approval.

## Checks actually completed

- Independently reopened all eight pinned upstream files through the GitHub connector. Confirmed material classification/defaults, alpha hashing and alpha testing descriptions, MSAA-dependent alpha-to-coverage documentation, render-item list assignment/comparator, separate opaque/transmissive/transparent passes, conditional double-sided two-pass draw path, physical-transmission opacity requirement, and transmission render-target presence.
- Independently checked Object3D's separate opaque/transparent order documentation; no claim that renderOrder repairs intersecting geometry is made.
- Confirmed MIT licence conditions and warranty disclaimer, package version 0.187.0, module/CommonJS exports and absence of a runtime dependencies block. Verified the example source exists without launching it. Confirmed upstream commit timestamp through the commit API.
- Findings are distinct from original recommendations. Runtime visual behavior, performance, accessibility, application-version compatibility, R3F and WebGPU equivalence are explicitly untested or excluded.
- Read the live 67-package catalog: eleven prior WebGL packages; stable run ID absent. Their titles/scopes support the narrow transparency-classification novelty. Rechecked the recursive topic directory listing; no additional unindexed topic package or same-slot artifact was present. Broad prior packages may mention transparency; this is a focused additive note, not a claim of discovering transparency itself.
- Parsed sources.json and manifest.json with Python JSON parsing successfully. Confirmed eight unique source IDs and pinned source URLs in manual review. Manifest draft/PENDING values are a prepublication checkpoint; publisher must finalize them consistently with this review.
- Ran bounded regex scans across research.md, sources.json and manifest.json for private-key markers, GitHub tokens, AWS access-key IDs, bearer values, credential assignments, email addresses and IPv4 literals: no matches. Manual reading found no private account/host details, credentials or confidential operational material. The scan is not an exhaustive secret detector.

## Corrections and limitations

No blocking claim corrections required. The unavailable former manual URL is transparently recorded as a failed lookup and is not used as evidence; QA did not rely on that URL. Recommendations about DOM labels, asset-manifest fields, art simplification and testing are appropriately identified as original proposals.

No runtime, browser, GPU benchmark, device, accessibility, install or integration test was performed. Review establishes source traceability and bounded documentary consistency only. Publication CAS, final byte verification and final catalog consistency remain the publishing worker's responsibility. Re-scan the final four-file package after manifest finalization.
