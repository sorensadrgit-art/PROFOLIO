# Independent documentary QA

Outcome: APPROVED_DOCUMENTARY

Reviewed 2026-11-06 UTC. Scope: the corrected research report and source list for the entity-resolution design. All eight cited primary sources were independently reopened and the corrected files reviewed. No further factual correction is required.

- S1 supports exact-pattern MERGE, constraints for concurrency uniqueness, null prohibition and the special bound-endpoint relationship locking guarantee.
- S2 supports composite uniqueness applying only to records containing every constrained property, plus Enterprise key and property-existence constraints.
- S3 limits elementId mapping guarantees to one transaction and recommends application-generated IDs.
- S4 requires idempotent retryable transaction callbacks. Keeping external effects outside callbacks is correctly labeled application synthesis.
- S5 supports Maven 3.9.11+, Bash/Make, standalone/managed availability and Community versus commercial Enterprise distinctions.
- S6 supports manifest version 2026.10.0, default VM target 21, Lucene 10.5.1, Netty 4.2.19.Final and Jackson 2.22.3. Full manifest properties were verified through the GitHub contents API because rendered XML omitted some values. Target 21 is overridable by a profile.
- S7 contains GPLv3; S6 labels the licence GPL-3.0-or-later. The report appropriately avoids granting blanket reuse rights.
- S8 confirms revision 2994b43b44f35bf6520bd2667ab181bb76a45585 and timestamp 2026-10-20T08:27:45Z.

The release caveat is corrected: GitHub's latest-release endpoint returned the old alpha-tagged release 3.2.0-alpha08, published 2017-04-11T08:47:47Z, with prerelease false. Both report and metadata retain the distinction and do not infer the current product release.

The proposed separation of mentions, entities and evidence-bearing decisions is coherent as an unimplemented design. The corrected immutable receipt key includes extraction version and separates logical source occurrence from alternative extractions. Its new acceptance case is explicitly unexecuted. Constraints are not presented as semantic identity or authorization checks.

All runtime, database, concurrency, integration, performance, accessibility and security tests remain NOT RUN. This documentary approval does not establish deployment readiness, benchmark results, external-model approval, repository duplicate checks or completion of the separate publication and sensitive-data checks. Retrieval may use cached content; rolling documentation is not pinned to the repository revision.

## Final package checks

The publisher parsed the manifest, sources and candidate catalog, checked all eight unique source IDs and URLs, verified four-file coverage and compared the run against the live 105-entry catalog and 14 published topic packages. No same-run or identical research package was found. The original unpublished run ID is retained. The candidate catalog adds one entry to each of its existing global and topic indexes without changing existing metadata or entries.

A bounded pattern scan and manual review of all four package files and the candidate catalog found no credentials, private account or host details, private paths or confidential records. Patterns covered common service tokens, private keys, credential assignments, private IPv4 addresses, local paths and email addresses. No unresolved finding remains. This is not a comprehensive security guarantee.

No existing package file is changed. Publication is limited to this four-file package and its catalog entry, using the observed branch tree and expected-head verification. Runtime, database/query, concurrency, integration, performance, accessibility, security and deployment tests were not performed.
