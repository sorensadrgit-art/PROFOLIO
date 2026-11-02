# A missing memory hit is not proof that the memory is absent

Run: 20261101T222400Z-agents-memory. Scheduled: 2026-11-01T22:24:00Z.
Evaluated: 2026-11-01 UTC. Author: Dot. Bounded engineering reference.

## Question and decision

**How should an agent distinguish absent knowledge from a filtered approximate-nearest-neighbor retrieval miss?**

**Original synthesis:** Keep document existence, embedding eligibility, candidate retrieval, and answer correctness as separate acceptance gates. An empty semantic result cannot certify a failed import, successful deletion, or lack of relevant evidence. Diagnose against an authorized exact baseline before changing prompts, embeddings, or resource limits.

The live catalog and all four existing agents-memory reports were read first. Earlier work addresses authorization, checkpoint effects, instruction trust and factual expiry. This report adds filtered-retrieval completeness. No existing run with this ID or unfinished same-topic package was observed. This is not a product shortlist.

## Source findings

- pgvector offers exact search and approximate HNSW/IVFFlat indexes. Approximate scans apply filters after candidate scanning, so selective filters can leave fewer results. Iterative scans, available since 0.8.0, extend scanning until enough results or configured limits. Strict ordering orders returned distances; it does not promise exhaustive candidate discovery. The README recommends comparing approximate results with exact search. NULL vectors, and zero vectors for cosine distance, are not indexed. [S1](https://github.com/pgvector/pgvector/blob/1d5ef0a160cadec08f754528669ce17e20eab38c/README.md)
- The changelog lists 0.8.7 dated October 1, 2026, and an unreleased 0.9.0 section that drops PostgreSQL 13/14. The inspected README still targets 0.8.7 and states PostgreSQL 13+. Do not apply that compatibility statement to unreleased master indiscriminately. [S2](https://github.com/pgvector/pgvector/blob/1d5ef0a160cadec08f754528669ce17e20eab38c/CHANGELOG.md)
- PostgreSQL EXPLAIN exposes the chosen plan. Its cost estimates are not elapsed milliseconds. EXPLAIN ANALYZE actually executes the query; it is not merely a harmless plan preview for arbitrary statements. [S6](https://www.postgresql.org/docs/18/using-explain.html)

## Proposed diagnostic sequence — original, untested

1. **Establish what should exist.** Using the caller's existing permissions, retrieve a known synthetic document by stable ID and revision. Verify its source checksum and ingestion state. Do not broaden permissions because search returns nothing.
2. **Check eligibility separately.** Confirm the record has the expected vector representation, model revision and dimensionality, and satisfies the intended scope, status and validity predicates. Distinguish “stored but not embedded” from “embedded but filtered out.”
3. **Freeze the comparison.** Use the same dataset snapshot, query vector, distance measure, authorization, metadata predicates, and requested k for both paths. Comparing an unfiltered administrator baseline with a restricted user query produces a meaningless recall result and risks disclosure.
4. **Inspect the actual plan.** Record whether the production query used an approximate index. For a bounded, authorized test, establish an exact reference plan explicitly. A configuration label or index's existence is not proof of the executed access path.
5. **Compare candidate identities.** Let E be the exact reference's top-k eligible IDs and A the approximate result IDs. Report |A intersect E| / |E| only when E is nonempty. Report empty-reference cases separately, and use a declared tie policy for equal distances. Record returned count independently. A full result count can still contain the wrong neighbors.
6. **Tune one factor, then remeasure.** Candidate budget, iterative scanning, filter organization or an exact fallback are hypotheses, not automatic fixes. Keep scope unchanged. A higher search budget may spend more time or memory without meeting the retrieval objective.
7. **Evaluate meaning last.** Exact vector neighbors are a geometric reference, not ground truth about factual relevance. Separately judge whether the retrieved source supports the requested prerequisite or capability. Preserve original-source pointers through generated answers.

For a “no knowledge found” response, the proposed application receipt should distinguish: no eligible records; exact reference empty; approximate search returned none; ingestion incomplete; retrieval error; and semantic answer unsupported. Do not collapse these into one success state.

## Engineering fit, alternatives and requirements

pgvector is a PostgreSQL vector-search extension, with C object build entries and SQL extension artifacts in its Makefile. Its build version is 0.8.7 at the inspected revision. [S4](https://github.com/pgvector/pgvector/blob/1d5ef0a160cadec08f754528669ce17e20eab38c/Makefile) It requires a compatible PostgreSQL installation, extension availability and a database client; no MCP server is inherently required. An MCP wrapper would need to preserve existing authorization and expose bounded diagnostic metadata, not arbitrary privileged SQL. Documentation examples exist, but none were run. [S1](https://github.com/pgvector/pgvector/blob/1d5ef0a160cadec08f754528669ce17e20eab38c/README.md)

The inspected license permits use, modification and distribution subject to retaining its specified copyright, permission and disclaimer text. It disclaims warranty and maintenance obligations. Public code is not permission to omit those conditions. [S3](https://github.com/pgvector/pgvector/blob/1d5ef0a160cadec08f754528669ce17e20eab38c/LICENSE) The observed repository commit is October 31, 2026, evidence of recent activity, not a reliability guarantee. [S5](https://github.com/pgvector/pgvector/commit/1d5ef0a160cadec08f754528669ce17e20eab38c)

Alternatives to evaluate: exact search over a small authorized subset; approximate retrieval with measured recall; or lexical/stable-ID lookup when the question demands an exact named instruction. These serve different queries and should not be treated as interchangeable. An exact path may trade latency for completeness relative to its metric; a lexical match may miss paraphrases. No recommendation to install, migrate or deploy is made.

Operational costs include reference-query CPU/I/O, index storage, benchmark maintenance, and embedding generation. Prices, latency, RAM requirements and savings are unknown. Full dependency versions and hosted-provider availability were not audited. Future application: a retrieval acceptance dashboard separating storage coverage, index eligibility, scoped recall and source-supported answer accuracy.

## Acceptance contract — NOT RUN

Use only synthetic, authorized fixtures:
- Known stored ID with missing embedding remains distinguishable from an absent document.
- Selective filters with enough eligible records reveal any short approximate result; authorized exact reference supplies comparison.
- Equal distances use the same declared tie treatment.
- Full k approximate results still receive overlap and answer-support checks.
- Changed predicates, model revisions or dataset snapshots invalidate comparisons.
- Budget exhaustion or timeouts remain incomplete observations, not evidence of absence.
- Source-supported exact prerequisite retrieval is tested separately from broad semantic similarity.

No database, runtime, integration, performance, accessibility or security tests were performed. Documentary QA approval concerns this report only. No confidential records, credentials, deployment changes, or external-memory imports are part of this package.

