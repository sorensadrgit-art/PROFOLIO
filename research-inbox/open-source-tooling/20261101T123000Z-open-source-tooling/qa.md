# Independent documentary QA

**Outcome: APPROVED_DOCUMENTARY** — 2026-11-01, reviewed 12:32–12:34 UTC. This is not a runtime, integration, security or accessibility pass.

## Review performed

- Read research.md, sources.json and repos.json independently, plus the draft manifest.
- Independently fetched all 14 listed public sources through the GitHub connector, including commit-pinned LICENSE files, READMEs, metadata and the release-tagged Playwright Test package. Checked GitHub latest-release endpoints separately; tags and publication timestamps match the report.
- Verified Apache-2.0 text in both Microsoft repositories and MIT text in Browser Use at the recorded commits. This does not audit dependency licences or confer website/content permissions.
- Verified Playwright current source 1.65.0-next versus stable Test 1.64.1 with Node >=20; MCP wrapper 0.0.84 declares Node >=18 and pins both Playwright packages to 1.65.0-alpha-2026-10-30. The report appropriately does not infer a supported deployment matrix from the wrapper minimum.
- Verified Browser Use pyproject version 0.13.14, Python >=3.11,<4.0, cdp-use 1.4.5, mcp 2.1.1 and pydantic 2.13.5. Independently confirmed server.json advertises 0.13.11 for both server and package, stdio --cli-mcp and hosted streamable HTTP. Version discrepancy is accurately retained; interoperability remains untested.
- Verified recorded commit timestamps against all three GitHub commit endpoints.
- Checked the MCP README warnings about non-security-boundary status, origin filters/redirects, file guardrails and shared persistent-profile conflicts. Checked Browser Use README warnings about saved snapshots/results retaining sensitive data and replay executing earlier actions before failure.
- Independently read the live [catalog](https://github.com/sorensadrgit-art/PROFOLIO/blob/research-data/research-inbox/catalog.json), blob SHA 628007a0b14669e9e8704fb5dcb90bf1e362fb25. It contained seven packages, no open-source-tooling package and no matching run ID. Its listed questions concern design, WebGL and motion, not selection among browser-control layers. Novelty passes at this observation; publication must still merge against the latest catalog.
- Parsed sources.json, repos.json and manifest.json successfully. Confirmed 14 unique source IDs, three repositories and matching manifest counts; every runtime_tested flag is false.
- Manually reviewed publication text for private user/account/host information and scanned the three reviewed research artifacts for common token/private-key/home-path/IP patterns: no matches. Public upstream identifiers and commit hashes are intentional. This is not an exhaustive secret scanner.

## Evidence boundaries and remaining publication checks

Recommendations are clearly synthesis; source capabilities are not represented as measured results. GitHub releases are distinguished from independently verified npm/PyPI publication. No installation, deployment, runtime benchmark, browser task, exploit test or remote write was performed by this reviewer. No blocking documentary defect was found.

Publisher must finalize the draft manifest QA status, scan the final publication bundle, preserve concurrent catalog entries, and verify remote contents after publishing. These publication actions were not part of this independent local review.

## Reviewed artifact SHA-256

- research.md: b2c2ac03b1b4133f2af669073b5cc6f2da0c387a6195f3c7b9903fbe58890871
- sources.json: af4b6b2510847afe11033ea8dc562c1b1f9af380e27a01ad2af24491dd8910ba
- repos.json: 3ddb15b674fc47051d3513c705c9b9ecb8d03cc5f1eff8bc626b034df3ac1dd6
