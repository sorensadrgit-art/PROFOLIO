# Backpressure is neither a memory ceiling nor a delivery receipt

Run: `20261102T094800Z-fullstack-engineering` · scheduled 2026-11-02 09:48 UTC · evaluated 2026-11-02.

## Bounded question and decision

For a Node.js HTTP export endpoint, what does respecting stream backpressure actually establish, and what must be designed separately?

**Decision — original synthesis, untested:** use backpressure to coordinate producer and consumer speed, but separately constrain work admission, record/chunk size, transform expansion, and request lifetime. Do not equate a completed server response with evidence that the client received or persisted the export. This is a design review aid, not a deployed endpoint or performance certification.

Novelty: the live catalog and seven preceding full-stack packages were inspected before selection. Those cover cache invalidation, optimistic writes, connection pools, index validity, React ordering, Vite environment substitution, and Playwright retry scope. Stream buffering and delivery semantics are a distinct unanswered production-engineering question. The topic directory contained those seven runs and README, with no unfinished same-topic directory or this run ID.

## Source findings

**S1 — Node stream API.** `highWaterMark` controls a buffering threshold rather than imposing a general hard memory limit. Object-mode thresholds count objects, not their byte size. Duplex/Transform streams have separate readable and writable buffers. When `write()` returns false, the producer should stop writing until `drain`; ignoring this can continue accumulating buffered data and eventually abort the process. `pipeline()` destroys streams on error; its documented HTTP example warns that the socket can already be destroyed before an error response is attempted. These are library contracts, not an end-to-end memory guarantee. [Stream API](https://nodejs.org/api/stream.html)

**S2 — Node HTTP API.** A ServerResponse `finish` event reports that its final headers/body segment was handed to the operating system. It does not establish client receipt. `close` can mean normal response completion or premature connection termination. Therefore logging either event alone as “download received” is unsupported. [HTTP API](https://nodejs.org/api/http.html)

**S3 — Maintainer learning guide.** The backpressure guide describes producer/consumer coordination and gives runnable examples. It explicitly advises honoring negative push/write signals and testing differences between Node/library versions. Its example measurements are not measurements of this project and are not reproduced here. [Backpressuring in Streams](https://nodejs.org/learn/modules/backpressuring-in-streams)

**S4–S5 — Repository and licence.** Node's licence grants broad reuse subject to retaining copyright/permission notices in copies or substantial portions; externally maintained components have additional listed terms. It is not unrestricted public-domain material. The observed main commit was `70e1db3deb6ed16dad31ace0dbc61e0d8c6be0ff`, committed 2026-11-02 08:35:07 UTC. This establishes one maintenance observation, not a support guarantee or installed release. [Pinned licence](https://github.com/nodejs/node/blob/70e1db3deb6ed16dad31ace0dbc61e0d8c6be0ff/LICENSE), [commit](https://github.com/nodejs/node/commit/70e1db3deb6ed16dad31ace0dbc61e0d8c6be0ff)

## Original engineering synthesis — proposed, not tested

Treat the endpoint as three independent contracts:

| Contract | Proposed evidence | What it does not prove |
|---|---|---|
| Flow control | Producer stops when downstream cannot accept more | All memory use is bounded |
| Resource budget | Admission cap, bounded chunks/records, cancellation and measured load envelope | Browser received the export |
| Delivery semantics | Server-local completion plus a separately defined client acknowledgement if needed | Durable storage merely from socket completion |

For design review, draw every staging area: database fetch batch, application array, serialization workspace, transform input/output, stream buffers, and socket/proxy layers. A streaming response wrapped around an already materialized full-result array does not satisfy the proposed bounded-work design. Model per-request resources first, then multiply by allowed concurrent exports; treat compression or enrichment as potential additional work rather than assuming the stream setting covers them. This is a budgeting heuristic, not a measured formula.

Prefer one explicit request owner for cancellation and cleanup. Review disconnect, timeout, source error and transform error paths together. A producer waiting only for `drain` needs a termination path too; the client might disconnect instead of becoming writable. Verify exactly which operation owns the socket before designing a fallback error response. Reject predictable invalid requests before beginning the streamed body where possible; once output is committed, document whether an interrupted export is detected by length, checksum, format validation or an application-level completion marker. These are candidate contracts to choose and test, not universal requirements.

## Tool and integration evaluation

- **Purpose / technology:** Node's built-in JavaScript stream and HTTP APIs coordinate incremental I/O in a Node server. TypeScript applications can use the same runtime interfaces, but their type-package/compiler compatibility must be checked locally. No React UI library or additional npm streaming dependency is required for this bounded design.
- **Installation / hosting:** use the project's already supported Node runtime; this task installed nothing. Review the actual framework response adapter, proxy buffering, connection/time limits and database driver's streaming/cancellation behavior before adopting the design. Edge/browser runtimes and Web Streams are not assumed to expose identical Node interfaces.
- **API / MCP / authentication:** application code would use built-in stream/HTTP interfaces; no MCP server or external account connection is necessary. Existing endpoint authorization and export-data access rules remain separate requirements. No credentials are part of this research.
- **Versions / maintenance:** fetched API pages display Node v26.13.0. This is an observed documentation label, not a claim that the target application runs that version or that it is the recommended supported release. Release date and deployed runtime are unknown. The repository observation above is independent of that label.
- **Demos:** official guide examples are the reference demonstrations; none were executed or copied into this package.
- **Costs / risks:** no new paid service is proposed. Hosting CPU, memory, bandwidth, database occupancy and engineering test time remain operational costs; amounts are unknown. Slow consumers, oversized records, unbounded application queues and ambiguous completion logs are review risks, not diagnosed defects in the user's system.
- **Alternatives:** for small strictly capped results, a buffered response may simplify ownership; for long exports, a queued artifact-generation/download workflow may decouple generation from connection lifetime but adds storage, retention and job-state obligations. Native Web Streams may fit a different runtime, requiring its own lifecycle review. These are untested architectural choices rather than benchmark rankings.

## Acceptance experiment backlog — all unperformed

1. Baseline small export, then oversized single record and many small records; inspect peak process memory and retained buffers rather than only heap.
2. Slow/non-reading client, cancellation while blocked, disconnect mid-transform, source failure and concurrent exports at the admission boundary.
3. Confirm downstream cleanup and database release on every terminal path; verify no hung wait for a missing drain event.
4. Inspect proxy behavior and client-visible truncation. Keep metrics named server-completed, interrupted and client-acknowledged rather than conflating them.

Future application: a reusable export contract sheet beside each endpoint could declare ownership, size/concurrency bounds, abort paths and the exact meaning of success. A CI fixture could later exercise slow-consumer and disconnect cases. Neither exists as an implementation from this research.

**Limitations:** documentary review only. No runtime, memory, performance, load, network, integration, security or accessibility tests. No claims about the live VPS or Gateway. Source contents were treated as untrusted reference data; no source instructions were executed. Unknown metadata remains null in sources.json.
