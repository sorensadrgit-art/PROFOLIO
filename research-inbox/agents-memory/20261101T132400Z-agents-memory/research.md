# Resuming an agent is not exactly-once execution: checkpoint and effect receipts

Run: `20261101T132400Z-agents-memory`  
Scheduled: 2026-11-01 13:24 UTC / 08:24 America/New_York (EST)  
Evaluated: 2026-11-01 UTC

**Main question:** How should an agent resume interrupted work without accidentally repeating an external effect?

**Original synthesis:** A saved reasoning state and an external operation's committed result are different facts. Preserve both a checkpoint reference and an operation receipt. If the external outcome is uncertain, reconcile it rather than declaring success or starting a new operation. This narrows an unanswered reliability question; the earlier [project-authorization reference](../20261101T124211Z-agents-memory-smoke/research.md) covers who may access memory, not replay semantics.

## Verified documentary findings

1. **Two different memory mechanisms.** LangGraph distinguishes thread-scoped graph checkpoints from cross-thread application stores. Its in-memory checkpointer loses checkpoints on process restart; persistence must be selected deliberately. A handoff summary in a store is not itself the graph checkpoint required to resume a thread. [S1: Persistence](https://docs.langchain.com/oss/python/langgraph/persistence)

2. **Checkpoint timing matters.** LangGraph documents `exit`, `async` and `sync` durability modes. Exit defers persistence until execution exits; async permits the next step while persistence proceeds; sync writes before continuing. Before the first durable checkpoint, a crash may be indistinguishable from a never-invoked thread. The documentation proposes a separate admission record when that distinction matters. These statements concern graph state, not an atomic transaction spanning an external API. [S2: Checkpointers, durability modes](https://docs.langchain.com/oss/python/langgraph/checkpointers#durability-modes)

3. **Resume repeats node prefixes.** When resuming an interrupt, LangGraph restarts the containing node from its beginning. Code before the interrupt runs again. The same thread ID selects the interrupted state; a new thread ID starts separate state. Therefore placing a non-idempotent effect before a pause requires deliberate handling rather than assuming execution continues at a machine instruction. [S3: Interrupts](https://docs.langchain.com/oss/python/langgraph/interrupts)

4. **Replay is execution, not a recording.** Time travel resumes from a prior checkpoint: later nodes, API requests and model calls execute again and may differ. Interrupts fire again. Updating state creates a new checkpoint rather than mutating the original history. Do not mistake a fork for undoing external effects from the original run. [S4: Use time-travel](https://docs.langchain.com/oss/python/langgraph/use-time-travel)

5. **Idempotency is provider-specific.** Stripe's API v1 saves the first response after execution begins, including a 500 response, for a supplied key. A reused key with changed parameters errors; pruning after at least 24 hours can make reuse a new request. Validation and concurrent-conflict failures before execution are not saved. Its API v2 has different retry semantics. This is a concrete API example, not a promise about every tool or memory service. [S5: Idempotent requests](https://docs.stripe.com/api/idempotent_requests)

## Proposed receipt pattern — original design, not executed

For one permitted external operation, persist a minimal record:

```text
operation_id: stable opaque identifier for this intended effect
input_fingerprint: digest of canonical non-sensitive inputs
checkpoint_ref: thread and checkpoint references
state: proposed | submitted | confirmed | uncertain | rejected
provider_receipt: verified external object/result reference, or null
verified_at: actual observation time, or null
next_action: reconcile | retry_same_operation | stop
```

Treat the record as context, not authorization. Re-check current permission before an effect. Avoid putting secrets or personal identifiers into keys, fingerprints' source logs, or receipts. A hash does not sanitize the underlying data or provide confidentiality.

Recommended control flow:

- Validate and admit the intended operation with a stable identity.
- Reuse that identity on an authorized retry only if inputs match and the provider's idempotency contract still applies.
- After a timeout, use an authenticated result lookup or another documented reconciliation mechanism. If unavailable, leave the outcome uncertain and stop automatic retries that could duplicate an effect.
- Mark confirmed only after verifying the external result; save the graph checkpoint independently.
- On handoff, provide both references and the unresolved state. The next agent must not infer external completion from a narrative summary.

Do not claim distributed exactly-once behavior from this design. If two clients can race, coordination must be enforced by the chosen storage/provider; an instruction to avoid overlap is insufficient.

## Proposed disposable acceptance tests — NOT RUN

| Failure point | Required evidence |
|---|---|
| Crash before first checkpoint | Admission distinguished from completion, or explicitly unknown |
| External effect commits, response is lost | Reconciliation finds original result without a duplicate |
| Pause after an effect | Resume does not repeat an uncontrolled node-prefix side effect |
| Same operation ID, changed inputs | Rejected rather than silently replayed |
| Retry outside provider key retention | No claim of deduplication; explicit reconciliation |
| Replay from an earlier checkpoint | New execution identified; old effects not presumed undone |
| New receiving session | Correct checkpoint plus receipt retrieved; current authorization rechecked |

Debugging tip: log state transitions and opaque correlation IDs rather than full payloads. Count actual external objects, not merely successful graph runs. A successful recovery test using an in-memory fake does not establish process-restart durability.

## Limitations and next decision

This is bounded documentary research, not a framework benchmark, crash test, integration certification or deployment recommendation. No packages, services or APIs were executed. Sources are rolling documentation observed on the evaluation date; exact LangGraph package version, documentation commit and publication dates remain unknown. Pin and re-check the intended runtime before implementation. Stripe API v1 behavior must not be generalized to v2. No software-license or hosting-price comparison is made because this is a design reference, not a tool shortlist. Additional storage, API calls and retention introduce operational cost; no costs were measured.

Next authorized evaluation, if separately requested: implement the table in a disposable synthetic workflow with a provider-specific receipt contract.

