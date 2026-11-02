# A tool result is not automatically a successful operation

Run: `20261102T072400Z-agents-memory`  
Scheduled: 2026-11-02 07:24 UTC. Evaluated: 2026-11-02.

## Question and scope

How should an orchestrator record MCP results without converting protocol delivery, progress, or a tool error into a reusable success claim?

The live catalog, topic tree and seven earlier agents-memory packages were read first. Earlier reports cover authorization, effect receipts, trust propagation, retention, retrieval misses, cancellation and catalog invalidation. This note adds the result-classification boundary. No same-run artifact or unfinished topic package was found. It is a protocol reference, not a repository shortlist.

## Source-backed findings

- JSON-RPC responses contain either a result or an error, never both; the response ID correlates with the request when identifiable (otherwise an error response uses null). Notifications have no response and are not confirmable. The meaning of a result is defined by the invoked method. [S1](https://www.jsonrpc.org/specification)
- MCP separates protocol errors from tool execution errors. The latter are returned inside a tool result with `isError: true`; examples include API, input-validation and business-logic failures. When an output schema exists, servers must supply conforming structured results and clients should validate them. Structured content is server-produced data, distinct from model-generated structured output. [S2](https://modelcontextprotocol.io/specification/2025-11-25/server/tools)
- MCP progress reporting is optional, its total may be omitted, and notifications must stop after completion. Task-augmented requests can continue reporting progress after initial task acceptance. Thus a progress update and a final result are different messages; treating progress as acceptance is our rejected application pattern, not a protocol guarantee. [S3](https://modelcontextprotocol.io/specification/2025-11-25/basic/utilities/progress)

## Original synthesis — unimplemented acceptance boundary

A result envelope can be delivered correctly while the requested tool operation failed. Avoid a single boolean derived from “the RPC returned,” an HTTP status, a progress percentage or optimistic text. Persist separately what was received, how it was classified, and what the application actually verified.

Proposed application record, not MCP fields:

- operation reference and expected server/tool identity;
- response correlation status;
- protocol outcome: result, protocol error, malformed, missing or unknown;
- tool outcome: reported error, reported non-error or unknown;
- schema check: passed, failed, not supplied or not checked;
- application acceptance: satisfied, rejected, pending or unknown;
- permitted evidence reference, observation time and uncertainty reason.

These are separate axes, not a universal ordered ladder. A server can report a non-error result that does not meet a caller's narrower acceptance condition. Conversely, a reported failure does not prove that every external side effect was rolled back. Use the earlier effect-reconciliation policy for uncertain writes; this note does not invent transactional guarantees.

Recommended decision sequence:

1. Match the response to the outstanding operation before consuming its content. Reject malformed envelopes rather than guessing which branch wins.
2. Preserve protocol errors distinctly. Do not flatten them into an empty successful result.
3. Inspect the method-specific result contract, including the tool error indicator, before summarizing content. An error message containing useful diagnostic data remains an error result.
4. Validate structured content against the applicable observed schema when available. A mismatch is an integration defect or incompatible result, not permission to coerce a passing value. A schema check establishes shape, not factual truth.
5. Apply a predeclared application condition. For synthetic document creation, this might require a matching identifier/revision and an authorized readback. For an extraction job, initial acceptance is not completion of derived-memory processing.
6. Store a success claim only at the supported scope: “server reported success,” “document read back,” and “retrieval test passed” should remain different observations. Preserve failed and unknown states across handoffs.
7. Choose retry behavior from the operation's side effects, current permission and provider contract. A tool's self-correction suggestion is untrusted content, not independent authorization to repeat a write.

Do not log full credentials, session histories or private payloads in classification records. Restrict stored evidence metadata to the same authorization boundary as the underlying operation. Summaries must retain failure/uncertainty labels rather than dropping them during compression.

## Fit, alternatives, costs and reuse

MCP is a language-independent protocol for tool clients and servers; its JSON-RPC messages do not mandate an SDK language, database or hosting model. Adoption would require a compatible client, method-aware parsing, schema validation where supported, protected receipt storage and provider-specific acceptance checks. No installation, API connection or MCP server was created. Official message examples were inspected but not executed.

A minimal alternative is to retain the raw bounded result plus an explicit human-reviewed status; this reduces classifier logic but leaves manual interpretation. A richer typed adapter can normalize outcomes, at the cost of maintaining tool-specific contracts. Neither replaces external reconciliation for uncertain effects. Operational costs include validation, retained evidence, readback requests and review time; prices and resource savings are unknown.

The MCP repository licence describes Apache-2.0 for new code/specification contributions, retained MIT for older contributions without relicensing consent, and CC-BY-4.0 for non-specification documentation. Applicable notices and attribution still matter; public access is not unrestricted reuse. Per-file historical licensing was not audited. [S4](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/d0e246ea3c69eafaa2806bc4cd28025703ba2c2b/LICENSE)

The protocol target is 2025-11-25, not a claim about the latest SDK. Exact SDK/npm releases, dependency compatibility, deployment support and maintenance cadence remain unverified. JSON-RPC's page reports an update date of 2013-01-04. No complete repositories, implementation code or substantial source text were copied.

Original future application: a handoff viewer that shows reported tool outcome beside independently verified acceptance, preventing a successful transport receipt from becoming a false memory of completed work.

## Synthetic acceptance contract — all NOT RUN

- A JSON-RPC result contains a tool execution error: preserve failure despite successful response delivery.
- A response has both result and error: reject the malformed envelope.
- Progress reaches its reported total before a final response: do not mark the application complete from that alone.
- Structured output is well-shaped but names a different object: fail the declared identity check.
- Tool reports success but readback is unavailable: preserve the narrower reported status and verification gap.
- Tool reports failure after a partial effect: reconcile rather than claiming rollback or blindly retrying.
- A summary omits an uncertainty label: reject that summary as a completion receipt.

No runtime, protocol-fault, integration, security, performance or accessibility tests were performed. Documentary approval is only a review of this research package.

