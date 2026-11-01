# A pool is local; the database connection budget is global

Run: `20261101T184800Z-fullstack-engineering`  
Scheduled: 2026-11-01 18:48 UTC. Research began at 18:46 UTC following an early automation delivery. Evaluated: 2026-11-01.

## Question and novelty

Why can a service exhaust PostgreSQL connections even when every process has a small pool, and what should be measured before increasing limits?

The live catalog and both existing full-stack packages were read first. They cover cache freshness and lost-update prevention; this note addresses connection capacity and ownership. No same-run package or unfinished topic directory was present. This is a focused engineering reference, not a product shortlist or an infrastructure diagnosis.

## Source-backed findings

1. **Count every pool instance.** The node-postgres sizing guide explicitly considers all instances of all services, including autoscaling. A pool size that consumes every database slot leaves no operational or rollout headroom. Its sample numbers are illustrations, not universal tuning values. [S1](https://node-postgres.com/guides/pool-sizing)
2. **A limit is not a reservation.** `pg.Pool` creates clients lazily. When full and all clients are checked out, acquisition waits in a FIFO queue. `totalCount`, `idleCount` and `waitingCount` expose different states. The documented default `max` is 10; `connectionTimeoutMillis=0` means no timeout. `idleTimeoutMillis` applies to clients idle inside the pool, not checked-out clients. [S2](https://node-postgres.com/apis/pool)
3. **Ownership survives errors.** A checked-out client must be returned on failure as well as success. For a single independent query, `pool.query` manages acquisition/release. Repeatedly creating pools undermines bounded pooling. Normal shutdown uses `pool.end`; it is not a per-request cleanup action for a shared long-lived pool. [S3](https://node-postgres.com/features/pooling)
4. **Transactions need one client.** All statements of a node-postgres transaction must use the same checked-out client. Dispatching BEGIN and subsequent statements through independent `pool.query` calls does not maintain that invariant. [S4](https://node-postgres.com/features/transactions)
5. **Server limits cost resources.** PostgreSQL allocates some resources according to `max_connections`; raising it also raises those allocations. It is a startup parameter. Reserved and superuser-reserved connections are distinct from ordinary application capacity. [S5](https://www.postgresql.org/docs/current/runtime-config-connection.html)
6. **A proxy changes semantics.** PgBouncer session pooling retains a backend for a client session; transaction pooling returns it after each transaction. Session-scoped features such as LISTEN and session advisory locks are incompatible with transaction mode. Protocol-level prepared plans require a nonzero `max_prepared_statements`; SQL PREPARE is a different, incompatible case in that mode. [S6](https://www.pgbouncer.org/features.html)
7. **Observe contemporaneous evidence.** PostgreSQL distinguishes dynamic activity from cumulative statistics. Statistics can lag and transaction snapshots can retain old observations; cross-session detail may be permission-restricted. Do not grant broad monitoring privileges merely to fill a dashboard. [S7](https://www.postgresql.org/docs/current/monitoring-stats.html)

## Original design synthesis — proposed, not tested

For direct database connections, budget the worst allowed overlap, not today's average replica count:

`sum(max simultaneously alive processes per service × pools per process × pool max) + other clients + operational headroom <= ordinary usable server slots`

This is an upper-bound planning model, not a performance formula. Account for background workers, migrations and old/new replicas during rolling deployment without double-counting reserved server slots. With a proxy, separate client-to-proxy sockets from proxy-to-database connections; the direct formula is no longer a one-to-one backend count.

Synthetic example: 6 web processes with one 8-client pool plus 3 job workers with one 4-client pool permit 60 clients. A full overlap of both generations during deployment permits 120, before other clients. These invented numbers demonstrate multiplication only; they recommend no specific production setting.

A useful proposed diagnostic panel joins timestamped pool counters, acquisition latency, checked-out duration, query/transaction duration, process count and authorized server activity. Classify the bottleneck before changing limits:

| Evidence pattern | Hypothesis to test | First bounded response |
|---|---|---|
| Waiting grows; all clients remain checked out after requests finish | Release path defect | Audit success/error/cancellation ownership |
| Waiting grows with long transactions | Lock contention or unnecessarily held client | Shorten measured transaction work |
| Many replicas, modest pools, server refusal | Aggregate budget exhausted | Bound rollout/autoscale overlap |
| Idle pool clients exist, no queue, stable latency | Reuse may be functioning normally | Do not call every idle socket a leak |
| Proxy connections available but transaction fails | Mode/feature incompatibility | Check actual proxy mode and session feature use |

A connection-acquisition timeout is not proof that a running database statement was cancelled. Define separate acquisition, statement and overall request policies against the actual driver and deployment. A timed-out write can still have an unknown outcome; the earlier mutation/receipt reports cover reconciliation.

For transactions, use explicit ownership with release in a finally path, but also handle failed rollback or broken transport: do not return an uncertain transaction-state client as healthy. Verify the installed driver's destruction/error behavior. Application concurrency and queue admission should be bounded separately; increasing pool size alone can move the queue into the database.

## Compatibility, reuse and operational cost

The inspected node-postgres repository commit was `2a5850fb0db18f5e40a642bbb4a4743de412ab54`, dated 2026-10-23. [S10](https://github.com/brianc/node-postgres/commit/2a5850fb0db18f5e40a642bbb4a4743de412ab54) Its package metadata reports `pg 8.23.1`, Node >=16, JavaScript/CommonJS and ESM exports, and a `pg-pool` dependency. This is repository metadata, not a verified npm release, installed version or recommendation to use an unsupported Node release. [S8](https://github.com/brianc/node-postgres/blob/2a5850fb0db18f5e40a642bbb4a4743de412ab54/packages/pg/package.json)

The actual repository LICENSE is MIT, requiring preservation of its copyright and permission notice for copies or substantial portions; it provides no warranty. This report copies no implementation or assets. [S9](https://github.com/brianc/node-postgres/blob/2a5850fb0db18f5e40a642bbb4a4743de412ab54/LICENSE)

PostgreSQL documentation displayed major 18. Mutable node-postgres and PgBouncer pages were not pinned to a release; confirm installed API and proxy behavior before adoption. The documentation examples are not executed demos.

Possible integration paths are direct Node-to-PostgreSQL pools or an operator-managed compatible proxy. Neither requires an MCP connection. Both require application-owned database access and secrets management; this report does not configure them. A proxy adds deployment, monitoring and failure modes. Larger databases/proxy services can have hosting charges; no prices or benchmark savings were verified. Prefer measured ownership fixes, query improvements and bounded concurrency before paying for capacity.

Original future application: add a CI-reviewed connection-budget worksheet beside deployment replica settings, and test rolling-deployment overlap in staging. Treat that worksheet as a change-review aid, not a substitute for live measurements.

## Proposed checks — all NOT RUN

- Saturate a tiny test pool, confirm queue/timeout behavior, then verify recovery.
- Inject a query error and client-disconnect error; confirm no checked-out client remains stranded.
- Exercise multi-statement transaction rollback on the same client.
- Run a controlled rollout overlap and compare aggregate connections with the declared budget.
- Verify proxy-mode compatibility for every session-scoped feature actually used.
- Confirm shutdown drains only the process-owned pool, without breaking other workers.
- Check telemetry timestamps and permissions; avoid logging query values or credentials.

No database, HTTP server, concurrency, runtime, security, accessibility, performance or cost tests were executed. No dependencies, services, settings, credentials or application code were changed.

