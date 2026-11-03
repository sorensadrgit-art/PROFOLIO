# A closed listener is not a completed shutdown

Run: `20261103T004800Z-fullstack-engineering`  
Scheduled: 2026-11-03 00:48 UTC. Retrieved/evaluated: 2026-11-03.

## Question and novelty
How should a Node.js HTTP service distinguish connection draining from completion of application work during a planned shutdown?

This bounded reference addresses an ownership boundary, not a shutdown library shortlist. The live catalog, full-stack README, complete topic tree and twelve prior reports were inspected. No same-run artifact or unindexed unfinished full-stack directory was observed. Earlier stream and queue notes cover delivery/lease boundaries; this note instead owns planned service termination and dependency teardown.

## Source findings
- **S1 — HTTP closure has distinct levels.** `server.close()` stops new connections and closes non-active ones; since Node 19 it reaps idle connections. `closeAllConnections()` forcefully closes active HTTP(S) connections too, but not upgraded sockets such as WebSocket. When combined, call it after `close()` to avoid the documented new-connection race. `closeIdleConnections()` exists for idle cleanup, especially older-version compatibility. [HTTP API](https://nodejs.org/docs/latest-v24.x/api/http.html#serverclosecallback)
- **S2 — Process termination is separate.** On non-Windows platforms a SIGTERM/SIGINT listener replaces default exit behavior. SIGKILL cannot be handled. An `exit` listener is synchronous-only; queued asynchronous work is abandoned. `process.exit()` can cut off pending I/O, whereas `exitCode` selects the eventual status without itself doing cleanup. Platform behavior differs. [Process API](https://nodejs.org/docs/latest-v24.x/api/process.html#signal-events)
- **S3 — Completion callback scope.** The pinned v24.0.0 net documentation describes closure after connections end, with the close callback following the close event; a callback error can report that the server was not open. This is a server-lifecycle receipt, not an application transaction receipt. [net source documentation](https://github.com/nodejs/node/blob/v24.0.0/doc/api/net.md#serverclosecallback)
- **S4 — Reuse terms.** Node-origin code uses MIT-style permission with retained copyright/permission notices and warranty disclaimer. The licence file separately lists externally maintained libraries and their terms; do not treat the entire distribution as one unrestricted grant. [Pinned licence](https://github.com/nodejs/node/blob/v24.0.0/LICENSE)

The HTTP/process pages displayed v24.22.2. That label is not independent release-artifact verification or a claim about any installed runtime. Exact release/commit dates remain unknown. S3/S4 are deliberately pinned historical v24.0.0 references.

## Original synthesis — proposed, untested
Define shutdown as a small state machine with independently observable obligations:
`serving → draining → dependencies-closing → stopped`, with a separate `deadline-exceeded` outcome.

The proposed owner should:
1. Make the transition idempotent so repeated termination notifications cannot start competing teardown sequences.
2. Mark admission closed at the application boundary and request traffic withdrawal through the actual hosting adapter. Treat withdrawal propagation as unconfirmed until that adapter's documented condition is met; do not assume an instantaneous load-balancer update.
3. Initiate listener closure, then wait on two separate ledgers: transport drain and accepted application operations. Work detached from a response must either be explicitly registered or durably handed off before it can leave the local ledger.
4. Keep the database/client resources needed by accepted work available until that work settles or the deadline policy takes over. Closing a pool first can defeat otherwise orderly request draining.
5. Resolve long-lived upgraded sessions through their protocol owner's separate policy. Do not infer their completion from an HTTP socket-cleanup method.
6. After normal settlement, close owned outbound clients, timers and other resources; produce a sanitized outcome record. Preserve errors as errors rather than writing “graceful” after any callback.
7. At an explicit deadline, record outstanding categories and apply a documented forced-stop policy. Interrupted work may have an unknown external outcome. Reconcile durable operation identities after restart rather than blindly replaying mutations.

These are design recommendations, not guarantees supplied by Node. The operation ledger must avoid losing registration between admission and task creation; its empty state is only meaningful after new admission is closed. A timeout bounds waiting, not correctness. A socket disappearing does not prove a downstream write rolled back.

Use a deadline shorter than the host's actual termination allowance, with a measured reserve for dependency closure and diagnostics. The reserve is a project parameter, not a universal number. No shutdown duration has been measured here. Keep telemetry categorical (phase, outstanding count, elapsed time, outcome); avoid request bodies, cookies, credentials and full connection addresses.

## Engineering evaluation and alternatives
Purpose/technology: Node.js runs JavaScript server code; this proposal uses built-in HTTP/process APIs and existing application's database/client adapters. No npm package, MCP connection, API key or paid service is required by the reference. No installation or deployment was performed. Framework/serverless adapters may own the listener or hide signal handling; inspect the actual hosting contract rather than transplanting a bare-server recipe. This reference targets planned shutdown, not recovery from corrupted runtime state.

The official pages include examples, read but not executed. Version-specific API history is useful for compatibility review, but historical API availability is not a recommendation to deploy an unsupported runtime. Recheck the chosen release, framework lifecycle and platform termination policy before adoption.

Alternatives: let an existing framework own shutdown and expose only application drain hooks; move long-running operations to a durable worker with its own lifecycle; or use immediate termination with explicitly accepted interruption/reconciliation costs. None is benchmarked or certified here. Additional operational cost comes from overlap capacity during replacement, outstanding-work tracking, timeout handling and verification effort; amounts are unknown.

Original future application: a deployment-readiness checklist that records separate transport, business-work and dependency-close evidence, preventing a single “server closed” log from becoming a false release-success signal.

## Acceptance exercise — all NOT RUN
- Start an ordinary request and a registered detached task, then initiate drain; teardown waits for both obligations.
- Keep an upgraded session open; verify the separate session policy rather than assuming HTTP force-close handles it.
- Trigger termination twice; only one drain/cleanup sequence owns state.
- Introduce a hung operation and verify deadline classification, non-success outcome and safe reconciliation evidence.
- Delay traffic withdrawal and attempt new work during drain; admission behavior matches the documented contract.
- Fail dependency closure and preserve the failure; do not emit an unconditional clean-shutdown receipt.
- Test actual platform, runtime, framework, proxy, database driver and process supervisor together.

## Limitations and provenance
Four primary sources support the findings; Dot authored the synthesis. No repository code, assets or substantial protected text were copied. Runtime, browser, concurrency, integration, performance, security and accessibility tests were not run. The live net API URL was unavailable through the web tool; its pinned repository documentation was retrieved instead. Documentary QA is not permission to deploy or proof of graceful shutdown.

