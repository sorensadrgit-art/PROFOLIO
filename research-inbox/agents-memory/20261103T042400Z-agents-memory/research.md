# A graph upsert is not entity resolution

Run: `20261103T042400Z-agents-memory` · Scheduled: 2026-11-03T04:24:00Z.  
Evaluated: 2026-11-03 UTC; primary sources rechecked 2026-11-06 UTC. Resumed for scheduled slot 2026-11-06T22:24:00Z under the original run ID. Bounded design reference, not a product shortlist.

## Question and decision

How can multiple agents add knowledge-graph memories without confusing equal labels, database uniqueness and real-world identity?

**Original synthesis:** resolve identity explicitly before committing a canonical entity. Use immutable application identifiers for storage, preserve source-specific mentions separately, and record the evidence behind any proposed equivalence. A database can enforce key uniqueness; it cannot establish that two extracted names identify the same person, project or object.

Original intake read the live catalog, topic directory and 14 existing agents-memory reports. The November 6 resumption rechecked the 105-entry catalog and the same 14 published topic reports, found this unpublished checkpoint, and resumed it rather than opening a duplicate run. No same-run entry or unindexed published topic directory was present. The existing reducer report concerns conflicting agent conclusions; this report addresses entity identity, storage keys and graph upsert semantics. No existing package is replaced.

## Primary-source findings

1. Cypher MERGE matches an exact pattern or creates it. Identifying-property constraints protect against unwanted duplicates under concurrent loads; MERGE alone generally guarantees existence, not uniqueness. The current manual separately documents serialization for relationship MERGE between already-bound endpoints, so the general warning must not be turned into a claim that every relationship merge duplicates. Null values are invalid in a merge pattern. [S1](https://neo4j.com/docs/cypher-manual/current/clauses/merge/)
2. A composite uniqueness constraint applies to a combination of properties, but records missing any constrained property are outside that uniqueness rule. Key constraints additionally require existence; the manual marks key and property-existence constraints Enterprise Edition. Therefore a uniqueness constraint alone is not a mandatory-identifier gate. [S2](https://neo4j.com/docs/cypher-manual/current/schema/constraints/create-constraints/)
3. The scalar-function documentation limits elementId-to-element mapping guarantees to a transaction and recommends application-generated identifiers rather than relying on internal IDs. A saved graph-memory reference therefore needs an application identity contract, not a presumed permanent internal locator. [S3](https://neo4j.com/docs/cypher-manual/current/functions/scalar/)
4. Python managed transaction functions can be retried; the driver documentation requires idempotent transaction functions. Keep outside effects out of a retried callback, or handle them under a separate receipt contract. That last recommendation is our synthesis, not a cross-service transaction guarantee. [S4](https://neo4j.com/docs/python-manual/current/transactions/)

## Proposed entity admission model — original and unimplemented

Keep three kinds of records distinct:

- **Mention:** a source-specific observation with document revision, location, extracted label and extraction version. Its stable ID identifies that observation, not a real-world person.
- **Entity:** an application-assigned opaque identifier inside an authorized project scope. Names and aliases are mutable attributes, never the sole identity key.
- **Resolution decision:** a versioned proposal or acceptance that links mentions to an entity, records supporting/conflicting evidence and identifies the permitted decision process. A model's similarity score is only a candidate signal.

An ingestion sequence to evaluate:

1. Authenticate the caller and establish the permitted project server-side. A project key is not authorization; existing scope controls still apply.
2. Validate required identity fields before writing. Reject missing keys rather than silently generating a new canonical entity for every retry. Create the appropriate database constraint through a separately authorized migration and verify it exists.
3. Persist an immutable mention receipt keyed by project, stable source identifier, source revision, source location, a disambiguator for multiple mentions at that location, and extraction version (including model/configuration version). Keep logical source-occurrence identity separate when grouping alternative extractions; a changed extractor must not overwrite a prior immutable receipt. Use a canonical encoding and collision handling; do not concatenate ambiguous strings or put private source text into public keys.
4. Retrieve candidate entities within the authorized scope. Preserve unresolved mentions when the evidence is ambiguous. Equal normalized names, embedding proximity and shared email domains are not sufficient proof of identity.
5. Admit an entity under its stable application ID. Merge on identifying properties only; update mutable display attributes separately under an explicit conflict policy. Including a changing name in the identifying pattern can turn an update into a failed constraint check or a different match/create decision.
6. Persist the resolution decision and graph changes transactionally where the chosen storage supports it. Keep external notifications outside retryable work. Distinguish a submitted mutation from a verified committed outcome.
7. Correct an incorrect resolution by recording a superseding decision and re-evaluating affected links. Do not erase the original provenance or assume a graph rewrite can recall summaries already sent to other systems.

These are application concepts, not Neo4j, MCP or GraphRAG standard fields. Database constraints cannot prevent a correctly shaped but semantically wrong identity decision. Access control, permission checks and record lifecycle rules remain independent.

**Future application:** a shared-agent memory reviewer could show two source mentions, proposed identity evidence, unresolved conflicts and the affected conclusions before a permitted resolver accepts the link. This would make mistaken entity collapse visible without granting every extraction worker destructive graph-merge authority.

## Tool fit, versions, licence and operations

Neo4j is the concrete property-graph example, with Cypher queries and a JVM implementation. The inspected repository revision is `2994b43b44f35bf6520bd2667ab181bb76a45585`, committed 2026-10-20T08:27:45Z. Its Maven parent declares version 2026.10.0, default Java target 21 (overridable by a build profile), and dependency versions including Lucene 10.5.1, Netty 4.2.19.Final and Jackson 2.22.3. These are repository observations, not proof of a deployed release or a complete dependency audit. [S6](https://github.com/neo4j/neo4j/blob/2994b43b44f35bf6520bd2667ab181bb76a45585/pom.xml) [S8](https://github.com/neo4j/neo4j/commit/2994b43b44f35bf6520bd2667ab181bb76a45585)

The pinned README describes standalone and managed hosting, source-build Maven 3.9.11+, Bash and Make requirements, and Community versus commercial Enterprise editions. It provides basic build/run examples; none was executed. Repository activity does not establish support or maintenance quality. [S5](https://github.com/neo4j/neo4j/blob/2994b43b44f35bf6520bd2667ab181bb76a45585/README.asciidoc)

The actual LICENSE.txt contains GPLv3; the Maven metadata labels GPL-3.0-or-later, and the README identifies Community as GPLv3 and Enterprise as separately licensed closed-source additions. Redistribution must respect applicable licence/source/notice requirements; public availability is not unrestricted reuse. No blanket permission is inferred for Enterprise, hosted services, drivers or dependencies. No implementation or substantial documentation text is reproduced. [S7](https://github.com/neo4j/neo4j/blob/2994b43b44f35bf6520bd2667ab181bb76a45585/LICENSE.txt)

The operational design needs an authorized database, supported driver, controlled schema migration, durable source receipts and a resolver. Python driver examples illustrate API use; the exact driver version and its compatibility with a selected deployment are unverified. MCP is optional and would need a separately permissioned narrow adapter; no connector, account or service was installed. Rolling current documentation is not pinned to the repository revision. Latest release date remains unknown; GitHub's latest-release endpoint returned an old 2017 alpha-tagged release (3.2.0-alpha08), with its prerelease flag set to false and was not used as a current-version claim.

Expected costs include database compute/storage, uniqueness-index maintenance, extraction or embedding inference, human review and provenance retention. No prices, latency, accuracy or resource figures were measured. Alternatives: store mentions and resolution decisions in relational tables when graph traversal is unnecessary; retain unresolved evidence without a canonical entity layer; or use deterministic source-issued IDs only where their issuer and scope are trustworthy. These alternatives were not benchmarked.

## Proposed acceptance exercise — all NOT RUN

| Synthetic case | Required observation |
|---|---|
| Two unrelated entities share a name | Remain distinct unless identity evidence supports a link |
| Same entity changes display name | Stable application ID and prior mention provenance retained |
| Two workers ingest the same mention with the same extraction version | One admitted receipt under the chosen key/constraint contract |
| An extraction version changes for the same source occurrence | Separate immutable extraction receipt; prior evidence preserved |
| Missing one component of a composite key | Application admission rejects it; uniqueness alone is not credited |
| Different projects reuse an external identifier | No cross-project collapse or unauthorized candidate disclosure |
| Managed transaction callback is retried | No duplicate outside notification or unrelated external effect |
| Wrong resolution is superseded | Affected links and derived summaries are marked for reconciliation |
| Application stores an internal element locator | Long-term identity not inferred from successful immediate lookup |

No installation, database connection, query execution, race test, runtime, integration, performance, accessibility or security test was performed. Documentary approval, if granted in qa.md, applies only to this package, not deployment or Gemini approval.
