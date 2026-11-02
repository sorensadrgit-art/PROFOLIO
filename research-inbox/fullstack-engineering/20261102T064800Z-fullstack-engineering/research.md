# Retrying an observation is not retrying the user command

Run: `20261102T064800Z-fullstack-engineering`  
Scheduled: 2026-11-02 06:48 UTC. Research began at 06:45 UTC after early delivery. Evaluated: 2026-11-02.

## Question and novelty

How should a Playwright test wait for asynchronous UI results without accidentally repeating the action that produced them?

The live catalog, branch tree and six prior full-stack packages were read first. Earlier work concerns cache freshness, concurrent writes, connection budgets, index migrations, React request ordering and build configuration. This note concerns test observation boundaries and retry scope. No same-run artifact or unfinished full-stack package was found. It is not a repository discovery shortlist.

## Primary-source findings

- **An assertion can repeat observations.** Playwright's web assertions re-fetch and check until a condition passes or the assertion timeout expires. Generic value assertions do not auto-retry. `expect.poll` re-invokes a producer; `expect.toPass` retries its code block. The latter's documented default timeout is zero and does not inherit a custom expect timeout. These mechanisms have different scopes. [S1](https://playwright.dev/docs/test-assertions)
- **A locator is a query, not a frozen element.** Locators resolve elements when used; Playwright recommends user-facing attributes or explicit test contracts. Strictness helps expose ambiguous single-element actions. Taking a value out of the locator does not turn a later generic assertion into a live query. [S2](https://playwright.dev/docs/locators)
- **Actionability concerns delivering the action.** A normal click waits for a unique, visible, stable, enabled target that receives events. Force can bypass nonessential checks. These checks are not evidence that an application transaction or background operation succeeded. [S3](https://playwright.dev/docs/actionability)
- **Whole-test retries are another boundary.** A failed test causes its worker/browser to be discarded; retries rerun the failed test in a new worker. A result that passes after initially failing is classified as flaky. This does not make the first failure disappear or establish cleanup of external state. [S4](https://playwright.dev/docs/test-retries)

## Original synthesis — proposed, not tested

Separate a test into an intentional command, an observation, and a declared acceptance condition. A click resolving means the automation delivered the interaction under its checks, not that the expected business outcome exists. Conversely, a result not yet visible does not automatically justify another click.

For a disposable editor fixture, the proposed sequence is: identify one save control, perform the save once, then await a result tied to that fixture's identity or revision. Prefer a live locator assertion for DOM state. When acceptance depends on an API read, a bounded polling producer should perform only the authorized read needed for that condition. Do not place creation, payment, submission or destructive cleanup inside a retrying block merely because a later assertion may fail.

Original pseudocode, not a runnable test or framework helper:

```text
fixture = createIsolatedFixtureForThisAttempt()
performIntendedCommandOnce(fixture)
waitForReadOnlyEvidence(fixture.expectedIdentity, explicitDeadline)
assertDeclaredOutcome()
```

The critical review question is **what is invoked again**, not whether an API name sounds like an assertion. A retryable block containing both a click and an assertion repeats both when it fails. A generic assertion over an already-read string only reuses that string unless the reading operation is deliberately repeated. Choose the smallest observation boundary that can establish the requirement.

An eventual match establishes that the condition was observed, not that it will remain true forever. If the requirement is stability, absence of duplicate effects or correctness after a later event, define those checks separately with explicit observation windows and identities. Avoid arbitrary sleeps as substitutes for a known application state, but also avoid asserting a transient toast when the requirement is durable persistence. Neither UI text nor a passing test proves unrelated authorization or database invariants.

Whole-test retry needs an independent fixture policy. A new browser process does not imply a new database, job queue or external service. Use isolated synthetic test data and attempt-aware identifiers where appropriate; reconcile uncertain write outcomes instead of blindly creating again. Preserve first-attempt failure evidence and review flaky classifications rather than presenting retry success as clean first-run success. This is an application/testing recommendation, not a claim about an executed backend.

## Proposed acceptance checks — all NOT RUN

1. Delay a result while counting the fixture's command invocations: polling must not generate extra commands.
2. Resolve a result with the wrong item identity: a generic success label must not satisfy the intended fixture's acceptance.
3. Replace the DOM node during rendering: verify the chosen locator still identifies the intended control/result.
4. Introduce duplicate matching controls: fail on ambiguity rather than choosing the first silently.
5. Make the first attempt fail after an external effect: verify retry fixtures and reconciliation do not duplicate it.
6. Make success transient: confirm the test distinguishes eventual visibility from any explicitly required durable state.
7. Check actual test, assertion and polling deadlines independently; retain diagnostics when they expire.

## Compatibility, maintenance, licensing and operational costs

Playwright Test is a JavaScript/TypeScript browser-testing runner; this note uses its Node-facing documentation. Actual package version, browser revisions, Node requirements, framework lockfile compatibility and source release/commit dates were not verified and remain null. Mutable documentation is not an installed-version receipt. Examples were read, not run, and no maintenance cadence is inferred from current pages.

Implementation would require a compatible test runner/browser installation, a test application and isolated backend fixtures. Existing authentication should use test accounts with minimal scope; no MCP connection, new paid service or production credential is required by this proposed contract. No packages or browser binaries were installed here. CI browser workers, repeated reads, traces and retries consume CPU, memory, time and storage; no cost or speed improvement was measured. Component/unit tests can isolate deterministic logic without a full browser, while a narrower end-to-end test covers the cross-layer interaction; actual cost depends on the suite, and neither replaces the other automatically.

The repository's actual LICENSE is Apache-2.0, with redistribution/notice conditions, patent provisions and warranty disclaimers. It is not unrestricted public-domain reuse or an audit of browser binaries, dependencies or third-party assets. No implementation or substantial source text was copied. [S5](https://github.com/microsoft/playwright/blob/main/LICENSE)

Original future application: a test-review checklist that annotates each retry boundary with its possible external effects and evidence identity. This can flag an unsafe action inside a polling block before a flaky test creates duplicate records.

No browser, runtime, integration, retry-race, security, accessibility or performance tests were performed. Documentary approval is not deployment or runtime certification.
