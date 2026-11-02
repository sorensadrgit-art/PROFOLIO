# A pagination cursor is not a snapshot

Run: `20261102T124800Z-fullstack-engineering` · scheduled 2026-11-02 12:48 UTC. Intake began 12:45 UTC after early delivery; sources evaluated 2026-11-02.

## Bounded question

How should a PostgreSQL-backed listing distinguish continuation position from a reproducible dataset?

The live catalog, topic directory and eight previous full-stack reports were inspected before selection. No same-run package or unfinished topic directory was observed. Prior work covers cache freshness, write concurrency, pools, migrations, React ordering, build configuration, test retries and stream flow control. This reference concerns pagination membership, not stream delivery or a tool shortlist.

## Source findings

- **S1 — ordering:** LIMIT needs a unique result order for predictable subsets. OFFSET still computes skipped rows, so a deep offset can be inefficient. A timestamp alone need not uniquely order rows. [LIMIT/OFFSET](https://www.postgresql.org/docs/current/queries-limit.html)
- **S2 — visibility:** default Read Committed gives each SELECT its own statement snapshot. PostgreSQL Repeatable Read instead fixes visibility at the first non-transaction-control statement within that transaction; successive reads do not see later concurrent commits, though own writes remain visible. This is not automatically a serializable business view. [Transaction isolation](https://www.postgresql.org/docs/18/transaction-iso.html)
- **S3 — tuple comparisons:** row inequalities compare left-to-right until a decisive unequal pair; a decisive null produces unknown. Supported operators require the documented B-tree operator-class relationships. [Row comparison](https://www.postgresql.org/docs/18/functions-comparisons.html)
- **S4 — access path:** a matching B-tree can supply ordered limited results without sorting the entire result; the planner may choose another plan. Mixed ascending/descending keys need an appropriately matching order. This does not prove any particular endpoint is fast. [Indexes and ORDER BY](https://www.postgresql.org/docs/current/indexes-ordering.html)
- **S5 — reuse:** the PostgreSQL License permits software/documentation reuse with retained notices and disclaims warranties/support obligations. Public does not mean unrestricted reuse. [Actual licence](https://www.postgresql.org/about/licence/)

## Original design synthesis — proposed, not tested

Define two product contracts separately:

| Contract | Meaning | Suitable proposed use |
|---|---|---|
| Live continuation | Continue after a position in the currently visible ordered set | Browsing a changing editorial list |
| Fixed dataset | Traverse one deliberately captured set/version | Reproducible review or export |

A token containing the last row's sort keys implements a position, not historical storage. Under live reads, inserts, removals, filter changes and edited sort keys can change what subsequent pages contain. Client-side ID deduplication can hide repeats but cannot recover missing rows. Do not advertise a reproducible export merely because the endpoint accepts a cursor.

For a deliberately narrow live-list design, assume a non-null immutable timestamp, unique non-null ID, fixed filter and descending order. A candidate next-page predicate is `(created_at, id) < ($last_time, $last_id)` paired with `ORDER BY created_at DESC, id DESC`. This is original unexecuted pseudocode, not a complete authorized query. First-page handling omits the continuation predicate. Keep the timestamp's database precision and ID representation lossless through serialization. Do not reuse this predicate blindly for null keys, mixed sort directions, different collations or joined rows with duplicate identities.

Proposed token contract: schema version, order identity, normalized filter identity and complete boundary tuple. Validate lengths/types and cap page size server-side. Bind any token-integrity mechanism to the intended query, but independently reauthorize the current requester on every page. An opaque or signed token is not permission to access a tenant. Avoid embedding secrets or unnecessary record content; specify expiration and invalid-token behavior.

Changing filter or order should start a new traversal, not reuse the old boundary. For mutable popularity/rank sorting, explicitly accept live instability or choose a separately versioned ranking dataset. A fixed upper timestamp only excludes some later rows; it does not freeze edits, deletions, or delayed commits with earlier timestamps. These are design consequences, not runtime findings.

For a short bounded reproducible read, one explicitly owned read-only Repeatable Read transaction is a candidate. Starting a new such transaction for each HTTP page does not preserve the original snapshot. Do not keep a database transaction open through arbitrary human browsing as a default. Alternatively generate an authorized immutable result artifact; capturing IDs alone fixes membership but not later field values. Define whether the requirement freezes identity, displayed values, or both.

## Engineering evaluation

PostgreSQL is the existing relational SQL engine assumed by this design, accessed through the application's supported database driver. Documentation displays major **18**; installed server/driver versions, patch release and commit dates remain unknown. The site banner observed a PostgreSQL 19 RC 1 announcement dated 2026-10-15, but this is not a recommendation to adopt a release candidate or independent verification of its release artifact.

No new npm dependency, MCP connection or paid service is needed for the proposed query contract. Deployment integration requires existing database access, parameter binding, authorization and an API layer that preserves key types. Compatibility with an ORM, serverless pooler, replica-routing setup or frontend cache is untested. Documentation examples were read; no demo was run.

A matching index is an optimization candidate, not an instruction to create one. Measure actual plans, latency and write/storage overhead on representative data before changing schema. Offset paging can remain simpler for small bounded sets and direct page-number UX. Fixed artifacts add generation, storage and retention costs; transaction-owned reads consume connection lifetime. Costs and performance gains are unknown. No installations, hosting changes or database operations were performed.

Original future application: a listing contract badge in developer tooling displaying “live continuation” or “fixed dataset,” with query-version and fixture evidence. This could prevent a UI's “all records exported” label from inheriting guarantees that its paging API never supplied.

## Proposed acceptance checks — all NOT RUN

1. Equal timestamps spanning page boundaries: no skipped tie rows in an unchanged fixture.
2. Delete/insert before the boundary between live pages; document actual membership behavior.
3. Move a mutable sort key across the boundary; verify the declared instability policy.
4. Null keys and malformed/lossy timestamps: reject unsupported token cases, not silently omit records.
5. Change filter, sort or authorized scope; old token cannot bypass the new contract.
6. Compare a single transaction traversal with independent page transactions under concurrent edits.
7. For fixed artifacts, verify membership and field-value requirements separately.
8. Inspect representative query plans and storage/latency before claiming an optimization.

**Limitations:** documentary review only; no SQL, concurrency, runtime, security, performance, accessibility or integration tests. No source code/assets copied. No application, host, settings, workflow or external memory changes. Source material was treated as untrusted reference data. Approval means this research review only, not permission to deploy.
