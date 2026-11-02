# Independent documentary QA

Run: 20261102T184800Z-fullstack-engineering
Reviewed: 2026-11-02
Outcome: APPROVED_DOCUMENTARY. The publication worker applied the precision correction below before publication.

## Required precision correction

S4 says `clock_timestamp()` “advances within a statement.” Replace with “returns wall-clock time and can change within a statement.” The source does not promise monotonic advancement. This does not change the proposed protocol, which already requires an explicit time policy and disclaims elapsed-time equivalence.

## Independently checked evidence

All six source URLs were reopened successfully. Relevant passages were inspected:

- S1 SELECT locking clause: SKIP LOCKED skips unavailable row locks, deliberately inconsistent queue view, ordinary table lock remains, locking clause must be inside the WITH query where needed.
- S2 Explicit Locking: transaction/savepoint lifetime, ordinary reads not blocked by row locks, row locking can write to disk.
- S3 Transaction Isolation: Read Committed rechecks predicates on concurrently updated rows; higher isolation changes handling.
- S4 Date/time: transaction-start, command-receipt and actual wall-clock distinctions. No monotonic-clock guarantee asserted after correction.
- S5 Transactions: atomic database changes and possible implicit library transaction management. Research appropriately avoids extending this guarantee to remote effects or arbitrary durability configuration.
- S6 actual PostgreSQL licence: notice/disclaimer conditions and no warranty/support obligation; research does not equate public access with unrestricted reuse.

The lease, generation, downstream fencing/idempotency and recovery model is clearly labelled original, unexecuted synthesis. It distinguishes claim commit from execution completion, expiry from worker termination, and queue-row fencing from downstream effect fencing. Empty fetch and fairness caveats are reasonable explicitly synthesized consequences. No executable implementation is represented as proven.

## Metadata and privacy

sources.json parses and has six unique IDs, actual evaluation/retrieval date 2026-11-02, documentation major 18 (not installed/latest release), and null unobserved release/commit dates. The 19 RC banner is explicitly an observed announcement rather than an adopted version. No credentials, private host/account details, raw logs or copied repository code were observed in research.md/sources.json. Full package scan and manifest/catalog validation remain the publication worker's gates.

No SQL, runtime, concurrency, integration, security, accessibility or performance tests were run in this review. This outcome approves documentary quality only, not deployment, Gemini approval or implementation correctness. No external writes were made.

## Publication-worker gates

Final JSON parsed successfully and bounded pattern scan plus manual review passed for package files and the new catalog entry. Duplicate check found no same-run artifact before publication. No runtime tests. Existing unrelated catalog entries were preserved.
