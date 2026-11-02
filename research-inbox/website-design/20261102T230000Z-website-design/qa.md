# Independent documentary QA

- Run: 20261102T230000Z-website-design
- Reviewer: independent_qa (separate review agent)
- Review started: 2026-11-02T23:02:45Z
- Primary-source rechecks: 2026-11-02T23:02:52Z–2026-11-02T23:03:13Z
- Corrected-file recheck: 2026-11-02T23:03:19Z
- Decision recorded: 2026-11-02T23:03:22Z
- Verdict: **APPROVED_DOCUMENTARY**

## Scope and evidence
Reviewed research.md, sources.json and manifest.json locally. Approval covers bounded documentary accuracy and publication hygiene, not an implemented component or deployment. Manifest was draft/PENDING during review; publisher may set published/APPROVED_DOCUMENTARY and record publication metadata after this decision.

Independently fetched all five source URLs in sources.json using the web retrieval tool:
- [S1 APG breadcrumb](https://www.w3.org/WAI/ARIA/apg/patterns/breadcrumb/): hierarchy, labelled navigation landmark and linked/non-linked current-page distinction verified. Footer licence link followed and resolved to the 2023 Software and Document licence.
- [S2 GOV.UK breadcrumbs](https://design-system.service.gov.uk/components/breadcrumbs/): multi-level use, flat/linear exclusions, parent-ending trail, examples and July 2024 v5.4.1 history entry verified.
- [S3 GOV.UK back link](https://design-system.service.gov.uk/components/back-link/): multi-page transaction scope, non-combination rule, required prior-state restoration and clearer complex-journey wording verified. December 2025 is a guidance entry, not a package release.
- [S4 Understanding Location](https://www.w3.org/WAI/WCAG22/Understanding/location.html): Level AAA, deep-entry orientation and alternative techniques verified. Page footer displays Updated 16 September 2025.
- [S5 W3C licence](https://www.w3.org/copyright/software-license-2023/): effective 1 January 2023, conditional reuse, notices/change notices and trademark/publicity restriction verified.

GOV.UK footers identify OGL v3.0 with exceptions. Independent retrieval of the [OGL endpoint](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/) again failed with HTTP 403. Full OGL terms and third-party asset clearance were not verified. Research properly limits its licence claim and redistributes no component implementation or substantial source text.

## Checks performed
- PASS: important factual claims match the primary pages above; no source was replaced by a secondary summary.
- PASS: source findings, original synthesis, untested contract and future acceptance work are explicitly distinguished. No measured usability/performance/conformance result or tested React/Next/browser compatibility is asserted.
- PASS: source history/document dates are distinguished from software releases. Unobserved versions and commit identifiers remain null. Recorded research timestamps precede this review and are not future-dated.
- PASS: sources.json and manifest.json parse as JSON; five source entries agree with source_count, and run IDs agree.
- PASS: prior-reports.json contains 14 reports. All titles and a bounded full-content search for breadcrumb/history terminology were checked; no matching breadcrumb/history treatment was found. The adjacent navigation-disclosure and gallery-filter reports were read directly and address different questions. This is bounded novelty review, not a global originality guarantee.
- PASS: post-review corrections applied and reread: S3 now says implementers must restore previous state rather than implying built-in component behavior; S4 document_date is 2025-09-16.
- PASS: bounded sensitive-data regex scan of research.md, sources.json, manifest.json and this qa.md found zero matches for private-key headers, GitHub token-shaped strings, AWS access-key IDs, long Bearer credentials, quoted secret/password/API-token assignments and email addresses. This is a pattern scan, not an exhaustive privacy/security audit. prior-reports.json is context, not part of the publication scan.

## Explicitly NOT_RUN / not independently established
- Runtime/example execution: NOT_RUN.
- Browser/framework integration, hydration, routing and return-state restoration: NOT_RUN.
- Keyboard, screen-reader, accessibility-conformance and assistive-technology matrix: NOT_RUN.
- Visual/rendering, narrow-width, zoom, translation and disabled-JavaScript tests: NOT_RUN.
- Performance, usability and conversion measurements: NOT_RUN.
- Full legal/asset-rights clearance: NOT_RUN; OGL endpoint inaccessible.
- Remote catalog count, same-run/unindexed-directory absence and publication commit verification: NOT_RUN by this reviewer. These remain publisher provenance assertions, not independently repeated QA results.

No unresolved documentary blocker. Publication still requires the publisher's own successful write and read-back verification.
