# A sampling request is not a delegated permission grant

Run: `20261102T192400Z-agents-memory` · Scheduled: 2026-11-02 19:24 UTC · Evaluated: 2026-11-02 UTC.

## Question and decision

What should an orchestrator record and enforce when an MCP server asks its client to generate a model response, possibly proposing tools or requesting wider context?

**Original recommendation:** separate the permission to obtain a generation from permission to disclose context, return generated content, execute a proposed tool, and persist the resulting memory. Record actual model selection and accepted effects rather than treating a sampling response as a delegation receipt.

Live intake read eleven existing agents-memory reports and the catalog. No same-run package or unindexed unfinished topic directory was found. Prior work covers replay, result interpretation, memory provenance and context compaction; this report adds the server-initiated sampling boundary. It is a protocol reference, not a tool shortlist.

## Source findings

1. **Support is negotiated, not assumed.** Basic sampling and tool-enabled sampling have distinct capability declarations. A server must not send tool-enabled requests without the client's sampling-tools support. The sampling guide recommends request/response review, rate limiting and loop limits; its human-review wording is SHOULD, not a universal mandatory UI. Cross-server context inclusion values are soft-deprecated. The illustrative loop returns proposed tool uses to the server, which executes tools and sends another sampling request with results. Tool choice controls generation, not an application permission system. That last distinction is our inference. [S1](https://modelcontextprotocol.io/specification/2025-11-25/client/sampling)

2. **Requests can differ from what was actually used.** The pinned schema allows the client to ignore model preferences, modify or omit the supplied system prompt, and ignore requested context inclusion. `maxTokens` limits requested generated tokens for that request; the client may generate fewer. The result reports the generating model and an optional, extensible stop reason. A tool-use stop reason therefore needs interpretation, not promotion to completed external work. The schema comments require the client to return an error for tools or toolChoice when the corresponding capability is absent. [S2](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/38c84e9f93ad191d9eb26d92b945d17bd0efcaf3/schema/2025-11-25/schema.ts)

3. **The host owns the information boundary.** MCP architecture assigns consent and security-policy enforcement to the host. It describes server isolation and necessary context rather than whole-conversation access. This is an architectural responsibility, not proof that a particular deployment enforces it. [S3](https://modelcontextprotocol.io/specification/2025-11-25/architecture)

4. **Bound the outer operation too.** Lifecycle guidance requires respecting negotiated capabilities. It recommends request timeouts and a maximum timeout even when progress resets a local timer. Our conclusion: one generation's token ceiling cannot serve as the whole nested workflow's time, spending or effect budget. [S4](https://modelcontextprotocol.io/specification/2025-11-25/basic/lifecycle)

## Original design: a sampling admission envelope — unimplemented

Keep a compact application-owned record, not a new claimed MCP standard:

- Parent operation ID, requesting server identity and negotiated capability snapshot.
- Explicitly permitted context references and their revisions; do not log secret or private prompt payloads.
- Requested model hints separately from the reported selected model; selected model is still a provider/client report, not independent execution attestation.
- Review decision and scope for generation, content disclosure, tool effects and memory persistence separately.
- Per-request allowance plus parent-level cumulative call, elapsed-time and spending limits.
- Proposed tool-use IDs, actual execution receipts and accepted output references, each with its own status.

Recommended gate sequence:

1. Identify the already-authorized parent operation and the server connection. Reject unrelated or over-broad sampling requests rather than accepting the server's prompt as new user authority.
2. Check supported version and capabilities. Capability support answers whether a feature is understood, not whether this user permits this request.
3. Select the minimum context allowed for the task. Do not send another project's memories merely because `allServers` was requested. Inspect metadata and embedded content as part of the same disclosure boundary.
4. Apply the host's real model and cost policy. If model choice matters to acceptance, compare the returned model with that policy; never record the requested hint as the model actually used.
5. Review the generation before disclosing it back to the requesting server under the application's configured consent rules. A generated response may itself repeat confidential context.
6. Treat tool-use blocks as proposals. Validate tool name, arguments, current permission and provider-specific effect semantics before execution. For a denied or failed operation, preserve an accurate error/outcome rather than manufacturing success to advance the loop.
7. Enforce a parent-level budget across continuations, retries and nested calls. A new sampling request must not reset the entire workflow allowance. A forced final-text turn is not a substitute for enforcing the effect budget.
8. Persist reusable conclusions only after evidence checks. Retain provenance to the accepted response and actual effect receipts. A sampling completion alone does not establish that a tool ran, that an answer is correct, or that a memory write completed.

These gates are original implementation recommendations, not tests or claims of protocol-enforced security. A malicious or faulty server must not be allowed to upgrade its own approval labels. External effects and persistence need their own authorization boundary even if a human approved a model call.

## Synthetic acceptance exercise — all NOT RUN

| Case | Required observable result |
|---|---|
| Client advertises sampling without tool support | Tool-enabled request rejected; no silent capability upgrade |
| Request asks for other-server context | Host denies or narrows disclosure according to existing permission |
| Client chooses a different model than requested | Receipt distinguishes hint from selected model |
| Approved generation proposes a disallowed write | No write; proposal and rejection remain distinguishable |
| Repeated continuation stays below individual token limits | Parent cumulative limit still stops runaway work |
| Generation ends with toolUse | No claim that the proposed tool has already completed |
| Sampled response contains a sensitive source excerpt | Return-to-server disclosure gate applies |
| Tool returns success text without an accepted receipt | Memory remains reported/unverified under this design |

Use synthetic data and a disposable client/server only after separate implementation authorization. Inspect actual effects and retained records, not merely the model's narrative.

## Engineering, licence, compatibility and costs

MCP uses JSON-RPC and a client-host-server arrangement; the inspected type definitions are TypeScript. This report chooses no SDK, model provider, package runtime or dependency set. Implementation needs a client with the relevant sampling capabilities, a compatible server, a model-access path and application-owned permission/budget/receipt storage. Authentication and billing for that model path are client/host deployment concerns; “no server API key necessary” does not mean free inference. No new account, paid service, MCP connection or install was performed.

Protocol revision is **2025-11-25**. The pinned repository commit is `38c84e9f93ad191d9eb26d92b945d17bd0efcaf3`, committed 2025-11-25T21:14:08Z. The schema file retains an internal DRAFT-2025-v3 constant; this report uses its cited sampling types, not that constant as a negotiated runtime version. Rolling documentation can change independently. No latest SDK release, deployment compatibility or maintenance-health certification is claimed. [S6](https://github.com/modelcontextprotocol/modelcontextprotocol/commit/38c84e9f93ad191d9eb26d92b945d17bd0efcaf3)

The pinned repository licence is MIT: preserve required notices; warranty is disclaimed. This does not grant rights to unrelated models, hosted services or third-party assets. No substantial source text or implementation is copied. [S5](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/38c84e9f93ad191d9eb26d92b945d17bd0efcaf3/LICENSE)

The official message examples illustrate the flow; no demo was executed. Costs include model tokens, repeated sampling, review latency and receipt storage; none were measured. Alternatives include host-owned orchestration with no server sampling, deterministic server processing, or a separately authorized model service with its own credentials and policy. These alternatives trade flexibility for simpler boundaries or additional operational responsibility; none was benchmarked.

Original future application: a handoff inspector showing generation accepted, context disclosed, tool proposed, effect verified and memory accepted as separate stages. Evaluate the synthetic exercise before adoption. No runtime, integration, security, accessibility or performance tests were performed.
