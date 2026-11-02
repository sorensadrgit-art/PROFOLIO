# Independent documentary QA

- Run: 20261102T143600Z-webgl-3d
- Review date: 2026-11-02
- Review window: 14:53–14:54 UTC
- Reviewer: separate QA worker, independent of the drafting worker
- Outcome: APPROVED_DOCUMENTARY

## Evidence checked

All six listed sources were independently reopened. The pinned Three.js implementation was fetched directly from the maintainer repository at commit 104aa0bc3cd4894da6349048948d92ccd5332bfa, and its commit metadata independently confirms 2026-11-02T13:16:24Z.

- WebGLShadowMap.js lines 94–95: disabled shadow pass and global update gate.
- Line 170: separate per-light update gate.
- Lines 289–295: six cube faces for cube shadow targets, with point-light target allocation inspected in the same file.
- Lines 372 and 378: per-light and renderer dirty-flag resets after processing.
- Line 528: VSM includes receiving objects in shadow selection.
- LightShadow documentation: explicit next-render refresh, automatic-update setting and map-size computation trade-off.
- React Three Fiber scaling guide: imperative mutation needs invalidation in demand mode; invalidate schedules rather than immediately renders.
- WebGLRenderer documentation: WebGL2 scope and WebGL1 removal from r163.
- Pinned package.json and LICENSE: declared 0.187.0 dev version, no runtime dependencies field, MIT notice-preservation and disclaimer. No release-status inference made.

## Minor clarity corrections verified

1. State that shadow rendering must already be enabled and the relevant light configured to cast shadows. Dirty flags cannot enable a disabled pass.
2. Replace “reference demonstrations” with “API reference”; no rendered demonstration was exercised.

Both changes were independently reread and verified at 14:54 UTC. The draft manifest also parses as valid JSON and correctly leaves runtime tests unperformed. The publication worker will finalize the documentary status and rerun the complete final-file scan.

No unsupported substantive claim found in the two-gate argument. Proposed ownership, invalidation categories, alternatives and acceptance fixture are explicitly original and untested. No performance benefit is quantified or certified.

## Local checks

- sources.json parsed successfully; all six source IDs unique.
- Bounded scan of research.md and sources.json found no private-key headers, common GitHub/AWS credential patterns, quoted credential assignments or known private operational identifiers.
- Manual content review found no credentials, private operational logs, account records, copied repositories or protected demo assets.
- This is a bounded pattern and content review, not an exhaustive guarantee. The publication worker must rerun validation and scan over the final manifest, QA and catalog addition.

## Limits

No runtime, integration, visual, accessibility, mobile, performance or installation test was performed. Live repository duplicate checks were performed by the drafting worker, not independently repeated here. Approval after the two corrections means documentary package review only, not Gemini approval or deployment authorization. Rolling documentation and pinned dev source do not establish a tested release combination.
