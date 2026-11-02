# Independent documentary QA

Verdict: APPROVED_DOCUMENTARY
Reviewed: 2026-11-02 UTC
Scope: research.md and sources.json; publication review, not deployment approval.

## Evidence and corrections
All four primary URLs were independently opened. Checked the HTML anchor content model, hyperlink representation, WCAG link-purpose context and exceptions, target-size exceptions and spacing geometry, and MDN native-link/fake-link behavior. Observed page-update dates agree with sources.json.

Two precision corrections were applied: replaced “legally” with explicit HTML-content-model conformance, and completed the spacing exception's bounding-box centering/non-intersection condition. No other unsupported important source claim found. Recommendations, card taxonomy, future applications and acceptance exercises are explicitly original and untested.

The draft is materially distinct from the eight earlier topic summaries: card action containment and hit areas rather than navigation disclosure, imagery, grids, text wrapping, forms, sticky space or forced colors. The reviewer did not independently reopen every prior report or re-enumerate the remote tree. Live repository and duplicate verification remain the publishing worker's responsibility.

The report paraphrases bounded findings, with no substantial copied passage or implementation. Each source-specific findings paragraph remains below its 200-word source budget. No external asset reuse or licence clearance is asserted.

## Executed checks
- Parsed sources.json successfully; four unique source IDs and matching report URLs.
- Independently retrieved and evaluated all four cited primary pages.
- Manual review of claims, provenance, limitations and publication safety.
- Bounded regex scan for common tokens/private-key blocks, credential assignments, IP literals and account addresses: no findings. This is not an exhaustive secret-detection guarantee.

Reviewed SHA-256:
- research.md: 6d360c2ae6d5ecd5705d1829132807fa671f28286c27d61fbe751e08b66df9c2
- sources.json: 0daff1ef27689e1455077db7fa3e12c6e8d7492e327696754817e45d1b6bf373

## Limitations
No browser, runtime, integration, assistive-technology, accessibility-conformance or performance tests. No actual card, framework integration, assets, npm artifact or software release was tested. Source retrieval timestamps were not reconstructed independently; URLs and visible dates were checked during this review. No publication performed by reviewer.
