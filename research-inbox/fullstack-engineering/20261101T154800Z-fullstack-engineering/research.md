# Two editors, one record: prevent lost updates at the write boundary

Run: `20261101T154800Z-fullstack-engineering`  
Scheduled: 2026-11-01 10:48 America/New_York (15:48 UTC). Evaluated: 2026-11-01.

## Question and novelty

How should an HTTP editing API reject an obsolete edit without silently overwriting a newer one?

Supporting questions: How does an HTTP validator connect to an atomic database condition? What should the editor show after a conflict?

The live catalog and its earlier full-stack package were inspected before selection. The earlier package treats cache freshness after a committed mutation. This note addresses a different boundary: whether a stale mutation may commit at all. It is not a product shortlist, deployment or executed database test.

## Primary-source findings

- **HTTP preconditions:** `If-Match` uses strong entity-tag comparison. A weak validator cannot establish a match; `*` tests existence rather than a specific revision. A false condition must not execute the requested method; 412 is an available failure response. The specification permits a successful response in certain already-applied cases, not arbitrary stale-write acceptance. Normal request checks precede precondition evaluation. ETags identify representations, so variant/encoding design matters. [S1](https://www.rfc-editor.org/rfc/rfc9110.html#name-if-match)
- **Missing precondition:** 428 signals that the server requires a conditional request. Its response should explain how to resubmit and must not be cached. This is different from a supplied condition that fails. [S2](https://www.rfc-editor.org/rfc/rfc6585.html#section-3)
- **Database race:** At PostgreSQL Read Committed, an UPDATE may wait for a concurrent updater and then reevaluate its WHERE condition against the committed updated row. Separate SELECTs can observe different snapshots. Serializable transactions address broader anomalies but require handling serialization failures. [S3](https://www.postgresql.org/docs/current/transaction-iso.html)
- **Observable update result:** UPDATE targets rows satisfying its condition; RETURNING exposes rows actually updated. An update count of zero is not itself a database error. The adapter must interpret that outcome rather than treating every successful SQL execution as a successful edit. [S4](https://www.postgresql.org/docs/current/sql-update.html)

These are documentary findings. PostgreSQL pages displayed version 18; neither that display nor the RFC publication dates prove an application's installed versions.

## Original implementation proposal — untested

Give each editable record a non-null revision that changes on every edit affecting its canonical representation. Do not derive authorization from the revision. The authenticated server determines the allowed record scope independently.

For a single canonical JSON representation, a server-generated opaque strong ETag may encode a revision if every representation-changing event changes it. This is a design obligation, not a guarantee of an integer column. If representation variants, related records, redaction rules or serializers alter the bytes independently, redesign the validator rather than advertising false strength. Keep the validator opaque to clients.

At read time, return the data and its validator together. At save time, retain the validator of the version the person actually edited. Validate the request, authorize the target, parse the HTTP condition correctly, and enforce the corresponding revision check in the same atomic operation as the mutation. Do not do an unlocked preflight comparison followed by an unconditional update: another writer can win between those statements.

Original SQL sketch for an already-authorized single-row edit (not a complete endpoint or access-control implementation):

```sql
UPDATE portfolio_entries
SET title = $1, revision = revision + 1
WHERE id = $2
  AND tenant_id = $3
  AND revision = $4
RETURNING id, title, revision;
```

Here `$3` comes from the server's authorized scope, not a trusted client assertion. `$4` is a validated expected revision mapped from the API's validator contract. Authorization must remain valid at mutation time; a tenant predicate alone is not sufficient for every permission model. A production endpoint also needs schema/length limits, parameter binding, transaction handling, appropriate database privileges, and rules covering every other writer.

For two competing updates expecting revision 7, the intended result is exactly one transition to 8; the other cannot also claim its edit was applied. A zero-row outcome may mean stale revision, deletion, authorization filtering or another database behavior. Do not expose another tenant's existence while diagnosing it. Resolve the response under the application's authorized visibility policy; do not mechanically label every zero-row result 412.

A RETURNING row precedes transaction commit in an explicit transaction. Confirm the commit using the actual driver contract before reporting a saved edit. Return a new validator only for the representation it actually describes. Follow the API method's validator rules; a transformed PUT representation, in particular, needs careful RFC review. A lost response is an unknown outcome, not proof of failure. Reconciliation and operation receipts are separate from this concurrency guard.

## Editor behavior and tradeoffs — original recommendations

Keep three values distinct: the base originally loaded, the unsaved local draft, and the latest authorized server record. On conflict, preserve the draft and offer comparison/reload or an explicit user-confirmed merge. Silently obtaining a fresh validator and replaying the old whole-record payload converts conflict detection into last-writer-wins.

For small editorial objects, optimistic revision checks keep edit sessions independent of long-held database locks. A transaction-local lock can protect a short server operation, but holding it through human editing is a poor proposed default. Multi-record invariants may require constraints, locking or serializable transactions beyond this single-row recipe. Measure contention and retry behavior before choosing an alternative; this report contains no latency or cost benchmark.

## Troubleshooting

| Symptom | Inspect | Avoid |
|---|---|---|
| Two editors both report success | Every write path's revision predicate and commit receipt | Checking only in browser state |
| Weak ETag never matches | Validator strength and canonical representation contract | Removing the weak marker to pretend strength |
| Request without revision overwrites data | Endpoint's required-precondition policy | Treating absent input as “latest” |
| Conflict erases the user's draft | Base/draft/latest ownership in UI | Automatic stale-payload replay |
| Zero-row result leaks record existence | Authorization and response mapping | Unscoped diagnostic SELECT |
| Background job bypasses protection | Worker and bulk-import writes | Assuming one guarded route protects all writers |

## Proposed acceptance matrix — all NOT RUN

1. Two independently authorized sessions edit the same base: one commit, one conflict; winner preserved.
2. Missing precondition: endpoint policy rejects before writing; supplied stale condition does not mutate.
3. Weak, malformed, multiple-tag and wildcard input: RFC parsing plus documented endpoint policy; wildcard is not mistaken for revision equality.
4. Another tenant's ID: no write and no unauthorized diagnostic disclosure.
5. Background writer changes representation: old validator cannot authorize a stale overwrite.
6. Commit rejection or connection loss: no fabricated success; ambiguous outcome clearly represented.
7. Draft survives conflict and successful rebase uses the explicitly reviewed latest version.
8. Variant/serialization changes: validators remain truthful, including any compression layer.

## Limitations

No HTTP server, database, driver, framework, browser, concurrency, security, accessibility or performance tests were executed. No installation, API integration or hosting configuration was attempted. The SQL and UX flow are original unexecuted proposals, not production-ready code. Four primary sources were read; mutable PostgreSQL documentation must be rechecked against the installed major version. No third-party repository or asset is copied; repository licensing/maintenance comparisons are outside this focused protocol question.
