# Cancelled is not evidence that an agent's external work stopped

Run: `20261102T012400Z-agents-memory`  
Scheduled: 2026-11-02 01:24 UTC · Evaluated: 2026-11-02 UTC

**Question:** What may an orchestrator safely conclude after cancelling an MCP operation?

**Original synthesis:** Separate the user's stop intent, the protocol cancellation state, and the external effect's observed outcome. A cancellation receipt is not a rollback receipt. This extends the earlier [checkpoint/effect reference](../20261101T132400Z-agents-memory/research.md) with the specific cancellation race and task-retention contract; it does not repeat the prior filtered-search or factual-freshness studies.

## Source findings

- Ordinary MCP cancellation is an optional notification for an outstanding request. Receivers should stop processing and free resources, but may ignore it when the request is unknown, already finished or cannot be cancelled. Late responses race with cancellation; senders should ignore those responses. Task-augmented requests instead require the dedicated task cancellation request. [S1](https://modelcontextprotocol.io/specification/2025-11-25/basic/utilities/cancellation)
- Tasks are experimental in protocol version 2025-11-25. Capability negotiation and tool-level support govern their use. For valid cancellation, the receiver must transition to `cancelled` before responding, while stopping execution is a SHOULD. That state must remain even if execution subsequently completes or fails. Already-terminal tasks reject cancellation with `-32602`. Cancelled tasks may be deleted immediately; needed information should be retrieved beforehand. Initial task acceptance is separate from the eventual operation result. [S2](https://modelcontextprotocol.io/specification/2025-11-25/basic/utilities/tasks)
- Streamable HTTP disconnection should not be interpreted as cancellation. Event-stream resumption is optional; a dropped stream alone does not establish an operation outcome. MCP uses JSON-RPC over stdio or Streamable HTTP, among possible custom transports. [S3](https://modelcontextprotocol.io/specification/2025-11-25/basic/transports)
- Tool results can contain structured content and an output schema. When supplied, the server must conform and clients should validate. Schema-valid data is still a result representation, not independent evidence that an external effect was reversed. The latter distinction is our synthesis. [S4](https://modelcontextprotocol.io/specification/2025-11-25/server/tools)

## Proposed cancellation record — original design, not a protocol extension

Keep a small application-owned record rather than overwriting one ambiguous `done` flag:

- `operation_id`: stable identity of the intended operation, distinct from an RPC request or task ID.
- `stop_requested_at`: actual user or authorized controller stop time.
- `protocol_state`: observed state and observation time, or unknown.
- `effect_state`: not observed / committed / partly committed / verified absent / unknown.
- `effect_receipt`: authorized external record reference, or null.
- `reconciliation_state`: pending / verified / blocked, with a narrow next step.

These fields are proposed application semantics, not MCP fields. No secret values, complete payloads or raw logs belong in the record. Store only the minimum necessary references under existing access controls.

Recommended handling:

1. Stop dispatching further dependent work when the user withdraws permission. Do not interpret a background completion as renewed permission for a follow-on action.
2. Select the cancellation mechanism from the negotiated protocol and actual invocation type. Do not send both forms speculatively.
3. Preserve already-known correlation references at admission. Do not postpone an urgent stop merely to collect a comprehensive diagnostic export; any pre-cancellation lookup must be bounded and permitted.
4. Record the response accurately. If it is lost, leave protocol state uncertain. If cancellation is rejected because work is terminal, reconcile that terminal outcome rather than claiming cancellation succeeded.
5. For an operation that could mutate external state, use the provider's authorized read-only lookup to establish what actually exists. A missing task record after cancellation is not evidence that an external artifact disappeared.
6. If no reliable lookup exists, report uncertainty and do not automatically resubmit. Compensating actions are new effects requiring their own scope and permission; they may not restore the original state exactly.
7. A UI can say “cancellation recorded; external outcome unverified.” Avoid “nothing changed” unless independently supported.

## Fit, compatibility, alternatives and costs

This is a protocol design reference, not a software shortlist. MCP connects model-facing clients and tool servers; implementation language, SDK and dependencies are choices, not fixed by these documents. There is no installation or hosting step for this report. Integration would require compatible client/server negotiation, supported cancellation behavior, protected correlation storage and provider-specific outcome reads. Do not assume a client supporting ordinary MCP also supports experimental tasks. SDK versions, deployed capability support, npm artifacts and endpoint behavior remain unverified.

The repository's inspected license describes a transition: new code/specification contributions are Apache-2.0; unconsented older MIT contributions retain MIT; non-specification documentation contributions use CC-BY-4.0. It is not a blanket unrestricted-reuse grant. Check applicable attribution, notice, modification and redistribution conditions before copying material. This report contains original analysis, not source copies. [S5](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/d0e246ea3c69eafaa2806bc4cd28025703ba2c2b/LICENSE)

Alternatives to evaluate are ordinary request cancellation for short operations, negotiated tasks for deferred results, or a provider-native job API with explicit stop/result semantics. None automatically supplies transactional rollback. Documentation includes illustrative message flows; no demo was executed. The specification version is known, but source-page publication/commit dates and runtime release alignment are unknown. The observed license repository revision is not proof that any SDK implements these behaviors.

Operational costs include retained receipts, bounded status/outcome reads and reconciliation work. Prices and resource savings are unknown. Future application: an agent handoff panel showing stop intent, protocol state and externally verified outcomes separately, preventing a cancelled task label from being reused as a false “safe to retry” signal.

## Synthetic acceptance contract — NOT RUN

- Cancel races with an external commit: show the committed effect even if protocol state stays cancelled.
- Cancel response is lost: preserve uncertainty and reconcile without blind resubmission.
- Task already terminal: handle rejection distinctly from successful cancellation.
- Task record is immediately purged: use retained correlation references, not absence inference.
- Stream disconnects without cancellation: do not claim work stopped.
- Late response arrives: follow protocol response handling without losing separately observed external receipts.
- Unsupported task capability: do not fabricate task cancellation support.

No runtime, integration, crash, cancellation-race, performance, accessibility or security tests were performed. Documentary QA concerns this research package only; no deployment or external-memory changes were made.
