# A successful MCP connection is not a session test

Run: `20261101T183000Z-open-source-tooling` · scheduled 2026-11-01 18:30 UTC · evaluated 2026-11-01.

## Question and novelty

When should a tool-integration workflow use the MCP Inspector's one-shot CLI, and when does its experimental connection client justify a persistent process?

The live tooling directory contained two packages: browser-interface selection and agent evaluation harnesses. Neither examines MCP diagnostic session lifetime. No artifact for this run ID existed. This is one repository with two interfaces, not two competing products. It complements an evaluation harness: protocol evidence comes before judging an agent's prose.

## Verified project snapshot

[MCP Inspector](https://github.com/modelcontextprotocol/inspector) is maintained by the MCP project. The inspected main commit is `e3217388c8590bcf6796fd8d0528a875ade1ac1f`, dated October 28, 2026. GitHub's latest release endpoint returned **2.13.0**, published October 28 at 18:41:38 UTC. Its pinned root manifest also declares 2.13.0. npm's latest endpoint could not be retrieved in this run; npm artifact version, integrity and publication date remain unverified. [S1–S2, S9]

The package combines a TypeScript/Node core with a React/Vite web interface, Ink terminal UI, CLI and experimental `mcpdo` executable. Root dependencies include MCP client/core/server 2.4.0, schema validation, HTTP, keyring and file-locking packages. Node's declared minimum is >=22.19.0, but release notes explicitly recommend newer patched Node versions. An engine-compatible runtime is not automatically a security-current runtime. Neither dependency resolution nor a vulnerability audit was performed. [S1–S2, S9]

**Licence caveat:** the pinned LICENSE records a transition: new code/specification contributions use Apache-2.0, ordinary documentation uses CC-BY-4.0, and legacy contributions without relicensing consent remain MIT. The manifest says to consult LICENSE; GitHub classifies it as Other. Do not simplify this to “everything is MIT” or “everything is Apache.” Review applicable file notices and dependencies before redistribution. No upstream code, documentation corpus or demo assets are included in this publication. [S3]

## Two interfaces, two evidence contracts

| Interface | Documented lifecycle | Suitable evidence | Important limit |
|---|---|---|---|
| Inspector CLI | Connect, perform one named request, disconnect | A repeatable initialization/list/call probe with machine-readable output | Repeated invocations do not prove continuity in one connection |
| Experimental mcpdo | A local daemon keeps named connections open between commands | Multi-step task/subscription/elicitation flows where connection continuity matters | Another long-lived process, auth store and cleanup obligation |

The CLI supports stdio and remote HTTP/SSE targets. A successful tool listing demonstrates discovery, not that a tool can run or return correct output. In v2, a tool result with `isError:true` has nonzero exit code 5 even though its payload is printed. Automation should inspect both process outcome and returned structure. `--config` describes a read-only session configuration; `--catalog` selects a writable catalog. “Read-only configuration” does not make the server's operations read-only. [S4, S7]

mcpdo is explicitly experimental and bundled in the same package. It is useful when separately launched CLI processes would lose the state being examined. Its daemon is started on demand; disconnecting the last connection leads to idle shutdown after approximately 60 seconds. A crash loses live connections, requiring reconnection. This lifecycle is not durable workflow recovery or exactly-once execution. [S5, S10]

## Security and integration boundaries

The web backend can launch stdio processes. Its page exposes the backend token to clients that can load it, so a token is not a substitute for restricting network reachability. stdio processes run with the launching user's authority. mcpdo's private mode separates shell connection state, not hostile processes sharing the same user. A local diagnostic tool is not an OS sandbox. [S10]

Secret handling deserves a separate preflight. Where an OS keychain is unavailable, the documented fallback can be an unencrypted secrets file unless encryption is configured; explicit memory-only storage is another option, with persistence trade-offs. Header values can remain directly in configuration. Redacting some known fields does not establish that URLs, arguments or arbitrary tool results are safe to publish. Use synthetic fixtures and scrub artifacts before sharing. [S4, S6, S10]

No MCP server installation, OAuth grant or public deployment is needed for this documentary evaluation. A later actual test requires a compatible pinned Node/package environment, an authorized server, its transport/working-directory settings, and any independently approved authentication. Running a stdio target executes that target. Remote tools can have real external side effects.

## Original synthesis: use a three-stage diagnostic gate

1. **Contract:** establish the intended version/transport, connect, inspect advertised methods and schemas, then invoke one explicitly harmless operation. Retain actual result and error status.
2. **Continuity:** only if the feature depends on connection state, use a persistent client and test two ordered operations on that same connection. Separately test disconnect/reconnect behavior; do not infer persistence.
3. **Agent integration:** repeat the approved operation through the actual agent client. Inspector success does not prove that client's tool policy, model calling behavior or approval delivery.

Proposed later fixtures: a correct tool, a tool that returns `isError:true`, a deliberately unavailable method, and a stateful sequence that fails if every step reconnects. The repository documents synthetic test-server fixtures; their presence is reusable test-design evidence, not proof this evaluation ran them. [S8]

Prefer the one-shot CLI for basic CI checks. Evaluate mcpdo only when continuity is an explicit acceptance criterion. For custom protocol assertions, a small SDK-based test client is an alternative; for interactive inspection, the bundled web/TUI interfaces may be simpler. These are workflow-fit recommendations, not measured speed or reliability rankings.

Costs are local/CI compute, logs/storage and any server-side service usage. A model is not needed for a direct protocol probe; an agent-driven workflow adds inference usage. Persistent clients consume resources while connected. No pricing quote, account activation, benchmark or installation was performed.

## Limitations

Documentary research only. No runtime, security exploit, load, accessibility, interoperability or performance tests passed because none were run. Release metadata does not guarantee support. Local configuration, authenticated-client behavior and deployment suitability remain unverified. See sources.json, repos.json and qa.md for provenance and independent review.

