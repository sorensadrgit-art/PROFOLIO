# MCP roots are scope hints, not a filesystem sandbox

Run: `20261102T222400Z-agents-memory`  
Scheduled: 2026-11-02T22:24:00Z. Evaluation: 2026-11-02 (started before the scheduled slot; slot retained).  
Author: Dot. Scope: one protocol/implementation decision, not a discovery shortlist.

## Main question

How should an agent remember filesystem scope without mistaking MCP roots for enforceable authorization, including the current protocol transition?

**Answer (original synthesis):** store scope observations separately from permission decisions. Re-establish server configuration and OS isolation before acting; neither a recalled root nor a protocol migration grants access.

## Source findings

1. The 2025-11-25 roots specification uses file URIs, a client roots capability, `roots/list`, and change notifications when supported. Its server-side path/boundary language is SHOULD, while clients have explicit access-control duties. A notification is not evidence that a particular operation was authorized. [S1](https://modelcontextprotocol.io/specification/2025-11-25/client/roots)
2. The current client guide explicitly calls roots coordination rather than security enforcement and places actual protection in OS permissions/sandboxing. This is a stated limitation, not a vulnerability discovered here. [S2](https://modelcontextprotocol.io/docs/2026-07-28/learn/client-concepts)
3. Roots became deprecated in protocol 2026-07-28. The registry recommends passing paths through parameters, resource URIs, or server configuration. Eligibility for removal starts with a revision on/after 2027-07-28, not an unconditional removal that day. Deprecation does not itself mean removal. [S3](https://modelcontextprotocol.io/specification/2026-07-28/deprecated)
4. The pinned reference filesystem README distinguishes connections: 2026-07-28 uses CLI directories; legacy clients can replace the allow-list via roots. It documents waiting for roots updates, denying symlink escapes, and a hard-link caveat: overwriting an allowed pathname also changes the same file under its other names. These are documentation claims, not results of our tests. [S4](https://github.com/modelcontextprotocol/servers/blob/194cc86e3ae6400958555bd4116849d822d69263/src/filesystem/README.md)
5. The inspected TypeScript startup resolves original/canonical directory paths, filters inaccessible directories and exits when supplied directories are all unusable. This corroborates configuration processing, not complete confinement. [S7](https://github.com/modelcontextprotocol/servers/blob/194cc86e3ae6400958555bd4116849d822d69263/src/filesystem/index.ts)

## Implementation fit, maintenance and constraints

The inspected server provides filesystem operations over stdio. Its repository manifest is version **2.1.0**, requires **Node >=24**, and lists `@modelcontextprotocol/server ^2.3.1`, diff, glob, minimatch and zod; TypeScript and Vitest appear in development tooling. Repository head was `194cc86e3ae6400958555bd4116849d822d69263`, dated 2026-10-28. This is evidence of recent repository activity, not an npm release date or ongoing support guarantee. npm latest metadata could not be retrieved; published version and release date remain unknown. [S5](https://github.com/modelcontextprotocol/servers/blob/194cc86e3ae6400958555bd4116849d822d69263/src/filesystem/package.json)

Licence is **not simply MIT**: the pinned LICENSE describes transition to Apache-2.0, retained MIT contributions without relicensing consent, and CC-BY-4.0 non-spec documentation. Preserve applicable notices and check the exact files/dependencies before redistribution; no blanket relicensing conclusion is made. No code/assets were copied into this package. [S6](https://github.com/modelcontextprotocol/servers/blob/194cc86e3ae6400958555bd4116849d822d69263/LICENSE)

**Untested integration recommendation:** use an explicitly pinned compatible server/runtime with a narrow configured directory and least-privilege execution identity. No cloud account, paid API or external memory service is required by this proposed local stdio arrangement. Costs are operator time, local compute/storage, maintenance and isolation testing; no measured or priced estimate. CLI configuration is the documented cross-version option in this implementation; it still is not an OS sandbox. The README supplies configuration examples, not a demo we executed. [S4](https://github.com/modelcontextprotocol/servers/blob/194cc86e3ae6400958555bd4116849d822d69263/src/filesystem/README.md)

## Original proposed pattern: filesystem scope admission receipt

This is a design proposal, not an MCP field schema:

- Record server build reference, observed protocol version, configuration revision, operation class and a logical workspace identifier.
- Record separately: intended scope, observed server allow-list, and independently verified execution isolation. Never compress these into one `authorized=true` memory.
- Keep sensitive absolute paths out of shared agent memory; store a private resolvable workspace reference instead.
- Invalidate an observation after reconnect, configuration/root change or server upgrade. A saved observation can explain a prior decision, not approve the next one.
- Gate writes on current user authority plus validated execution policy. Treat unexpected protocol/configuration mismatch as a blocked admission, not an invitation to expand directories.
- Record unknown evidence as unknown; do not invent an isolation receipt from a successful tool response.

**Alternatives (original evaluation):** an application-owned narrow file API offers a smaller operation surface but requires engineering; direct process filesystem access is simpler but still needs explicit OS controls; legacy dynamic roots can coordinate trusted clients but add a migration obligation. Neither transport nor scope syntax solves confidentiality by itself.

**Future application:** a multi-agent workspace broker could give each worker a short-lived logical workspace handle and retain a private policy-revision receipt. Shared memory would carry provenance and operation outcomes, not reusable path authority.

## Unexecuted acceptance cases

| Case | Proposed expected behavior | Status |
|---|---|---|
| Recalled workspace differs from live configuration | Block and refresh admission evidence | Not run |
| Legacy root is withdrawn while operation queues | Verify documented update ordering with a controlled fixture | Not run |
| 2026-07-28 connection relies only on remembered legacy roots | Require explicit supported configuration | Not run |
| Symlink points outside scope | Verify rejection in isolated fixtures | Not run |
| In-scope file has another hard-link name | Detect/restrict risky writes according to policy | Not run |
| OS permissions deny path accepted by app policy | Surface denial; never elevate automatically | Not run |

## Novelty and limitations

Compared with the 12 existing agents-memory packages, this addresses filesystem scope enforcement and roots migration, not project-label ACLs, replay receipts, prompt injection, search absence, TTL, cancellation, tool discovery/results, reducers, compaction, resource sync or sampling. Live catalog and topic directories had no same-run artifact at intake. Existing packages are preserved.

Bounded documentary review only. No package installation, server connection, runtime, race, symlink/hard-link, penetration, integration, performance or accessibility test was performed. No claim that the reference server is securely confined, production-ready, or that all deployed clients support the current protocol. Source pages may change; repository links are commit-pinned. This task's approval is not Gemini approval or deployment permission.

