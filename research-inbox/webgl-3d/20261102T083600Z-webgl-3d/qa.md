# Independent prepublication QA

Evaluated: 2026-11-02. Verdict: **PASS for documentary publication**, not runtime or integration approval.

## Checks actually performed

- Read the complete research draft, source registry and manifest; parsed both JSON files and checked matching run IDs, seven unique source IDs and source-count consistency.
- Reopened both official documentation pages. Object3D documents the directional/spot depth-material hook, point-light distance-material hook, WebGLRenderer-only scope and default frustum culling; BufferGeometry documents application-owned boxes and sphere recomputation after vertex changes.
- Independently fetched BufferGeometry.js, Frustum.js, Mesh.js, package.json and LICENSE from official Three.js commit `8086b3fda07a70bffad7ee39265c9617994ec482` through the GitHub connector. The web fetch of Mesh.js failed; the connector retrieval succeeded.
- Verified CPU position/morph reads in sphere construction, object/geometry sphere world transformation in frustum checks, and Mesh raycasting's sphere/optional-box broad phase followed by triangles using CPU position/morph data. None of those inspected methods executes arbitrary vertex GLSL. The draft correctly limits this to ordinary Mesh behavior rather than generalizing to specialized overrides.
- Confirmed package version 0.187.0, absent runtime dependencies field, existing development dependencies, and MIT notice/warranty terms. This does not establish an npm release or deployment compatibility.
- Reopened the earlier `20261101T173600Z-webgl-3d` research package. Its instance-slot identity/matrix/bounds concern is distinct from this draft's arbitrary GPU displacement contract. The three-contract synthesis, bounded local displacement argument and proposed gallery/sculpting applications add meaningful analysis; no copied implementation or substantial quoted passage was found in manual review.
- Ran bounded regular-expression scans on research.md, sources.json and manifest.json for private-key headers, common provider tokens, quoted credential assignments, email addresses, private/loopback network addresses and local home/workspace paths: zero matches. Also manually reviewed those files for private operational details. This is not exhaustive secret detection.

## Limits and disposition

No corrections to substantive claims were required. Local-radius inflation follows the triangle inequality only under the stated local-space displacement bound; it does not fix triangle picking. The draft explicitly identifies shadow synchronization, proxy semantics and acceptance checks as untested design proposals.

The author-reported read of all ten earlier packages and the commit timestamp were not independently reverified in this QA; only the relevant earlier instance-selection package was reopened. Moving documentation may differ from pinned source or deployed releases. No installations, browser/GPU runs, benchmarks, accessibility, integration or deployment tests were performed; no remote state was changed. Publication status and review-status promotion remain the publishing agent's responsibility.
