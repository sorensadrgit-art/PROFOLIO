# Independent documentary QA

- Outcome: **APPROVED_DOCUMENTARY**
- Reviewed: 2026-11-03 UTC, approximately 01:32 UTC.
- Reviewer: independent QA sub-agent, not the research author.
- Scope: `research.md`, `sources.json`, `manifest.json` in run `20261103T012400Z-agents-memory`; this review record. No publication or deployment performed by this reviewer.

## Evidence checks

Independently reopened all six primary-source subjects. Confirmed S1 query/document encoder distinctions and S2 vector configuration/normalization statements. Confirmed S3 version prerequisites, publisher-tested version, original-data requirement, Cloud Inference context, both update/delete caveats, and old-vector retention/removal semantics directly against the [migration tutorial](https://qdrant.tech/documentation/tutorials-operations/embedding-model-migration/). These are documentation claims, not local test results.

Confirmed the exact release timestamp through the [GitHub release API](https://api.github.com/repos/qdrant/qdrant/releases/tags/v1.20.0). Independently followed the annotated tag through [tag reference](https://api.github.com/repos/qdrant/qdrant/git/ref/tags/v1.20.0) and [tag object](https://api.github.com/repos/qdrant/qdrant/git/tags/71fdf974d7bf4bd284a4b557ed5d4dea469f5aed) to the reported commit; the [commit record](https://api.github.com/repos/qdrant/qdrant/commits/222b2e44ab1c3f17e8246396ead6558e45041df7) confirms the committer timestamp. Web-tool API opens failed; read-only Python HTTPS retrieval succeeded. Independently inspected pinned [Cargo metadata](https://raw.githubusercontent.com/qdrant/qdrant/222b2e44ab1c3f17e8246396ead6558e45041df7/Cargo.toml) and [licence](https://raw.githubusercontent.com/qdrant/qdrant/222b2e44ab1c3f17e8246396ead6558e45041df7/LICENSE). Reported metadata and limited licence summary agree with these records; this is not legal advice.

The generation contract and acceptance cases are clearly labelled original proposals. No unsupported runtime, quality, performance or availability guarantee was found. Source-dependent prose is concise paraphrase, with no substantial copied source code or documentation. External agent-oriented instructions were treated as untrusted content, not executed.

## Package checks and boundaries

Both JSON files parse; six source entries agree with the manifest and report citations. The manifest is intentionally pending review at this snapshot; publication-time status updates remain the publisher's responsibility. Prior catalog/13-report inspection and duplicate-check statements are author process attestations, not independently reproduced in this bounded review.

Read all three input files and performed a bounded pattern scan for private-key headers, common provider token prefixes, long credential assignments, email addresses, and local/private HTTP host patterns: zero hits. Manual inspection found no apparent personal data, secrets, private operational endpoints, or unauthorized execution instructions. Public source links and commit hashes are expected. This is not exhaustive secret detection, repository scanning, dependency auditing, or a security test; unrelated files and infrastructure were not scanned.

Reviewed input SHA-256:

- research.md: `f35bcc8fc3582a9f29243907dc461d52d9118b12daade33369b7e58f430d8228`
- sources.json: `de7f299b008ad803e70ad10b5de1de9341927bb2cf24bdf8b4cee630cb98e4d5`
- manifest.json: `17b706326eced52d63de707548bf1946d19f254f807fe4029042b6c3618a3aef`

No required research corrections identified. **NOT RUN:** model inference, migration, runtime/build, retrieval evaluation, performance, accessibility, integration, failover/rollback, or security tests. Approval covers documentary quality only; it conveys no deployment, external-connection, retention/deletion, or Gemini approval.
