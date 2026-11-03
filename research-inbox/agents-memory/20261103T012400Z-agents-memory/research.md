# Changing an embedding model is a memory-index migration

Run: `20261103T012400Z-agents-memory` · Scheduled: 2026-11-03 01:24 UTC.
Evaluated: 2026-11-03 UTC. Author: Dot. Bounded engineering reference, not a discovery shortlist.

## Question and decision

How can persistent agent memory change embedding models without silently querying an incompatible or incomplete index?

**Original synthesis:** Treat the embedding recipe and its indexed corpus as a versioned pair. Equal vector lengths are not evidence of semantic compatibility. Route a query to the matching recipe and index generation; require separately measured retrieval quality and source coverage before cutover.

The live catalog and 13 existing agents-memory reports were inspected first. No same-run directory or unfinished topic package was observed. The earlier [retrieval-miss study](../20261101T222400Z-agents-memory/research.md) distinguishes approximate misses from absence; this report instead addresses changing the representation used to retrieve existing memories.

## Source findings

- Semantic search embeds queries and corpus into the same vector space. Sentence Transformers documents separate query/document encoding paths, including prompts and routing for suitable asymmetric models; these methods can be identical for models without those distinctions. [S1](https://www.sbert.net/examples/sentence_transformer/applications/semantic-search/README.html)
- Qdrant constrains vector dimensions and metric; named vectors permit distinct configurations within one point. Cosine vectors are normalized on upload. These storage rules do not themselves identify the embedding model. That last distinction is our interpretation, not a documented semantic-validation feature. [S2](https://qdrant.tech/documentation/manage-data/collections/)
- Qdrant's migration tutorial says re-embedding requires original data. It describes parallel collections with dual writes and backfill, or adding a named vector to an existing named-vector collection on 1.18+. Its examples report testing on 1.19.0 and use Cloud Inference; that is the publisher's test claim, not ours. The parallel-collection example requires paused deletes/partial updates or extra handling; the named-vector backfill requires pausing updates/deletes to existing points. Old named vectors remain available until explicitly removed. [S3](https://qdrant.tech/documentation/tutorials-operations/embedding-model-migration/)
- Release metadata identifies Qdrant v1.20.0, published 2026-10-22T14:14:12Z. Its tag resolves to commit `222b2e44ab1c3f17e8246396ead6558e45041df7`, committer date 2026-10-21T17:45:09Z. Cargo metadata declares Rust edition 2024, minimum Rust 1.98, version 1.20.0, and dependencies including serde, tokio and tonic. This newer release does not retroactively prove the tutorial's behavior under our workload. [S4](https://github.com/qdrant/qdrant/releases/tag/v1.20.0) [S5](https://github.com/qdrant/qdrant/blob/222b2e44ab1c3f17e8246396ead6558e45041df7/Cargo.toml)
- The pinned Qdrant repository licence is Apache-2.0. Redistribution obligations include providing the licence, identifying modified files and retaining applicable notices; it grants no general trademark permission. This does not license arbitrary model weights, source documents or managed services. [S6](https://github.com/qdrant/qdrant/blob/222b2e44ab1c3f17e8246396ead6558e45041df7/LICENSE)

## Proposed migration contract — original, unimplemented

Represent a retrieval generation with an application-owned descriptor:

```text
generation_id
document_encoder_ref + immutable revision
query_encoder_ref + immutable revision
query_prompt_revision / document_prompt_revision
chunking_revision / preprocessing_revision
dimensions / normalization / similarity_metric
index_target + vector_name
source_revision_watermark
coverage_receipt / evaluation_receipt
state: building | shadow | serving | retired
```

These are proposed application fields, not a Qdrant or MCP schema. A shared model family name is insufficient when prompts, weights or chunking differ. Keep original evidence identifiers stable and separate from derived vector IDs; re-embedding must not make a stale source appear newly verified.

Recommended bounded sequence:

1. Freeze and record both recipes and the authorized corpus scope. Do not send previously private memory to a new hosted encoder without the required authority.
2. Build a separate generation. Choose a write policy that covers inserts, edits and deletions; do not infer cross-index transactions from two successful API calls. A backfill must not resurrect a deleted memory or overwrite a newer revision.
3. Keep a per-record source revision and generation eligibility receipt. Compare exact authorized ID sets and revisions, not merely total point counts. A count can match while different records are missing.
4. Shadow representative synthetic and permitted real queries against each matching query/index pair. Use labelled relevance and answer-support checks; never equate successful vector insertion with useful recall.
5. Switch the query recipe and index target together at the application routing boundary. Track in-flight requests and cache generation. A database alias alone is not proof that all clients changed their query encoder.
6. Keep the old recipe and a sufficiently current old index during a defined rollback window. If dual writes stop, document the recovery gap before calling rollback safe. Retire only under a separately authorized retention/deletion policy.

Future application: a multi-agent handoff can carry a retrieval-generation receipt so another worker can distinguish a genuine lack of evidence from a model/index mismatch. The receipt is diagnostic metadata, not authorization or a claim that a retrieved statement is true.

## Alternatives, integration and operational assessment

This is a design decision, not a product ranking. Qdrant is the concrete Rust vector-search example; Python Sentence Transformers illustrates encoder semantics. The tutorial provides API/client examples, not an MCP requirement. No MCP server or external memory connection is needed to evaluate this design in a disposable fixture.

Three application choices: retain the old encoder until evaluation is justified; rebuild during a permitted maintenance window for lower coordination complexity; or shadow a second generation for continuity at greater resource cost. Parallel collections can isolate index configuration; named vectors share a point's payload and lifecycle. Neither choice is a substitute for scoped access control and deletion policy.

Implementation would require an authorized vector service, compatible client, source-data reader, encoder runtime or provider, durable migration progress and an application query router. Source builds of the inspected Qdrant tag require its declared Rust toolchain; no build or installation was attempted. Encoder/library/model versions, hardware requirements and their licences are not selected here and remain unknown. The repository licence must not be generalized to them.

Expected cost drivers are re-embedding compute or provider usage, temporary vector/index storage, dual writes, shadow-query traffic and engineering/monitoring. No price quotation or savings claim is made. Latency, relevance, storage growth and availability have not been measured. The tutorial's availability language is not a guarantee for arbitrary concurrent edits.

## Proposed acceptance cases — all NOT RUN

| Case | Required evidence |
|---|---|
| Same dimensions, intentionally wrong model generation | Router rejects mismatched recipe/index before retrieval |
| Backfill races with edit or delete | Latest source revision wins; deleted memory remains unavailable |
| Interrupted dual write | Incomplete generation cannot pass coverage gate |
| Equal counts, different source IDs | Set/revision reconciliation detects mismatch |
| Cutover with old cached query vector | Cache key/generation check prevents stale reuse |
| New generation has poorer labelled recall | No promotion solely on successful ingestion |
| Rollback after writes continued | Old generation's coverage and freshness are verified first |
| Changed source access permission | Both generations enforce current access, not cached permission |

## Limits

Documentary review only. No migration, model inference, runtime, performance, accessibility, integration, security or failover tests were performed. Source pages are rolling unless pinned; their publication/commit dates are unknown where not explicitly recorded. Source content was treated as untrusted evidence, including embedded agent-oriented suggestions. No source code, model assets or substantial documentation text is reproduced. Independent review and bounded package scan are recorded in qa.md; approval applies only to this research package, not deployment or Gemini approval.

