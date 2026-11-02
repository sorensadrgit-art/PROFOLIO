# A pending Transition is not a request-ordering policy

Run: `20261102T004800Z-fullstack-engineering`  
Scheduled: 2026-11-02 00:48 UTC. Research began at 00:43 UTC after early delivery. Evaluated: 2026-11-02.

## Question and novelty

How should a React interface keep an older asynchronous response from replacing the result of a newer user intent?

Supporting question: When should a UI preserve every action in order rather than accept only the latest read result?

The live catalog and all four previous full-stack packages were read first. They cover cache invalidation, database lost updates, pool budgets and index migrations. This note concerns client-side async result ownership, not database concurrency or a repository shortlist. No same-run artifact or unfinished topic directory was observed.

## Source findings

1. **Scheduling does not establish ordering.** React documents that async Actions inside a Transition can complete out of order. Its quantity demo deliberately exposes this race. Higher-level abstractions can handle common ordering needs; custom async transition layers must define their own policy. `isPending` describes pending Transition work, not which network result belongs to the latest intent. [S1](https://react.dev/reference/react/useTransition)
2. **A serial action queue is a specific policy.** `useActionState` queues dispatches sequentially and passes the preceding result to the next reducer Action. Dispatch belongs inside an Action, for example through an Action prop or a Transition. An exception cancels queued actions and reaches an Error Boundary. This is not interchangeable with discarding obsolete search reads. [S2](https://react.dev/reference/react/useActionState)
3. **Read effects need cleanup ownership.** React's fetch example ignores results after that Effect's cleanup, preventing obsolete responses from publishing. Cleanup runs before the next setup when dependencies change and after removal. Development Strict Mode adds a setup/cleanup stress cycle. The documentation recommends framework fetching or a client cache where appropriate instead of hand-building fetching in Effects. [S3](https://react.dev/reference/react/useEffect)
4. **Input priority and result priority differ.** Transition updates cannot control text inputs. The current documentation requires another Transition wrapper for state updates after an awaited request. The standalone `startTransition` provides no pending flag. These are scheduling constraints, not substitutes for request identity. [S4](https://react.dev/reference/react/startTransition)

## Original synthesis — proposed, not tested

Choose the semantic policy before choosing a Hook:

| Interaction | Proposed ownership policy | Do not infer |
|---|---|---|
| Search suggestions for the currently typed query | Latest eligible read result wins | Completion order equals intent order |
| Ordered edits whose next value depends on the previous result | Explicit sequential Action queue | One browser queue serializes all database writers |
| Independent uploads | Per-item identity and status | A single pending boolean identifies every upload |
| Route or account changes | Invalidate the old view's publication authority | Unmount proves remote work was rolled back |

For a custom search layer, maintain separate **requested identity**, **displayed identity**, **pending identity** and **error identity**. Identity should include the query plus relevant filters and authorized view scope. A monotonic request generation distinguishes repeated identical queries separated by other intent. Do not embed credentials in identifiers or telemetry.

At the point that current intent changes, invalidate the old generation. Every completion path, including error and final cleanup, must prove it still owns the current generation before altering current UI state. A stale success must not replace the new data; a stale failure must not replace the new error; an old finally handler must not clear a newer spinner. If old results remain visible while a new read is pending, label them as previous results rather than pretending they answer the new query.

This is a proposed application contract, not a ready-made React API. An Effect-local cleanup guard can cover that Effect's lifetime; a custom event-driven request manager needs its own corresponding lifetime and invalidation design. Avoid mixing two owners that disagree about when intent changes. Prefer the framework's existing request/cache ownership if it meets the requirement, and test its actual semantics rather than adding a competing cache.

For writes, hiding an old response is only a presentation decision. It does not undo a server effect or solve cross-tab concurrency. Use the earlier full-stack write-boundary and commit-reconciliation references for those separate obligations. Similarly, cancellation of local interest must not be reported as confirmed remote rollback. A serial client queue may be useful but still needs server authorization, validation and the intended concurrency contract.

## Compatibility, reuse, alternatives and cost

The official reference pages displayed **React v19.3**. This is a documentation label, not a verified installed or npm artifact version. Release/commit dates and this application's dependency lockfile are unknown. Recheck the installed React/renderer/framework combination before using an API, especially async Actions. Documentation demos were read, not executed; no maintenance cadence or latest-release claim is made.

The technologies are React Hooks and JavaScript/TypeScript application state; no additional service, MCP connection or new paid tool is required for the proposed design. Existing API authentication remains application-owned. Framework loaders, a compatible client cache, or a deliberately serialized interaction are alternatives; this note does not evaluate or endorse a particular third-party cache package. API traffic, redundant reads and queue latency are operational costs to measure. No hosting prices or performance gains were established.

The actual React repository license is MIT, with notice-preservation conditions and warranty disclaimers. Public code is not restriction-free. No React implementation, demo assets or substantial source text is copied here. [S5](https://github.com/react/react/blob/main/LICENSE)

Original future application: a development-only request timeline showing requested generation, accepted result generation and rejected stale completions, without storing response bodies. It could make a stale-result bug reproducible without confusing a spinner with a correctness receipt.

## Proposed acceptance checks — all NOT RUN

- Resolve old query A after new query B: only B may become the current result.
- Reject A after B succeeds: no obsolete error replaces B.
- Finish A while B remains pending: B's pending state stays truthful.
- Change filters, route or authorized view while a read is in flight: old view data cannot publish into the new view.
- Type A → B → A with controlled response ordering: generations remain distinct.
- Exercise cleanup and development Strict Mode without duplicate visible effects.
- For ordered writes, verify queue behavior, error handling and server effects separately.
- Verify input responsiveness and result labeling with keyboard and assistive technology in the actual app.

No browser, runtime, database, concurrency, accessibility, performance or integration tests were performed. No packages, services, credentials, settings or application code were changed. Documentary approval does not authorize deployment.
