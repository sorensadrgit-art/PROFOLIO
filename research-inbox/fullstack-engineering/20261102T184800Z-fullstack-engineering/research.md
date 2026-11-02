# Skipping a locked job is not a durable execution lease

Run: `20261102T184800Z-fullstack-engineering` · scheduled 2026-11-02 18:48 UTC. Retrieved and evaluated: 2026-11-02.

## Question and novelty

How should a PostgreSQL-backed job queue distinguish short-lived claim locks from ownership of work performed after commit?

The live catalog, topic directory and ten prior full-stack reports were read before selection. No same-run package or unfinished topic directory was observed. Earlier notes cover pools, optimistic edits, pagination and transaction-related boundaries, but not worker claim expiry and stale completion. This is a focused documentary reference, not a queue-product shortlist or a diagnosis of any live system.

## Source findings

- **S1 — skip semantics:** `FOR UPDATE SKIP LOCKED` skips rows that cannot immediately be locked. PostgreSQL explicitly permits this inconsistent view for queue-like consumers, not general-purpose reads. Only row locking gets this behavior; the ordinary table lock can still wait. A locking clause needed inside a CTE must be placed there. [SELECT](https://www.postgresql.org/docs/18/sql-select.html)
- **S2 — lock lifetime:** row locks end with the transaction or relevant savepoint rollback. Ordinary reads are not excluded by these locks. Lock acquisition can itself write to disk. [Explicit locking](https://www.postgresql.org/docs/18/explicit-locking.html)
- **S3 — competing updates:** under Read Committed, a competing updater can wait, then reevaluate its WHERE predicate against the updated row after the first updater commits. Different isolation levels require their own failure/retry handling. [Transaction isolation](https://www.postgresql.org/docs/18/transaction-iso.html)
- **S4 — clocks differ:** `now()`/`CURRENT_TIMESTAMP` describe transaction start; `statement_timestamp()` describes command receipt; `clock_timestamp()` returns wall-clock time and can change within a statement. They are not interchangeable elapsed-time measurements. [Date/time functions](https://www.postgresql.org/docs/18/functions-datetime.html)
- **S5 — commit boundary:** a transaction groups database changes atomically, with intermediate updates not exposed to concurrent transactions. Libraries may implicitly manage BEGIN/COMMIT, so inspect the actual driver's contract. [Transactions](https://www.postgresql.org/docs/18/tutorial-transactions.html)
- **S6 — reuse terms:** PostgreSQL's licence permits software and documentation reuse subject to retained notices and disclaimers; it does not provide warranty or support obligations. [Licence](https://www.postgresql.org/about/licence/)

## Original synthesis: claim, execute, reconcile — untested

A database row lock protects a short transaction. A worker making a remote call after that transaction commits needs a separate ownership protocol. Treat a lease as application data with explicit expiry and generation, not as a database lock that somehow remains held. None of these SQL clauses provides exactly-once external effects.

Proposed job record: immutable job ID, authorized scope, state, eligibility time, attempt count, claim generation, lease expiry and safe outcome reference. The generation changes for each new claim; a stable worker name alone cannot distinguish a restarted attempt from an older process with the same name. Authorization comes from the server's worker policy, not possession of a job ID or generation alone.

Proposed short claim transaction:

1. Select a bounded batch of eligible ready rows using a deterministic tie-breaking order and row locking with SKIP LOCKED.
2. In that same transaction, transition the selected rows to running, increment their generations and record expiry using an explicitly chosen database-time policy.
3. Return claim identifiers and confirm commit before starting external work. If commit outcome is ambiguous, reconcile the recorded claim rather than assuming either success or failure.

The selection and update must share the transaction and locked identities. An unlocked SELECT followed by an unconditional UPDATE reintroduces a race. Do not hold the transaction open for minutes of remote work merely to preserve the row lock. These steps are a contract sketch, not executable SQL or a complete schema.

A worker renews or completes only through an atomic predicate that matches job, authorized scope, running state and its claim generation. Choose explicitly whether an expired-but-not-yet-reclaimed attempt may finish. A strict-expiry policy also checks expiry at the chosen database evaluation point; it must not claim that a predicate checked earlier guarantees validity at a later wall-clock instant. On zero affected rows, ownership or eligibility is unconfirmed: stop claiming success and reconcile. Do not automatically overwrite the new owner's result.

Expiry is a reason to reconsider ownership, not proof that the original worker stopped. A paused worker can resume after another attempt takes over. A generation predicate can protect the queue's own result row; it cannot retroactively stop a remote side effect. For external mutations, define a destination-enforced idempotency key and result-reconciliation contract, or a resource-side fencing mechanism that rejects stale generations. A token checked only in the queue is not downstream fencing. If the destination supports neither, document the duplicate-effect risk and require explicit recovery policy.

A recovery pass should conditionally reclaim only still-expired running claims, under the same concurrency discipline as normal claimers. Bound retries and distinguish retryable failures, terminal failures and unknown external outcomes. Preserve safe attempt metadata without logging complete payloads. A completed remote effect followed by a failed completion write is an ambiguity to reconcile, not automatic permission to repeat the effect.

An empty SKIP LOCKED result is not evidence that all jobs are finished: ready rows may be locked, jobs may be delayed, and work may already be running. Likewise, order preference does not prove fairness under contention. Proposed metrics should distinguish ready, running, expired, terminal and unknown-outcome counts, and alert on age rather than presenting one empty fetch as queue completion.

## Engineering evaluation

PostgreSQL is a relational SQL engine; this pattern uses existing transactional driver access, not a new npm package. The inspected documentation is major version 18. Exact server patch, installed driver version, source commit and release dates were not established. The site displays a 2026-10-15 PostgreSQL 19 RC 1 announcement; that is an observed banner, not a recommendation or verification of a release artifact.

Integration needs supported transactions, parameter binding, bounded worker concurrency, explicit authorization, a recovery owner and tested downstream semantics. No MCP connection, new account or paid service is required by the design. Examples in the official documentation were read, not run. No installation or hosting changes occurred. Confirm ORM and pooler transaction behavior before implementation.

Costs include database polling, lock/update traffic, dead tuples and maintenance, indexes, connections, retention and engineering verification. Amounts and throughput are unknown. Long transactions, contention, clock assumptions and duplicate external effects are risks to test, not observed defects. A dedicated durable queue can offer different delivery/visibility features but adds another system and still requires examining external-effect semantics. For a small serial workload, a single worker can simplify concurrency, but does not eliminate crash ambiguity. Neither alternative is benchmarked here.

Original future application: a job-status panel that shows claim generation, lease policy and external outcome confidence separately, preventing “worker acknowledged” from being displayed as “business operation completed.” This is a proposal only.

## Acceptance exercise — all NOT RUN

1. Two claimers race for one ready row; only the committed claim may begin the proposed work.
2. All eligible rows are locked: empty fetch is not presented as global completion; table-lock delay remains observable.
3. Crash before claim commit, after commit, and after remote effect but before completion; verify distinct recovery outcomes.
4. Pause an old attempt past expiry, reclaim with a new generation, then resume it; stale completion cannot replace the new result.
5. Test renewal versus reclaim and strict-expiry behavior at boundary times, including a long transaction demonstrating frozen `now()`.
6. Repeat an external request with the same destination idempotency identity; inspect actual destination behavior, not just queue state.
7. Unauthorized scope, retry exhaustion, delayed jobs and continuously contended jobs retain honest state and bounded resource usage.

## Limitations

Six primary sources support documentary findings; the protocol is original, unexecuted synthesis. No SQL, concurrency, runtime, security, integration, accessibility or performance tests were performed. No repositories or substantial copyrighted text were copied. Mutable documentation must be rechecked against the deployed versions. Approval means this task's independent documentary review only, not deployment permission. The UPDATE reference timed out twice and was excluded from the source set; no claim relies on its retrieval.
