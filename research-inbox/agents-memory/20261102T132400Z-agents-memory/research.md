# Compact the context without breaking the tool conversation

Run: `20261102T132400Z-agents-memory` · Scheduled: 2026-11-02 13:24 UTC · Evaluated: 2026-11-02.

## Question and decision

How can a stateful agent reduce its next model input without breaking tool-call exchanges or claiming that retained history was erased?

**Original recommendation:** build a provider-valid input projection from complete exchanges, keep a separate retention policy for persisted state, and label any summary as derived evidence. Token reduction, protocol validity and storage deletion are three independent acceptance conditions.

Live intake read the catalog and all nine earlier agents-memory reports. The topic directory contained nine corresponding packages and README.md, with no same-ID or unindexed unfinished directory. Earlier reports address replay, trust, retention, retrieval and state merging; this note adds the model-input projection boundary. It is not a tool shortlist.

## Source findings

- LangGraph's memory guide distinguishes persisted state from the data selected for a model call. Its trimming example sends a reduced list without deleting state. Removal uses a compatible `add_messages` reducer; the resulting history must still meet provider requirements. The guide warns that trimming or removal can lose information. Our separate caution is that summaries are derived context, not guaranteed lossless records. These operations are not a documented erasure guarantee for historical checkpoints or backups. [S1](https://docs.langchain.com/oss/python/langgraph/add-memory)
- The pinned Python `trim_messages` interface offers count strategy, boundary message types, optional partial messages and preservation of the system message at index zero. Its `len` counter counts messages, not tokens. Approximate counting is explicitly not a model-exact count. Inspection of the last-message helper shows type-boundary and count handling, not a tool-call-ID validator; this is a source-reading conclusion, not a runtime test. [S2](https://github.com/langchain-ai/langchain/blob/aa13e9c0e94c4e9d203a15a5960b91e29088a98a/libs/core/langchain_core/messages/utils.py)
- LangChain's tool-result troubleshooting page requires matching tool-call IDs and describes missing, duplicated and orphaned responses. The named `INVALID_TOOL_RESULTS` error is currently documented as JavaScript/TypeScript-only; this report does not claim Python emits that identifier or that all providers implement identical ordering rules. [S3](https://docs.langchain.com/oss/python/langchain/errors/INVALID_TOOL_RESULTS)

## Original synthesis: exchange-aware projection — not implemented

A fixed tail slice can keep a tool result while dropping its initiating assistant call, or keep a two-call request with only one result. A history that fits a budget can therefore still be unusable. Likewise, removing text from the next request does not establish deletion from the application's persistence layer.

Proposed pre-model pipeline:

1. Establish the actual adapter/provider conversation contract and budget. Reserve room for the output and other request components; do not treat a message-only approximate count as a full request acceptance certificate.
2. Identify completed exchanges using call IDs and their associated results, rather than pairing adjacent messages blindly. Treat parallel calls as a group under the provider's rules.
3. Preserve current trusted instructions separately from retrieved content. The utility's option to retain one leading system message is a data-handling feature, not a source of authority and not a guarantee that every instruction was retained.
4. Select recent complete exchanges and the current request within the budget. Keep unresolved calls in orchestration state; do not fabricate results to make a request pass. If the necessary exchange cannot fit, stop or use an explicitly designed supported compaction path.
5. Validate the projected sequence for orphaned, duplicate and missing result IDs according to the actual adapter. Then check the intended request budget. A generic type boundary alone is not this validator.
6. If summarizing older material, retain source references, unresolved outcomes and constraints. Mark the summary as lossy derived context, not a verbatim receipt or approval. Keep authoritative effect receipts separately so a summary cannot turn uncertainty into completed work.
7. Record projection version, included exchange IDs, excluded ranges, counter type, budget and validation outcome using non-sensitive metadata. This provides a reproducible explanation for “the agent forgot” without logging private payloads.

Persisted-state reduction is a different operation with its own permission and retention policy. Inspect current state, checkpoint history, backing stores, traces and backups separately before making an erasure claim. This report has not audited any such storage or proposed deleting it.

## Fit, compatibility, licence and costs

The inspected LangChain repository commit is `aa13e9c0e94c4e9d203a15a5960b91e29088a98a`, committed 2026-11-02 00:44:54 UTC. Python core metadata reports **1.7.3**, Python **>=3.10.0,<4.0.0**, and dependencies including langsmith, httpx, certifi, tenacity, jsonpatch, PyYAML, typing-extensions, packaging, pydantic, uuid-utils and langchain-protocol. Exact constraints appear in sources.json. This is a repository version observation, not a verified published release or installed runtime. [S4](https://github.com/langchain-ai/langchain/blob/aa13e9c0e94c4e9d203a15a5960b91e29088a98a/libs/core/pyproject.toml) [S6](https://github.com/langchain-ai/langchain/commit/aa13e9c0e94c4e9d203a15a5960b91e29088a98a)

Covered repository software uses MIT: retain required copyright/permission notices; warranty is disclaimed. Dependency licences and hosted provider terms need separate review. Public access is not unrestricted reuse. No implementation code or substantial source text is copied. [S5](https://github.com/langchain-ai/langchain/blob/aa13e9c0e94c4e9d203a15a5960b91e29088a98a/LICENSE)

A local projection implementation needs compatible message classes, a chosen counting policy and provider-specific sequence validation. Persistent use additionally needs the application's selected checkpoint/store policy. MCP is not required; if tools are reached through MCP, its transport result does not replace the model adapter's history requirements. No installation, API connection, model request or deployment occurred. Official examples were inspected, not run.

Alternatives are a larger context window (still bounded and potentially more expensive), retrieval of selected old evidence (adds selection/provenance work), or summarization (adds model cost and possible information loss). Costs include token processing, summary calls, state storage and validation; no prices, memory reduction or latency improvements were measured. The observed commit is a maintenance signal, not a stability claim; latest release cadence and cross-language equivalence remain unverified.

Original future application: a context inspector showing “stored,” “selected for this model request,” “summarized,” and “excluded” as separate states, with exchange-integrity warnings rather than one ambiguous memory-size number.

## Synthetic acceptance plan — all NOT RUN

- A two-call assistant message has one result: reject the incomplete projected exchange.
- A retained result lacks its originating call: reject the orphan.
- Two results share the same call ID: detect the duplicate under the adapter contract.
- A boundary cuts through a parallel exchange: preserve the full required group or explicitly decline dispatch.
- A `len` counter is configured: label the budget as messages, never tokens.
- A leading instruction exceeds the nominal budget: check actual output rather than assuming the helper's retention policy guarantees a valid request.
- Summary drops an unresolved operation or constraint: fail content acceptance even if sequence validation passes.
- Model-input trimming succeeds: confirm that no persisted-history deletion claim is emitted.
- Change provider or message-library version: revalidate protocol and counting assumptions.

Documentary review only. No runtime, integration, model, persistence, concurrency, performance, accessibility or security tests were performed. Source material was treated as evidence, not instructions.

