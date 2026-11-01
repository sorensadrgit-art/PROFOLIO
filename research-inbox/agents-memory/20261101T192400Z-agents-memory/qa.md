# Independent documentary QA

Reviewed: 2026-11-01T19:26:13Z. Reviewer: separate QA worker.
Verdict: **PASS — APPROVED_DOCUMENTARY** after one scope clarification.

## Evidence checks

- Independently fetched the pinned LangGraph store source. Blob SHA matches sources.json. TTLConfig read-refresh, omission default, store-support caveat, minutes and no-sweep defaults support the report. No backend runtime behavior is asserted.
- Redis TTL official documentation confirms the documented return-value distinction. EXPIRE web opens timed out twice; the official Redis documentation repository at https://github.com/redis/docs/blob/main/content/commands/expire.md and the official page's searchable content independently confirmed the expiry assertions. This is a documentary fallback, not a service test.
- Qdrant's official filtering page confirms datetime-range format, UTC comparison and feature introduction.
- Independently fetched all three repository license files; their blob SHAs match sources.json. The report avoids treating visibility as unrestricted reuse or selecting a legally cleared hosting license.
- Re-read the live catalog: three earlier agents-memory entries cover authorization, effect receipts and instruction trust. The current TTL/freshness question is materially distinct. Full historical package review was performed by the author; this worker's duplicate review used catalog titles and summaries.
- Source-derived paraphrases are brief and below the relevant 200-word document limits. No substantial source text or repository implementation is reproduced. Recommendations and inference are separated from source findings.

## Correction

Acceptance item 1 now explicitly conditions retention extension on supported, enabled sliding read-refresh and checks the disabled case. This prevents interpreting every read on every backend as a TTL extension.

## Validation and limitations

Both JSON files parsed successfully. A local regex scan found no private-key blocks, common GitHub/AWS/API credential patterns, assigned secret strings, IPv4 addresses, private host references or local-account paths in the three reviewed files. Manual inspection found only public sources and generic design content. This limited pattern/manual scan is not a guarantee that all possible sensitive content can be detected.

No runtime, integration, performance, accessibility, deployment or deletion test was performed. Compatibility, exact backend dependencies and license obligations still require release-specific implementation review. This approval covers original sanitized documentary research only, not Gemini approval or permission to deploy. Publication and remote integrity verification are the publishing worker's responsibility.
