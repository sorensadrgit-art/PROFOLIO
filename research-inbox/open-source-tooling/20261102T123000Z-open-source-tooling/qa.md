# Independent documentary QA

Run: `20261102T123000Z-open-source-tooling`  
Reviewer: separate QA worker, not the drafting worker  
Review window: 2026-11-02 12:35–12:36 UTC  
Decision: **APPROVED_DOCUMENTARY**, subject to the publishing worker's final exact-byte scan and append-only remote verification. This is not Gemini approval, software certification or permission to install/apply changes.

## Independently checked
- Reopened all nine primary sources in sources.json: pinned README, actual MIT LICENSE, Cargo and npm source manifests; latest-release metadata plus resolved tag commit; FAQ, rewrite guide, rule-test guide and CLI reference.
- Release endpoint returned 0.51.1 with publication time 2026-11-02T05:33:13Z. The tag resolved to 5a204b1ef3facef94de125d990dddc7cf90011c0 with commit time 2026-11-02T05:29:50Z. Licence, Rust minimum, npm engine, postinstall and platform-package claims agree with pinned files.
- FAQ supports the stated semantic-analysis limits and CLI/playground parser/encoding differences. Rewrite documentation supports preview/interactive/update-all distinctions, invalid-output example and empty substitution for unmatched metavariables. Rule-test documentation supports expected match/nonmatch fixtures, diagnostic snapshots and baseline updates. CLI documentation supports configuration discovery, JSON output and stderr inspection diagnostics.
- The baseline-is-not-independent-proof statement and three-gate workflow are appropriately original synthesis, not attributed as built-in guarantees.
- Live catalog still contained 69 packages, including eight earlier tooling entries and no current run ID. Their summaries cover browser automation, evaluation harnesses, MCP diagnostics, secret scanning, ripgrep, jq, ShellCheck and Hyperfine. Structural codemod semantics and snapshot ownership add a distinct useful question. The drafting worker separately inspected the full earlier reports and topic listing.
- No unsupported claim requiring a substantive correction was found. Source metadata was clarified by the drafting worker to include release/commit retrieval endpoints; this does not change the findings.

## Validation and publication hygiene
Python parsed all three JSON artifacts. Run identifiers, nine unique source IDs, source count and repository source references agree. Manual content review found original prose with limited API names, no copied implementation or assets, and no private account/host/session records.

A bounded local regex scan of the four draft artifacts checked common private-key markers, GitHub/AWS credential patterns, credential assignments, email addresses and IPv4 literals: **zero matches**. This is limited pattern checking plus manual review, not a complete secret detector or DLP audit. The final scan must include this report, finalized manifest and catalog addition after all edits. Final JSON validation must be repeated after status updates. The pre-final manifest intentionally remains pending until the publishing worker records this decision.

## Remaining limitations
No ast-grep install or execution, codemod, parser comparison, compile/type-check, behavioral, performance, security, accessibility or MCP/API interoperability test was run. npm registry metadata remains unavailable; source manifest and release observations do not establish package integrity. Living documentation is not pinned to the binary release. No guarantee of behavior preservation, complete coverage or target-environment suitability is made.
