# Independent documentary QA

**Verdict: APPROVED_DOCUMENTARY**  
Reviewer: roots_qa (independent child reviewer). Reviewed 2026-11-02, approximately 22:25 UTC.

## Scope and evidence

Read research.md, sources.json and manifest.json in full. Independently fetched all seven primary sources; GitHub blob retrieval failed for package.json and LICENSE, so the exact same commit-pinned raw.githubusercontent.com files were fetched instead. Verified:

- S1: file URI requirement, roots capability, list/change messages, client MUST duties and server SHOULD boundary/path duties.
- S2–S3: roots are coordination rather than OS confinement; deprecation in 2026-07-28; migration alternatives; eligibility for removal on/after 2027-07-28 is not guaranteed removal.
- S4: CLI configuration across protocol eras; legacy roots replacement/update waiting; documented symlink rejection and hard-link write caveat. These remain documentation assertions, not tested security guarantees.
- S5: pinned package version 2.1.0, Node >=24, SDK ^2.3.1 and listed dependency/tooling families.
- S6: pinned license transition language supports the report's qualified treatment of Apache-2.0, retained MIT contributions and CC-BY-4.0 non-specification documentation. No contribution-level legal audit.
- S7: inspected startup source corroborates original/resolved paths, filtering and exit when every supplied directory is unusable.

The pinned commit page also resolves. Its extracted page did not expose a timestamp; exact commit date and the author's historical HEAD observation were not independently verified. Source retrieval timestamps are author-reported, not reconstructed by this review. npm publication metadata remains unknown as stated.

## Artifact checks

Both JSON files parse. Seven unique source IDs match manifest source_count. Run identifiers and source mappings are consistent by inspection. A bounded heuristic scan for private-key headers, common GitHub/AWS/OpenAI credentials and assigned bearer/API/password tokens returned zero matches; manual reading found no apparent private user data or secret. This is not an exhaustive secret audit.

The scope-admission pattern, alternatives and future application are marked original proposals. Proposed acceptance cases are explicitly unexecuted. No unsupported production-readiness or security-confinement guarantee found.

Compared against the 12 prior subject categories supplied in the report, filesystem scope enforcement plus roots migration is a distinct primary subject; generic receipt/memory vocabulary overlaps without duplicating those primary questions. This reviewer did not independently reread the historical catalog/packages; parent reports live intake review. No independent confirmation of historical catalog counts or same-run absence is implied.

## Limits

No install, remote mutation, server execution or runtime, integration, security, race, performance or accessibility testing. Approval is documentary only, not Gemini approval or deployment authorization. Parent should finalize manifest QA/JSON/sensitive-scan status before publication; the reviewed manifest intentionally remained pending during review.
