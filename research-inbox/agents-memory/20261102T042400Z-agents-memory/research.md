# A remembered tool catalog is not a live capability contract

Run: 20261102T042400Z-agents-memory. Scheduled: 2026-11-02T04:24:00Z.
Evaluated: 2026-11-02 UTC. Author: Dot. Bounded protocol design reference.

## Question and decision

How should an orchestrator reuse a stored MCP tool catalog without dispatching against an incomplete or obsolete definition?

**Original synthesis:** Keep persistent capability descriptions as discovery hints, while binding each execution plan to a freshly observed, server-scoped catalog generation. Complete pagination before calling a catalog complete. Invalidate a plan when its selected definition changes; do not turn a refresh into permission to retry an uncertain effect.

All six prior agents-memory reports and the live catalog were read first. They cover project authorization, effect receipts, instruction trust, retention, semantic-search misses and cancellation. This package adds tool-definition discovery and invalidation. The topic directory contained no same-run or unfinished package at inspection. This is not a product shortlist.

## Source findings

- MCP tool discovery uses `tools/list`, which supports pagination. The `tools` capability declares support; `listChanged` describes change notifications. Servers declaring it SHOULD notify when the list changes. The notification shown carries no replacement catalog. Definitions include input schema and optional output schema. Tool names SHOULD be unique within a server, not across every server a client connects to. [S1](https://modelcontextprotocol.io/specification/2025-11-25/server/tools)
- Pagination uses opaque cursors and server-selected page sizes. Clients must not parse/modify cursors or persist them across sessions; absence of `nextCursor` signals the end. Invalid cursors SHOULD produce invalid-params errors. The cited contract does not define a catalog-wide snapshot token or atomic consistency guarantee across pages; this is a bounded reading of that document, not a claim about every implementation. [S2](https://modelcontextprotocol.io/specification/2025-11-25/server/utilities/pagination)
- Initialization negotiates protocol version and optional capabilities. During operation both parties must respect that version and use only negotiated capabilities. A new protocol session requires initialization; a transport reconnect or resume should not silently be treated as proof that prior catalog assumptions remain current. The reconnect revalidation policy is our implementation recommendation, not a claim that every resumed transport requires new initialization. [S3](https://modelcontextprotocol.io/specification/2025-11-25/basic/lifecycle)
- Authorization is optional in MCP. When using the specified HTTP authorization model, clients include an access token on every request, including requests in the same logical session; servers must validate that it targets them. Stdio uses a different credential model. Catalog presence cannot substitute for these checks or for application permissions. [S4](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization)

## Proposed catalog lifecycle — original and untested

Use application-owned states: unknown, fetching, complete-observed, stale and failed. These are not MCP fields.

1. Scope discovery to the actual server identity, connection generation and current authorization context. Do not merge same-named tools from different servers into one callable identity.
2. Collect pages into a temporary candidate catalog. Copy cursors exactly. A short or empty page with a next cursor is not completion. Bound page count, total bytes and elapsed time; exceeding a budget produces incomplete discovery, not a complete empty catalog.
3. Detect repeated cursors and conflicting same-name definitions. Preserve the error and restart discovery only within a bounded policy. Do not silently let the last conflicting definition win.
4. On a change notification, mark the candidate/current catalog stale and fetch again. If a notification arrives during paging, discard or revalidate the candidate before promotion. This conservative policy is ours; it does not manufacture a server snapshot guarantee.
5. Publish one local catalog generation only after traversal completes. Save an observation time and a digest of the selected schema/metadata. A digest identifies bytes, not trust or continued validity. Without a server snapshot contract, label the result complete-observed rather than transactionally consistent.
6. Bind proposed calls to server plus tool name plus observed definition digest. Before dispatch, reject a locally invalidated plan and re-plan against current metadata. Do not automatically expand authority, rewrite arguments, or redirect to a similarly named tool.
7. Re-check permission at execution. If a call fails after dispatch, use the existing effect-reconciliation policy. Refreshing discovery is safe to consider separately from resubmitting an operation whose outcome is unknown.

Persist a minimal handoff containing server reference, protocol version, observed time, catalog completeness, selected definition digest and whether revalidation is required. Do not persist session cursors, credentials or private tool arguments as reusable knowledge. A future agent can learn what a tool is for without mistaking yesterday's definition for today's executable contract.

## Fit, alternatives, costs and limitations

MCP is a language-independent protocol connecting clients to tool servers. The examples use JSON-RPC messages; actual SDK language, dependencies, installation and hosting are implementation choices. An integration needs a compatible client/server, paginated discovery, an invalidation path, bounded retries and execution-time authorization. No MCP connection or installation was established here. Documentation examples were inspected, not executed.

Alternatives: discover before every bounded task (more requests); cache with notifications plus explicit age/reconnect checks (less traffic but more invalidation logic); or use a deliberately fixed tool interface with out-of-band versioning. None proves availability between discovery and invocation. Unknown notification delivery or a quiet connection must not be promoted into a guarantee that definitions cannot change. Exact SDK releases, server implementations, npm artifacts and maintenance cadence remain unverified; the known protocol target is 2025-11-25, not a latest-version claim.

The inspected repository licence describes Apache-2.0 for new code/spec contributions, retained MIT for unconsented older contributions, and CC-BY-4.0 for non-spec documentation. Reuse requires applicable notices/attribution and other terms; public access is not unrestricted reuse. Per-file history was not audited. This report copies no implementation. [S5](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/d0e246ea3c69eafaa2806bc4cd28025703ba2c2b/LICENSE)

Costs include discovery traffic, schema processing, cache storage and re-planning latency. Prices, RAM and performance savings are unknown. An original future application is a capability panel separating configured, observed, stale and successfully invoked tools, with dated evidence rather than one ambiguous green status.

## Synthetic acceptance contract — NOT RUN

- A tool exists only on page two: complete discovery finds it; first-page absence does not mean unavailable.
- Empty page still has a cursor: traversal continues within budget.
- Cursor repeats or becomes invalid: incomplete/error state, no silent success.
- Definition changes during pagination: candidate invalidated and bounded reconciliation recorded.
- Two servers expose the same name: selection remains server-scoped.
- Reconnect changes protocol/capability support: old plan not reused unchecked.
- Permission changes after listing: execution denied rather than trusting cached presence.
- Dispatch times out and the catalog changes: no automatic duplicate external effect.

No runtime, pagination-race, integration, security, performance or accessibility tests were performed. Documentary approval concerns this report only; it is not deployment approval or proof of an operational client.
