# Publication QA

## Independent review
A separate read-only QA worker independently re-fetched the pinned README, licence, Go module, command and Git source files; reviewed release-tag and release-commit root command; checked GitHub release and commit dates and the official Git clone manual. Important documentary claims and links passed review on 2026-11-01. README/source default disagreement and maintenance limits were retained rather than hidden. S8 was strengthened to an immutable release commit during review; sources record the actual retrieval day.

Independent review approved the three research/data drafts, subject to the final publisher scan. A failed web commit fetch was resolved using the official GitHub connector. The reviewer verified the pinned commit, not independently that it was current master. No upstream software was executed.

## Publisher checks
Live catalog and all three prior tooling reports were read before selection. Catalog contained 34 packages; topic directory contained three run directories and setup README, with no same-ID or unfinished package. This count and novelty check were performed by the publisher, not independently reconstructed by QA.

Final JSON parsing, source-ID/reference validation and pattern-based sensitive-data checks passed. Patterns cover private-key headers, common token formats, credential-bearing URLs, email addresses and private operational identifiers; manual review covers confidential context and unauthorized copied material. No candidate hits remain. This is a bounded regex/manual publication scan, NOT a Gitleaks execution or exhaustive DLP assurance.

## Approval scope and exclusions
APPROVED_DOCUMENTARY means only this task's documented source review and sanitized research publication checks. It is not Gemini approval, permission to deploy, private-data suitability or security certification. Runtime, performance, accessibility and integration tests were not performed. The proposed fixture matrix and recommendations remain untested.

Package and catalog must be committed atomically against the current branch head with conflict rejection, then remotely byte-verified. Commit identity is established by the actual Git commit, not a self-referential manifest hash.
