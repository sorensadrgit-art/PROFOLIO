# Independent documentary QA

Run: 20261103T021800Z-open-source-creative  
Reviewed: 2026-11-03T02:23:52Z  
Outcome: **APPROVED_DOCUMENTARY** after one correction.

## Independent source checks
The separate QA worker reopened the pinned README, package manifest, actual LICENSE, font implementation and stylesheet implementation, plus GitHub latest release, release-tag commit and latest default-branch commit endpoints. All eight source links returned the expected primary materials.

Confirmed: 0.63.2 release published 2026-10-20T09:08:32Z; tag/default head 074f0b38ed0f5d8fa768f709c0bb072b2d6c99e1 committed 2026-10-20T09:06:28Z; package version placeholder is not the release number. Confirmed documented WOFF2/Grid support, variable-font default-instance handling, stateless rendering limitations, default outlined text, supplied-font fallback, stylesheet matching limits and MPL-2.0 obligations summarized from sections 3.1–3.4. No npm-latest claim is made.

## Correction applied and rechecked
The draft overgeneralized stylesheet error dropping. The tagged README explicitly distinguishes values containing var(): they are checked at computed-value time and invalid values can throw like inline styles. The final research paragraph now includes this exception. The QA worker reread the corrected paragraph before approval.

## Novelty and structural checks
Independently reread the live catalog: 101 packages, 13 global creative entries, no Satori/generated-card entry. Existing topics concern other component/effect contracts. Intake full-report/tree review is the research worker's recorded evidence; QA checked catalog scope, not every prior report again. The unrelated COBE topic-index omission is not repaired.

Three JSON files parsed successfully with Python's JSON parser. Required run/topic/source metadata is consistent. The pending manifest status is expected to be changed to documentary-approved before publication by the publishing worker. Remote publication, catalog append-only checks and commit verification remain that worker's responsibility.

## Bounded sensitive-data check
Reviewed the four draft artifact files manually and ran a local regex scan for private-key blocks, GitHub token forms, AWS access-key IDs, credential assignments, private host patterns and private account addresses. No findings. This is a bounded artifact scan, not a comprehensive secret-detector or dependency-security audit; upstream source text and repository history were not scanned. The QA report contains no credentials or private operational details.

## Limits
No installation, build, rendering, browser interaction, runtime, performance, accessibility, security or integration tests were performed. Demos were not operated. npm metadata retrieval failure and unverified tarball/source equivalence remain explicit. Approval means this documented source/structure/privacy review only, not Gemini approval, legal advice, deployment permission or production certification.
