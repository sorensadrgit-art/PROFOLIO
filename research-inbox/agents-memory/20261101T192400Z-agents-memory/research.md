# Memory TTL is retention, not factual freshness

Run: 20261101T192400Z-agents-memory. Scheduled: 2026-11-01T19:24:00Z.
Evaluation: 2026-11-01T19:24:41Z. Author: Dot. Type: bounded engineering design reference.

## Question and decision

Can a frequently retrieved memory safely remain actionable merely because its storage TTL has not expired? **No such guarantee follows from TTL.** Keep a factual-validity deadline separate from access-refreshed retention, and revalidate consequential claims against their authoritative source. This is an original engineering recommendation, not a measured product comparison.

The catalog and all three existing agents-memory packages were read before selecting this gap. Earlier work covers authorization, exactly-once effects, and evidence/instruction trust. This package addresses time validity and expiry visibility, without replacing those controls. No same-run directory or unfinished same-topic package was observed at the initial check.

## Source findings

1. **LangGraph:** the inspected Python store interface defines refresh-on-read as true by default for get/search, with an operation-level override. Its default TTL is minutes, absent by default. Omission of expired items defaults false; support is store-dependent, and the optional omission behavior can hide expired items before a deletion sweep. A missing sweep interval means no sweeping in this configuration contract. These are interface/documentation semantics, not proof that every backend implements TTL. [S1](https://github.com/langchain-ai/langgraph/blob/3cd57c038ad827d05f80c6488adea9994156c58c/libs/checkpoint/langgraph/store/base/__init__.py)

2. **Redis:** EXPIRE assigns a key deadline in seconds. Expiration has passive access-time and active cleanup mechanisms; updating EXPIRE can change the deadline. Expiry uses absolute timestamps, so clock behavior matters. TTL reports remaining seconds, with -1 for no expiry and, since 2.8, -2 for an absent key. Neither value reports whether a statement remains true. Do not transfer LangGraph's read-refresh default to Redis reads. [S2](https://redis.io/docs/latest/commands/expire/) [S3](https://redis.io/docs/latest/commands/ttl/)

3. **Qdrant:** payload conditions constrain search/retrieval. Datetime ranges, documented since 1.8.0, accept RFC3339 timestamps and compare in UTC. This is a possible filtering primitive for an application-maintained validity field, not automatic verification of facts. [S4](https://qdrant.tech/documentation/search/filtering/)

**Inference:** a sliding inactivity policy can preserve repeatedly accessed stale information. Retrieval popularity and verified freshness are independent. Physical cleanup and read visibility are also distinct; a backend's deferred sweep must not silently become permission to use expired facts.

## Original synthesis: two clocks and one admission decision

Use a small record contract:

- `observed_at`: when evidence was captured.
- `verified_at`: last successful authoritative recheck, never merely a read.
- `valid_until`: application deadline for actionability; null means unknown, not forever.
- `retention_expires_at`: storage lifecycle deadline, independent of validity.
- `source_revision`, `superseded_by`: evidence identity and replacement relationship.

At retrieval, enforce existing authorization first; then evaluate validity and supersession before assembling actionable context. Reject unknown or expired validity for consequential actions, or route to revalidation. Historical questions may intentionally receive stale evidence, explicitly dated and labeled. A recent re-embedding, summary, or cache read must not advance `verified_at`.

After source replacement, invalidate the old record logically and propagate that relationship to derived summaries and caches. Physical removal is a separate retention operation. An empty vector result does not establish deletion from other stores, backups, or summaries. These are proposed application invariants, not claims that the cited products supply a complete lifecycle system.

## Engineering fit, requirements and trade-offs

**LangGraph** provides a Python orchestration/store abstraction. The inspected source revision is recorded in sources.json; its latest observed file change is October 21, 2026. Backend implementation and dependency versions must be pinned before integration. A store adapter plus an application admission wrapper is required; a TTLConfig declaration alone is not an end-to-end test. The repository license inspected is MIT: preserve applicable copyright and license notices. [S5](https://github.com/langchain-ai/langgraph/blob/main/LICENSE)

**Redis** is an alternative key-expiry primitive, not a semantic freshness engine. Integration requires a separately operated service/client, explicit expiry policy, and handling the no-expiry/missing-key distinction. Its inspected license file distinguishes Redis 8's RSALv2/SSPLv1/AGPLv3 choices from earlier licensing. Public availability is not unrestricted reuse; choose and review the applicable release's terms before redistribution or hosting. This report does not select a license or provide legal clearance. [S7](https://github.com/redis/redis/blob/unstable/LICENSE.txt)

**Qdrant** is an alternative vector retrieval/filtering primitive, useful when validity metadata must constrain semantic recall. It requires a server or compatible deployment, a client/API, correctly typed payloads and a source-update path; filter support does not replace authorization. Its inspected repository license is Apache-2.0; preserve applicable notices and obligations. [S6](https://github.com/qdrant/qdrant/blob/master/LICENSE)

These are reference components, not a discovery shortlist or recommendation to install three services. Exact dependencies, latest releases, deployment compatibility and maintenance cadence were not exhaustively audited; unknown release versions remain null. Official documentation examples were inspected, but no demo was executed. No MCP connection is required to define this policy. An existing MCP adapter would need to carry evidence metadata and enforce the same boundary without granting new permissions.

Alternatives: absolute retention avoids indefinite extension but can discard useful history; sliding inactivity improves cache reuse but says nothing about truth; source-event invalidation can react sooner but needs reliable event delivery and reconciliation. A hybrid validity deadline plus revision invalidation is an untested recommendation. There is no universal safe TTL.

Operational costs are revalidation requests, metadata/index storage, cleanup work, and possibly embedding regeneration. No prices, latency, RAM estimates or savings are asserted. Recheck batching can reduce requests but increases staleness windows. Future application: an agent handoff envelope that preserves evidence age across summarization rather than presenting a recently generated summary as recently verified knowledge.

## Proposed acceptance contract — NOT RUN

Use synthetic records only:
1. Where sliding read-refresh is configured and supported, repeated reads extend retention but never advance verified_at or valid_until; with refresh disabled, they do not extend retention.
2. A record past valid_until cannot support a consequential action even before a sweep.
3. Unknown timestamps, clock skew, missing keys and no-expiry keys remain distinguishable.
4. Superseded evidence is excluded from current answers but available to authorized dated-history queries.
5. Source-update failure cannot silently bless cached claims; summary/cache invalidation is checked independently.
6. Backend/version tests cover refresh overrides, expiry omission support and delayed sweeps.

No runtime, performance, accessibility, integration, deployment, or deletion tests were performed. Approval means documentary QA only. No private records, account details, credentials, copied repository code, or complete copyrighted documents are included.

