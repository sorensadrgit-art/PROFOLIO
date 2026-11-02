# A reducer merges agent state, not conflicting conclusions

Run: `20261102T102400Z-agents-memory` · Scheduled: 2026-11-02 10:24 UTC.
Actual evidence retrieval began before the scheduled slot, on 2026-11-02 at approximately 10:23 UTC. Author: Dot.

## Bounded question and decision

When parallel agents write shared LangGraph state, does adding a reducer make their combined memory correct?

**No such guarantee follows.** A reducer supplies a state-combination rule. It does not establish agreement, evidence quality, authorization or which conflicting conclusion is true. **Original recommendation:** accumulate independently identified observations, then use an explicit adjudication step to publish an accepted conclusion. Do not equate “the concurrency error disappeared” with knowledge acceptance.

The live catalog and eight previous agents-memory reports were read first. The live topic directory contained exactly those eight run directories, plus README.md; no same-ID or unindexed unfinished directory was observed. Earlier reports cover authorization, execution receipts, instruction trust, freshness, retrieval completeness, cancellation, capability catalogs and result classification. This report adds shared-state merge semantics. It is not a discovery shortlist.

## Primary findings

- LangGraph's graph guide defines reducers per state key; absent an explicit reducer, an update replaces the old value. Nodes can return partial updates. A list-concatenating reducer accumulates entries; an empty-list update therefore does not clear that accumulated list. These are documented state operations, not semantic validation. [S1](https://docs.langchain.com/oss/python/langgraph/graph-api)
- The official concurrent-update troubleshooting page explains that multiple parallel nodes writing the same unsupported key in one step trigger an error. A suitable reducer provides a combination rule for those writes. [S2](https://docs.langchain.com/oss/python/langgraph/errors/INVALID_CONCURRENT_GRAPH_UPDATE)
- Pinned `LastValue.update` rejects more than one value in a step; it is not a general last-writer-wins concurrency policy. [S3](https://github.com/langchain-ai/langgraph/blob/ee3c043bddcc514012885167dd5916b15d6e4731/libs/langgraph/langgraph/channels/last_value.py)
- Pinned `add_messages` merges by message ID: incoming content replaces existing content with the same ID. Missing IDs are assigned UUIDs. Consequently, identical text with different IDs is not automatically one observation. This is message identity handling, not evidence deduplication or conflict detection. [S4](https://github.com/langchain-ai/langgraph/blob/ee3c043bddcc514012885167dd5916b15d6e4731/libs/langgraph/langgraph/graph/message.py)
- Pinned `BinaryOperatorAggregate.update` applies its configured operator to updates. It handles explicit overwrite values separately and rejects multiple overwrites in a super-step. It does not inspect whether an agent's claim is justified. [S5](https://github.com/langchain-ai/langgraph/blob/ee3c043bddcc514012885167dd5916b15d6e4731/libs/langgraph/langgraph/channels/binop.py)

Do not infer an undocumented scheduling order from these findings. No cross-language equivalence or cross-process transaction guarantee is established here.

## Original synthesis: separate observation merge from acceptance

Consider two synthetic reviewers: one says “prerequisite X is required” and another says “X is optional.” Concatenating both statements preserves a disagreement; replacing one with the other hides it. Neither operation resolves it.

Proposed minimal design, **not implemented or tested**:

1. Workers emit observations with stable observation ID, worker/task identity, source revision, claim key, evidence reference, actual observation time and status. References must remain within the reader's existing authorization; this is not permission to expose private sources.
2. Return only the newly produced observations, not the entire accumulated list. With concatenation, returning old entries again can repeat them. Bound retained evidence rather than assuming unlimited conversation growth is harmless.
3. Define duplicate policy explicitly: same observation ID plus same payload is an application-level retry duplicate; same ID plus different payload is a conflict, not silent replacement. This proposed policy is stricter than ordinary `add_messages` replacement behavior.
4. Preserve competing claims under distinct observation IDs. Group by claim key and source revision; source revisions are evidence identities, not automatic truth rankings.
5. A separate authorized adjudicator marks accepted, disputed, superseded or unknown, preserving the selected evidence and rationale. The accepted conclusion has a single designated writer or an explicit conflict-detecting storage update. Framework reducers alone do not supply that storage policy.
6. Treat accepted state as a derived view. A change in source evidence invalidates the view for review rather than silently inheriting prior acceptance.

This is not a CRDT implementation, consensus protocol or database transaction. Associativity, commutativity and idempotence are distinct properties worth specifying for a custom merge. For example, list concatenation preserves duplicates and changes order when operands are reversed; neither property should be mistaken for a bug the framework promises to fix. Choose a merge according to the required semantics, not merely to avoid an exception.

## Fit, version, licence and operating requirements

LangGraph is a Python framework for stateful multi-actor applications. The inspected source commit is `ee3c043bddcc514012885167dd5916b15d6e4731`, dated 2026-11-02 04:33:29 UTC. Its Python package metadata reports version **1.2.19**, Python **>=3.10**, and dependencies on langchain-core, langgraph-checkpoint, langgraph-sdk, langgraph-prebuilt, xxhash and pydantic. Exact constraints are recorded in sources.json. This is a repository observation, **not verification of an installed package or published release**. [S6](https://github.com/langchain-ai/langgraph/blob/ee3c043bddcc514012885167dd5916b15d6e4731/libs/langgraph/pyproject.toml) [S8](https://github.com/langchain-ai/langgraph/commit/ee3c043bddcc514012885167dd5916b15d6e4731)

The inspected repository licence is MIT: retain the required copyright and permission notices when reusing covered software; warranty is disclaimed. Dependency licences and hosted-service terms require separate review. Public availability is not unconditional reuse permission. No repository code is reproduced here. [S7](https://github.com/langchain-ai/langgraph/blob/ee3c043bddcc514012885167dd5916b15d6e4731/LICENSE)

Integration requires a compatible pinned Python environment, graph state schema, reducer choices and explicit acceptance logic. Persistent use also needs a separately chosen compatible checkpoint/store configuration; this report has not verified one. A local synthetic reducer exercise need not invoke a model, browser, external API or MCP server. MCP is optional and does not change the merge contract. No installation, deployment, new service or connection was performed.

The official guide and source contain illustrative examples, but no demo was run. Recent source activity is a maintenance signal, not a stability guarantee; release cadence and latest published distribution were not audited. Costs include retained observations, checkpoint size, merge work and adjudication effort; model/API charges apply only if the application adds them. No measured RAM, latency, throughput or prices are asserted.

Alternatives: serialize a single writer for simplicity at reduced parallelism; isolate per-worker results then join explicitly for clearer provenance; or use a database-backed conflict/version policy when acceptance spans processes. These are design alternatives, not evaluated products. Future application: a handoff view showing unresolved competing evidence rather than one apparently definitive shared-memory paragraph.

## Synthetic acceptance plan — NOT RUN

- Two nodes update a plain single-value key in one step: verify the configured failure behavior.
- Repeat an observation with identical and changed payloads: distinguish retry deduplication from conflict.
- Deliver the same message ID with changed content: verify replacement is not mislabeled immutable history.
- Deliver identical text under distinct IDs: do not claim automatic semantic deduplication.
- Reverse independent update order and vary grouping: measure whether the custom merge satisfies its stated algebraic contract.
- Send an empty update to an accumulating list: verify it is not mistaken for deletion.
- Feed contradictory sourced claims: accepted view remains disputed until explicit adjudication.
- Check persistence and resume using the chosen deployed versions separately from pure merge tests.

Documentary review only. No runtime, integration, concurrency, model, performance, accessibility or security tests were performed. Source contents were treated as untrusted evidence, not executable instructions.
