# Independent documentary QA

Run: `20261103T020000Z-website-design`  
Reviewed: 2026-11-03, approximately 02:08 UTC  
Outcome: **APPROVED_DOCUMENTARY** after one metadata correction. Reviewer: separate QA worker, not research author.

## Evidence review
Independently reopened all five linked primary sources. Confirmed [APG's landmark guidance](https://www.w3.org/WAI/ARIA/apg/patterns/landmarks/) and [region practice](https://www.w3.org/WAI/ARIA/apg/practices/landmark-regions/): implicit main/nav roles, named sections, distinct repeated-role labels with an identical-content exception, main/document caveats, and the advisory rather than normative landmark-count heuristic. Confirmed [Bypass Blocks](https://www.w3.org/WAI/WCAG22/Understanding/bypass-blocks.html) Level A scope and its alternative techniques. Confirmed [G1](https://www.w3.org/WAI/WCAG22/Techniques/general/G1) focuses the main content, permits focus-only visibility, and is not a compulsory implementation.

Followed the APG footer licence link independently to the [W3C Software and Document License 2023](https://www.w3.org/copyright/software-license-2023/). The limited rights summary agrees with the licence; it is not legal advice or permission for unrelated assets. Confirmed observed S3 and S4 update dates. Requested correction of S5's 2023-01-01 value from page update to effective date; latest sources.json now records effective_date separately and page_updated_at as null. Unknown releases and commits remain null. Retrieval timestamps are author provenance receipts, not independently reconstructed timestamps.

Recommendations, future applications and acceptance exercises are labelled original and untested. No production implementation, runtime guarantee or accessibility certification is asserted. No substantial copied code or source text was found. Source content was treated as evidence, not instructions.

## Novelty and package checks
Inspected the supplied 15-report prior-topic snapshot for overlap. The accordion report already cautions against excess panel landmarks and the breadcrumb report uses navigation landmarks; the new report explicitly acknowledges related prior work and adds page-wide structure plus skip-link focus/target verification. No prior report in that snapshot addresses this combined question. Remote catalog/head/tree completeness and append-only publication remain the publisher's checks; this reviewer did not independently retrieve the catalog.

Both JSON files parse. Five source IDs match the report and manifest; run IDs and declared package filenames agree. Manifest status is pending at this review snapshot and may be finalized to this documentary outcome by the publisher. No repos.json is required for this non-shortlist reference.

A bounded pattern scan of research.md, sources.json and manifest.json checked private-key headers, common provider-token formats, credential assignments, email addresses and local/private HTTP hosts: zero matches. Manual inspection found no apparent credentials, confidential records or private operational details. This is not exhaustive secret detection, a repository-wide audit or security testing. The publisher should scan the final four-file package and catalog delta before writing.

Reviewed input SHA-256:
- research.md: `9578e124c770bb757d03817d1ee0fe7cb5541a1ed5ad5cb81314d711b6ff1402`
- sources.json: `27b3152f90963b6e7a78cebfecf12bab7d49c3658707a776c24fda6b1052e1de`
- manifest.json (pending review status): `7dfe8ba3d49c7b4007af64e15321cd790b5b94b89145da80656f74dd9530ca88`

**NOT RUN:** browser, keyboard/assistive-technology, runtime, accessibility-conformance, usability, integration, performance or deployment tests. Approval applies to bounded documentary quality only, not Gemini approval or permission to deploy.
