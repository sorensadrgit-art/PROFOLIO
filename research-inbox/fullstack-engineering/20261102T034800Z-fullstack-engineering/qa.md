# Independent documentary QA

**Verdict: PASS for bounded publication as original, untested research.**

Reviewed independently on 2026-11-02, approximately 03:47–03:48 UTC. Scope: `research.md`, `sources.json`, and `manifest.json`; only this QA document was written by the reviewer. No publication or application execution was performed.

## Evidence and checks

- Independently opened all five primary URLs rather than relying only on the author's summaries. [S1](https://vite.dev/guide/env-and-mode) supports build replacement, public string values, environment precedence and the mode/`NODE_ENV` distinction.
- [S2](https://vite.dev/config/shared-options#envprefix) supports configurable prefixes and rejection of the empty prefix. Its `define` and `publicDir` sections support static replacement and untransformed asset copying. Prefix checks alone cannot establish that output contains no private values; this conclusion is appropriately presented as synthesis.
- [S3](https://vite.dev/guide/build) supports static hosting, build-time base adjustment, removed-old-chunk failures and the reference to Rolldown options. [S4](https://vite.dev/guide/backend-integration) supports the asset/dependency manifest and backend HTML integration. The runtime configuration contracts are clearly original proposals, not claimed native Vite functionality.
- Independently read the actual [repository LICENSE](https://github.com/vitejs/vite/blob/main/LICENSE): the MIT identification, notice obligation and warranty disclaimer are accurately summarized. No dependency licensing audit is claimed.
- The core unchanged-static-artifact conclusion is a sound inference from build substitution, correctly labeled as inference rather than a runtime result. It does not assert that a separate runtime configuration document or server-rendered HTML cannot change.
- Both JSON files parsed successfully. Five unique source IDs S1–S5 match the manifest count and research links. Null release/commit metadata honestly preserves unknowns. Draft status and null publication commit describe the prepublication state, not a completed publication.
- Manual review and a bounded pattern scan of the three files found no apparent personal data, private local paths, credential-bearing URLs, private-key blocks, AWS access-key patterns, GitHub-token patterns or bearer-token values. This is not exhaustive secret detection or a security audit.
- Acceptance checks are explicitly NOT RUN. No browser, build, runtime, deployment, security, accessibility, performance or integration result is claimed. JSON parsing and document inspection are QA checks, not application tests.

## Corrections and limitations

No required technical corrections identified. Publication metadata may be updated by the publisher after the QA gate; do not recast this review as runtime validation.

The claim that the live catalog, branch tree and five prior packages were inspected is the author's process statement. Those prior artifacts and the novelty comparison were not independently re-audited in this bounded review. Author retrieval timestamps were not independently reconstructed; reviewer access occurred during the interval above. All primary pages are mutable and no immutable source revision, installed Vite version, registry artifact, Node requirement, framework compatibility or deployment behavior was verified. The review approves the documentary content only, not an implementation, security posture or deployment.

## Reviewed content fingerprints

SHA-256 at review time (before any subsequent publication metadata changes):

- `research.md`: `0cbaed75cf1fa9c798c3a68925f1189180915ddd8d826c0c3a24b5611b763af2`
- `sources.json`: `8972e7a91c2e642948805acf2ec6088396dd48a33bda6b41b3bde0a8a6dfe3b7`
- `manifest.json`: `608c57c78cb1c0f1bc8f339f2628495d7eb803ad376c18202646808c512684e3`
