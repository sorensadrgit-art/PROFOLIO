# A resource subscription is not a durable memory-sync log

Run: `20261102T162400Z-agents-memory`  
Scheduled: 2026-11-02 16:24 UTC  
Evaluated: 2026-11-02 UTC

**Question:** When an agent caches MCP resources as reusable context, what does a resource-update notification actually establish, especially after reconnecting?

**Original synthesis:** Treat the notification as a reason to reconcile an authorized resource, not as the resource's new contents, a durable change journal, or proof that derived memories are current. Track transport recovery separately from content revision and derived-record acceptance.

## Scope and novelty

The live catalog and ten existing agents-memory reports were read first. Earlier work covers tool-catalog discovery, factual freshness, retrieval misses, context compaction and execution receipts. This report instead addresses resource subscription invalidation and reconnect recovery. The topic directory had no same-run artifact or unindexed in-progress package. Existing files are preserved.

## Source findings

1. **Two optional signals serve different purposes.** The resource capability can expose subscription support, list-change support, either, or neither. List changes concern discovery; per-resource subscriptions concern content changes. Reading content is a separate request. [S1: Resources](https://modelcontextprotocol.io/specification/2025-11-25/server/resources)

2. **The update is not the document.** The pinned TypeScript schema defines an updated-resource notification with a URI and optional common metadata, not a required content body, revision sequence or patch. Its URI may identify a sub-resource of the subscribed target. The comment describes re-reading as potentially necessary. Consequently, an application cannot infer a portable revision ledger from this message alone. [S4: schema](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/38c84e9f93ad191d9eb26d92b945d17bd0efcaf3/schema/2025-11-25/schema.ts)

3. **Replay support is optional and stream-specific.** Streamable HTTP servers may add SSE event identifiers and may replay after a Last-Event-ID request. Replay must not include another stream's messages. A terminated session produces 404 for its session identifier, requiring new initialization. This transport contract does not promise a permanent history of resource revisions. [S2: Transports](https://modelcontextprotocol.io/specification/2025-11-25/basic/transports)

4. **Reconnection does not grant old capabilities forever.** Normal operation must respect the negotiated version and capabilities. Therefore a new session should re-establish its actual resource features rather than trusting a stored capability snapshot. That recovery policy is our synthesis, not a claim that every transient SSE reconnect creates a new session. [S3: Lifecycle](https://modelcontextprotocol.io/specification/2025-11-25/basic/lifecycle)

## Proposed reconciliation design — original, unimplemented

Keep three application records separate:

- **Transport observation:** connection generation, negotiated version, subscription target, last observed event cursor where supported, and whether continuity is known or unknown. Never expose authentication material in this record.
- **Resource observation:** authorized URI, read timestamp, provider revision when documented or null, content digest when appropriate, and read outcome. A digest compares bytes; it does not authenticate an untrusted source or establish factual correctness.
- **Derived memory:** source observation identifier, transformation version and status such as pending, accepted, stale or unavailable. These are application labels, not standard MCP fields.

Recommended flow:

1. Initialize and inspect current resource capabilities. Subscribe only where supported and authorized. Establish a baseline read; do not assume subscribing also reads the resource.
2. On an update, mark the affected cached content and dependent summaries stale. Resolve child-resource notifications according to the provider's documented hierarchy rather than string-prefix guessing.
3. Coalesce repeated invalidations into bounded reads. Record a local invalidation generation before starting a read; if it changes during the read, reconcile again instead of declaring the newest state accepted. This protects against a local race, not every remote consistency anomaly.
4. Validate a successful read, preserve its provenance and rebuild only affected derived records. A failed or denied read leaves stale/unavailable state; it does not authorize a broader query.
5. After lost continuity, use supported stream resumption where applicable, then reconcile content. On a genuinely new session, re-negotiate and re-establish subscriptions. Do not interpret transport reconnection alone as completed memory repair.
6. Where the server exposes no revision token or atomic read/change boundary, document that limitation. A read-before/after-subscribe strategy narrows but does not prove elimination of races. High-consequence uses should re-read at use time or require a stronger provider-specific snapshot/change-feed contract.

A listing refresh is useful for membership changes but is not a replacement for reading changed content. Likewise, accepting an SSE event is not proof that downstream extraction finished. Persist extraction acceptance only after checking the resulting record against the source observation.

## Unexecuted acceptance fixture

| Synthetic scenario | Required evidence before adopting the design |
|---|---|
| Server supports resources but not subscriptions | Baseline/polling path works; no invented subscription success |
| Content changes without list membership changing | Content invalidation handled independently of list refresh |
| Notification identifies a child URI | Documented target resolution, no unauthorized widening |
| Two updates arrive during one read | Local generation remains dirty until reconciliation completes |
| Disconnect without replay support | Continuity marked unknown; fresh content reconciliation |
| Session expires, then capabilities differ | New initialization and subscriptions follow new capabilities |
| Resource access is revoked | Denied read blocks stale content reuse under the proposed policy |
| Extraction fails after successful read | Source observation advances; derived memory stays pending/stale |

All fixture cases are **NOT RUN**. They require an explicitly authorized disposable server/client and synthetic resources. No real memories or account data were used.

## Engineering, compatibility and cost boundaries

This is a protocol design reference, not a product shortlist or deployment recipe. MCP provides JSON-RPC resource access over transports including stdio and Streamable HTTP; the schema examined is TypeScript. No SDK, language runtime or dependency version was selected. An implementation needs a compatible client/server, negotiated resources support, authorized read access and application-owned cache/extraction storage. HTTP replay requires actual server support; no automatic connection was established.

The source tag resolves to commit `38c84e9f93ad191d9eb26d92b945d17bd0efcaf3`, dated 2025-11-25T21:14:08Z. The pinned schema retains an internal DRAFT-2025-v3 constant despite its versioned path; only the cited resource types were used, not that constant as a negotiated version. Rolling website pages may receive corrections independently. No latest SDK/release or maintenance-health claim is made. [S6: commit](https://github.com/modelcontextprotocol/modelcontextprotocol/commit/38c84e9f93ad191d9eb26d92b945d17bd0efcaf3)

At this pinned commit the repository LICENSE is MIT, with notice preservation and warranty disclaimers. That does not establish licences for every implementation, hosted service, later contribution or third-party asset. No substantial source code or protected assets are reproduced. [S5: pinned licence](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/38c84e9f93ad191d9eb26d92b945d17bd0efcaf3/LICENSE)

Alternatives are bounded periodic reads, read-on-use caching, or a provider-specific revisioned change feed. Subscriptions can reduce polling but add connection state, coalescing, retry and invalidation work. Reads, extraction/model calls and retained versions introduce costs; no price or performance saving was measured. The official protocol examples are message illustrations, not a tested end-to-end demo here.

**Future application:** an original evidence dashboard could show separate badges for transport continuity, last accepted source revision and derived-summary freshness. Evaluate the fixture before adopting it. Documentary review cannot certify runtime, security, accessibility, integration or performance behavior.
