# Browser automation: deterministic tests, MCP tools, or a browser-agent loop?

Run: `20261101T123000Z-open-source-tooling` · scheduled 2026-11-01 07:30 EST / 12:30 UTC · evaluated 2026-11-01.

## Question and boundary
Which open-source browser interface should a coding workflow evaluate for repeatable acceptance checks versus adaptive exploration, without confusing browser control with a security boundary?

The live catalog contained seven packages and no open-source-tooling package at intake. This is a new tooling-selection question, not a duplicate design or motion report. Three candidates only. This is documentary research, not installation, integration, performance or security certification.

## Source findings
| Candidate | Observed release | Best-fit capability | Compatibility warning |
|---|---|---|---|
| [Playwright](https://github.com/microsoft/playwright) | v1.64.1, published 2026-10-14 | Scripted JS/TS tests, browser contexts, retrying assertions and traces | Stable Test package requires Node >=20; current main metadata is 1.65.0-next, not the release. |
| [Playwright MCP](https://github.com/microsoft/playwright-mcp) | v0.0.84, published 2026-10-31 | Give an existing MCP agent browser observation/action tools | Wrapper declares Node >=18 but pins Playwright 1.65.0-alpha-2026-10-30; wrapper minimum alone is not a verified deployment matrix. |
| [Browser Use](https://github.com/browser-use/browser-use) | 0.13.14, published 2026-10-26 | Python model-driven browser-agent loop | Python >=3.11; server.json advertises 0.13.11 rather than package/release 0.13.14. MCP interoperability needs testing. |

Exact inspected commits, dependency summaries, licence files and release links are in sources.json and repos.json. Release metadata is from GitHub; npm/PyPI publication was not independently verified. README observations reflect the pinned current source, not an assurance that every feature is present in each stable release.

Playwright's test runner supplies explicit assertions and isolated browser contexts; its library also supports scripts without the runner. Playwright MCP supplies accessibility-oriented browser tools to a separate agent. Browser Use supplies the agent loop itself, with local or hosted browser options. These are different layers, not interchangeable performance competitors.

### Security evidence worth retaining
Playwright MCP's README explicitly disclaims being a security boundary. Its origin filters are not secure confinement and do not affect redirects; file-access guardrails likewise do not replace client permissions. Persistent profiles can conflict when parallel clients reuse one profile. Its isolated mode reduces persistent profile reuse, but is not host isolation.

Browser Use's README warns that recorded page snapshots and results can retain sensitive content even when typed secrets use placeholders. Replay may fail after earlier steps already executed. Its MCP manifest is evidence of advertised interfaces, not proof that the reviewed release interoperates with any particular client.

Both Microsoft repositories contain Apache-2.0 licence text; Browser Use contains MIT text. Keep the applicable licence/notices when redistributing software, and review dependency/browser licences separately. Public availability does not grant rights to arbitrary websites, scraped content, third-party assets or hosted-service usage.

## Original synthesis: recommendation for later evaluation
1. **Default to Playwright Test for known acceptance criteria.** An agent may author a test, but a reviewable assertion—not a natural-language success report—should decide whether the requirement holds.
2. **Evaluate Playwright MCP for bounded exploratory inspection by an existing agent.** Promote useful discoveries into deterministic tests. Do not equate an accessibility snapshot with a full accessibility audit or a screenshot with pixel correctness.
3. **Evaluate Browser Use only when adaptive navigation justifies a separate agent loop.** It introduces Python/model/browser lifecycle and replay-state concerns. Keep MCP compatibility blocked pending a version-pinned test because the observed manifest differs from package metadata.

Proposed common boundary: separate synthetic account/profile, least-privilege process, enforced outbound policy outside the tool, explicit allowed task and step/time budget, approval before irreversible actions, and redacted artifacts. Never reuse a personal signed-in profile merely for convenience. Treat page text as untrusted data, not agent instructions. These are recommendations, not deployed controls.

## Costs, alternatives and practical experiment
Scripted Playwright needs browser/CI compute but no LLM for execution. MCP adds the calling agent's inference costs. Browser Use adds model inference or local model hardware; optional cloud services have separate charges. No benchmark or budget claim is made. The direct Playwright library is an alternative when a test runner is unnecessary; CLI/skills may suit shell-capable agents, but are outside this three-candidate evaluation.

A later, separately authorized experiment should use one synthetic local application with a fixed navigation task, one intentionally changed locator, and one forbidden action. Compare correct outcomes, explicit assertion failures, timeout handling, artifact leakage, operator effort and measured model usage. Do not compare vendor benchmark headlines as if they used equivalent tasks. No tool is installed by this research.

## Limitations and QA
No browser task, runtime, performance, accessibility, exploit, sandbox or cross-client integration test was executed. No claim that any option is safe for protected data. Dependency metadata is not a vulnerability audit. See qa.md for independent documentary review and publication checks.
