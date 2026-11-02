# An index name is not a completed migration

Run: `20261101T214800Z-fullstack-engineering`  
Scheduled: 2026-11-01 21:48 UTC. Evaluated: 2026-11-01.

## Question and novelty

What evidence should an online PostgreSQL index migration require before declaring success or retrying after failure?

Supporting questions: How do build progress and query validity differ? When is a retry unsafe to treat as a no-op?

The live catalog, topic README, all three existing full-stack research reports and recursive branch tree were read first. Existing reports concern post-write cache freshness, lost updates and connection budgets. No same-run artifact or unfinished full-stack directory was present. This reference addresses migration outcome evidence, not pool tuning, query-index design or a tool shortlist.

## Primary-source findings

1. **Concurrent does not mean free.** Concurrent index creation avoids blocking ordinary writes but performs extra scans and waits, consuming CPU/I/O. It cannot run inside a transaction block; only one concurrent build per table is allowed. Partitioned parent indexes need a different procedure. Failure can leave an invalid index with write overhead. A failed unique build can still enforce uniqueness. `IF NOT EXISTS` checks a name, not equivalent definition or validity. [S1](https://www.postgresql.org/docs/current/sql-createindex.html)
2. **Progress is phase-specific.** `pg_stat_progress_create_index` reports active CREATE INDEX/REINDEX backends. It exposes phases, the process currently being waited for and phase-local block/tuple counts; some fields depend on access-method support. Waiting for old snapshots is a documented phase, not evidence by itself that the process is stuck. [S2](https://www.postgresql.org/docs/current/progress-reporting.html#CREATE-INDEX-PROGRESS-REPORTING)
3. **Write readiness is not query validity.** `pg_index.indisready` controls insert/update participation, whereas `indisvalid` describes query validity. `indislive` distinguishes an index being dropped; `indcheckxmin` can further restrict use by snapshots. Therefore one boolean alone is an incomplete operational receipt. [S3](https://www.postgresql.org/docs/current/catalog-pg-index.html)
4. **Recovery has its own scope.** REINDEX rebuilds existing index contents. Only the INDEX form can concurrently rebuild an invalid index. It is not a substitute for correcting a wrong definition; review the actual failure before choosing reconstruction. [S4](https://www.postgresql.org/docs/current/sql-reindex.html)
5. **Validity is not benefit.** EXPLAIN describes the chosen plan; cost units are planner units, not elapsed milliseconds. EXPLAIN ANALYZE actually executes the statement. Do not casually use it on production writes or expensive queries to obtain a screenshot. [S5](https://www.postgresql.org/docs/current/using-explain.html)

## Original synthesis — proposed, not tested

Treat migration completion as a small evidence bundle rather than a green task badge:

- **Intent:** approved schema-qualified table/index identity plus complete expected definition, including keys, order, predicate, expressions and uniqueness.
- **Execution:** installed server version, migration-tool transaction behavior, attempt ID, start/end times and command outcome.
- **Reconciliation:** current object identity, catalog state and definition comparison after the command returns or its connection is lost.
- **Value:** separately authorized, representative plan/workload evaluation, not merely object existence.

A lost client connection produces an unknown outcome until reconciled. Do not blindly launch another migration because a CI runner timed out. Check whether the original attempt is still active and whether its object matches the intended change. Coordinate one deployment owner for this table so two release jobs do not independently repair the same artifact.

Proposed decision table:

| Observed evidence | Proposed interpretation | Safe next decision |
|---|---|---|
| Active attempt; phase has changed | Work remains in progress | Observe agreed operational budget; do not start a duplicate |
| No active attempt; expected object absent | Not proven applied | Review command receipt before authorizing a new attempt |
| Object exists but differs from intent | Name collision or drift | Stop automatic retry; obtain reviewed correction plan |
| Expected definition; validity not established | Partial or failed migration | Capture failure and relevant state; review narrow recovery |
| Definition and state match; workload not evaluated | Structural completion only | Report performance benefit unverified |

The table is an application-level policy proposal, not a PostgreSQL state-machine API. Observation permissions and changing concurrent state can limit certainty. A missing progress row alone cannot distinguish success, failure, visibility restrictions or an operation that ended between samples.

Recovery should be explicit and separately reviewed: determine dependencies, uniqueness implications, expected traffic and resource headroom before any drop/rebuild. Never make the receipt collector silently remove an index. This report authorizes no database changes and provides no turnkey production migration command.

## Compatibility, alternatives, costs and reuse

The inspected official documentation displays PostgreSQL **18**. This is a documentation version, not the project's installed version or the latest verified release. Exact patch/release/commit dates are unknown. The examples on the EXPLAIN page are documentation examples, not executed demos. Recheck the actual server major and migration runner before implementation; no ORM/Node/Next.js integration was tested.

This is a SQL/catalog workflow in an existing PostgreSQL deployment. It needs approved database permissions and a runner capable of the chosen command's transaction rules; it requires neither a new package nor MCP. Existing application drivers may provide the transport, but their compatibility is unverified. Alternatives include a conventional build during an accepted maintenance window or deferring the index until workload evidence justifies it. Neither is a universal recommendation.

Measure CPU/I/O headroom, storage growth, workload latency and deployment duration before selecting an operational window. Additional index maintenance and hosting capacity have real operational costs; no prices, savings or benchmark results were measured. Do not raise memory settings or purchase capacity merely because a build waits.

PostgreSQL's actual license permits software/documentation reuse under its notice-preservation terms and disclaims warranties/support obligations. Public availability is not a waiver of these terms. This package contains original analysis and short paraphrases, no implementation or assets. [S6](https://www.postgresql.org/about/licence/)

Original future application: a deployment dashboard that shows separate labels for command outcome, object reconciliation and workload acceptance. Its alert should say “needs reconciliation,” not “rerun migration,” when the runner loses contact.

## Proposed acceptance checks — all NOT RUN

1. Force a controlled build failure in a disposable database; verify that cleanup is not inferred from task failure.
2. Present the same index name with a deliberately different definition; ensure the receipt rejects false equivalence.
3. Simulate lost runner contact; reconcile the original attempt before allowing another.
4. Hold a disposable old snapshot; verify phase-aware monitoring rather than fabricated global percent completion.
5. Verify catalog receipt and representative query plan separately; a valid object does not automatically pass performance acceptance.
6. Exercise migration-runner transaction behavior against the supported server major.

No database, runtime, migration, concurrency, security, accessibility, performance or integration tests were performed. No dependencies, services, settings or application code were changed. Documentary review is not deployment approval.
