# Independent documentary review

Run: `20261101T153000Z-open-source-tooling`
Reviewed: 2026-11-01T15:39:00Z
Decision: **APPROVED_DOCUMENTARY** for the bounded research content. Publication receipt remains the publisher's separate verification step.

## Checks performed
- Read research.md, sources.json, repos.json and manifest.json independently.
- Re-read pinned Promptfoo README, LICENSE and package.json, and pinned DeepEval README, LICENSE.md and pyproject.toml through GitHub. Confirmed MIT / Apache-2.0, declared Node >=22.22.0 / Python >=3.9,<4.0, optional MCP dependency, pytest entry point and stated integration constraints.
- Re-read both repositories' latest-release and latest-commit API responses. Confirmed the recorded releases, source commits and dates. DeepEval release/source version divergence is correctly retained; no registry-version conclusion is drawn.
- Opened the four official documentation URLs in sources.json (S11–S14). Confirmed telemetry defaults/opt-outs and advertised external MCP integration, MCP server stdio/HTTP transports and public share_evaluation capability. Re-read DeepEval README's cloud-login logging and dotenv behavior statements. These are documentary confirmations, not observed network behavior.
- Re-read the live catalog and prior tooling research. Observed 20 catalog packages and one earlier tooling package on browser execution interfaces. Evaluation-harness selection adds a distinct question; the proposed deterministic/judge split is clearly identified as original synthesis.
- Parsed all three JSON files. Checked source count 14, unique source URLs 14, repository count 2, stable run ID and pending publication receipt. No malformed JSON or inconsistent counts found.
- Ran bounded private-data/secret-pattern checks and manually reviewed the complete draft content. Zero matching private-data/credential patterns found. This is not a comprehensive DLP guarantee.

## Findings and limits
No blocking factual corrections identified. Descriptions of compatibility and local/CI execution remain documented capabilities, not installation results. Recommendations do not claim measured superiority, security certification or unlimited commercial reuse. Source URLs and exact pinned references are retained in sources.json and repos.json.

Runtime, installation, dependency resolution, performance, accessibility, exploit, telemetry egress, MCP interoperability and model-evaluation tests were **NOT PERFORMED**. Cloud/privacy statements are vendor documentation, not independent certification. Public-source scans do not establish tool safety for confidential data.

Before publication, update review_status, scan the final manifest/QA files, preserve fresh-head catalog concurrency and verify remote bytes, original artifact commit SHA and unique catalog entries. Approval here is documentary QA only, not deployment or integration authorization.
