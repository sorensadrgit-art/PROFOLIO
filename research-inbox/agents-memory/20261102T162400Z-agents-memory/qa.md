# Independent documentary QA

Run: `20261102T162400Z-agents-memory`  
Reviewed: 2026-11-02T16:29:26Z  
Outcome: **APPROVED_DOCUMENTARY**

A separate QA worker read the draft and independently reopened all six cited primary sources, including the complete transport resumption/session sections and the pinned schema, licence and commit. The tag target was independently resolved.

## Checks completed

- Confirmed optional resource subscription and list-change capabilities are distinct; reading contents is separate.
- Confirmed the pinned updated-resource notification requires a URI, permits common metadata and can refer to a sub-resource. It does not require a content revision or patch.
- Confirmed SSE identifiers/replay are optional and replay is scoped to the disconnected stream. Expired session handling requires new initialization; a transient stream reconnect is not equated with session replacement.
- Confirmed negotiated capabilities govern operation. Reconciliation, local generation tracking and derived-memory labels are clearly original, unimplemented design proposals.
- Confirmed pinned MIT licence and commit date. The internal DRAFT-2025-v3 schema constant caveat is accurate; no claim was made that this is an SDK runtime version.
- Reviewed against the ten earlier topic titles and inherited report evidence: resource invalidation/reconnect recovery is distinct from earlier tool-catalog, TTL, retrieval and checkpoint questions. No same-run artifact was reported in the preflight directory listing. Parent must repeat publication-time duplicate checks.
- Parsed sources.json and manifest.json successfully. Manifest pending QA fields may now be finalized by the publisher.
- Bounded regex scan of the draft package for private-key markers, common token forms, credential assignments, private-host patterns, IP addresses and email addresses returned no findings. Manual review found no private account records, copied scripts or confidential operational data.
- Source summaries are short, attributed and paraphrased; no substantial copyrighted text or source code is reproduced.

## Limits

This approval applies only to the documented research review, not Gemini approval or deployment permission. No runtime, integration, reconnect, extraction, accessibility, security-penetration or performance tests were performed. The bounded scan is not exhaustive secret detection. The publisher remains responsible for final manifest/catalog validation, current-head conflict protection and remote verification. No external changes were made by this QA worker.
