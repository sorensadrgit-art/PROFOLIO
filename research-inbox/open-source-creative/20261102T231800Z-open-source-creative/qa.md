# Independent documentary QA — Tweakpane

Run: `20261102T231800Z-open-source-creative`
Reviewed: 2026-11-02T23:24:52Z
Reviewer: separate QA worker (`qa_tweakpane`)
Outcome: **APPROVED_DOCUMENTARY**

## Checks actually performed
- Read research.md, sources.json, repos.json and the draft manifest.json independently.
- Re-fetched Tweakpane 4.0.5 README, package manifest and actual MIT LICENSE.txt. Verified ESM, TypeScript guidance, absence of a runtime dependencies field and licence obligations.
- Re-fetched the tagged container-blade, binding and input-binding implementation files. Confirmed index-based existing-child import, exported binding key/value and injected input value followed by fetch. The proposed application-schema separation is correctly labelled original, untested synthesis.
- Opened official Misc and Getting Started documentation. Confirmed state methods, manual refresh, disposal and additional TypeScript package guidance.
- Independently queried latest GitHub release and default-branch commit: release 4.0.5 published 2024-11-03T16:37:01Z; commit 50a18c3dba67cb78431d7248dbe4d5a76336885a dated 2026-03-15T08:28:49Z. These are not npm-latest claims.
- Parsed all three package JSON documents successfully with Python's JSON parser. Source IDs and URLs are distinct; required research and discovery files are present, with manifest finalization pending this verdict.
- Read the current remote catalog: 94 global packages, 12 creative entries, no same run ID and no Tweakpane entry. The author performed the deeper existing-report intake; this reviewer independently checked catalog titles rather than re-reading all 12 reports.
- Performed a bounded local regex scan plus manual reading for private keys, common GitHub/AWS credential formats, credential assignments, host identifiers, private account identifiers and IPv4 literals. No sensitive-data findings in the new research files. This is not an exhaustive secret-detection guarantee.

## Corrections and release conditions
No substantive claim corrections required. This QA report replaces an unrelated stale local QA file; that stale report is not evidence for this run. Author must finalize the manifest, rerun JSON/secret checks across the five final package files and publish only via the required expected-head compare-and-swap with remote content verification.

## Limits
No package installation, build, demo execution, runtime, performance, accessibility, security or integration test was performed. npm registry retrieval failure and unresolved matching TypeScript core version remain explicit limitations. Live documentation is mutable; implementation evidence is pinned to the 4.0.5 tag. Approval covers this documentary research package only, not deployment or Gemini approval.
