# Evaluate the agent, not its confidence: Promptfoo or DeepEval?

Run: `20261101T153000Z-open-source-tooling` · scheduled 2026-11-01 10:30 EST / 15:30 UTC · evaluated 2026-11-01.

## Question and novelty
Which open-source evaluation harness should a coding team shortlist for repeatable agent regression checks, while keeping judge scores separate from hard acceptance gates?
The live catalog had 20 packages; the only tooling package compared browser execution interfaces. This two-project shortlist instead addresses evaluation harnesses. It does not repeat browser-tool selection or the checkpoint/effect-receipt reference.

## Verified source findings
| Project | Observed release / source | Practical fit | Constraints |
|---|---|---|---|
| [Promptfoo](https://github.com/promptfoo/promptfoo) | GitHub release 0.125.1, 2026-10-25; inspected main commit aa3ee561b1416d4d68f7c4e05fedd082db6aa2b1, 2026-11-01 | TypeScript CLI/library, declarative prompt/model comparisons and CI checks | Inspected package requires Node >=22.22.0. Main metadata is not independently verified npm artifact metadata. |
| [DeepEval](https://github.com/confident-ai/deepeval) | GitHub latest endpoint reports python-v4.2.4, 2026-09-22; inspected main commit 90e230cbe916c73240a48312c494065a39bf52d1, 2026-10-29 declares 4.3.2 | Python test functions, pytest integration, end-to-end and trajectory metrics | Python >=3.9,<4.0; integrations can require newer Python. Do not mistake latest GitHub release for latest PyPI package. |

The pinned licence texts are MIT for Promptfoo and Apache-2.0 for DeepEval. Preserve required licences/notices and applicable modification notices on redistribution; dependency, model and hosted-service terms require separate review. No third-party software is copied here.

Promptfoo exposes CLI and Node library entry points; provider configuration supports external MCP tools, with an optional MCP SDK dependency. Its separate [MCP server interface](https://www.promptfoo.dev/docs/integrations/mcp-server/) exposes evaluation tools over stdio or HTTP, including a sharing tool that can create public result URLs. Do not expose or grant that tool merely to run local checks. These are documented interfaces, not tested interoperability. DeepEval offers pytest, direct evaluate/metric APIs and trace instrumentation. Its README links a separate Confident AI MCP server; that is not evidence that the Python library itself is a drop-in MCP server. Examples and demos are in each repository README; none were executed.

Dependencies sampled from pinned manifests: Promptfoo includes model SDKs, schema validation, storage and telemetry libraries; its MCP SDK is optional. DeepEval includes pytest plugins, Pydantic, OpenAI, aiohttp, OpenTelemetry and PostHog, with several wildcard constraints. These declarations justify lockfile/environment review, not a vulnerability verdict.

## Privacy and operational boundaries
A local harness does not mean all evaluation data stays local. Remote target models and remote judges receive their configured inputs. Promptfoo documents default usage telemetry and opt-out `PROMPTFOO_DISABLE_TELEMETRY=1`; its page distinguishes telemetry from prompt/output content and identifies account-associated fields when configured. DeepEval documents default telemetry and `DEEPEVAL_TELEMETRY_OPT_OUT=1`. Its README says platform login logs test cases and notes automatic dotenv loading at import, disableable with `DEEPEVAL_DISABLE_DOTENV=1`. These settings are documented controls, not traffic tests.

Original recommendation: begin with synthetic fixtures, no platform login, explicitly selected providers, reviewed environment variables, isolated result storage and an outbound destination inventory. Inspect caches, traces, reports and CI artifacts before public upload. Do not run adversarial tests against systems without authorization. Neither tool substitutes for OS isolation or backend authorization.

Both can run in a local/CI environment without deploying a new public service. Runtime provisioning differs: Node versus Python environments and optional integrations. Costs include target calls, judge calls, retries, generated cases, storage and CI compute; local models shift costs to hardware. Optional hosted platforms are separate services. No price estimate, paid activation or installation is performed.

## Original synthesis: a two-lane acceptance design
**Hard lane:** deterministic checks for schema, required evidence references, forbidden actions and externally observed outcomes. A broken invariant fails regardless of a fluent answer.
**Judgment lane:** rubric-based usefulness and groundedness scores on the same fixed cases, with judge model/version, rubric, thresholds and uncertainty recorded. Calibrate against human labels; do not let a judge authorize actions.

Shortlist Promptfoo first for a JS/TS team comparing providers/configurations through a reviewable CLI matrix. Shortlist DeepEval first when Python tests and traced multi-step behavior already own the engineering workflow. This is a workflow-fit recommendation, not a measured superiority claim. A small existing pytest/Vitest suite is a simpler alternative when only deterministic assertions are needed.

An original future evaluation fixture could give an agent a synthetic issue and three permissible tools. Expected outputs include one structured answer and citations; a fourth forbidden tool must never run. Include a persuasive but incorrect answer, unavailable tool, missing evidence and a valid result. Record hard failures separately from judge scores. Run repeated trials only after approval and bound concurrency, case count, retries and model spend. A rising average score must not hide a forbidden action.

## Limitations and next evaluation gate
Release and maintenance dates are observed metadata, not support guarantees. No npm/PyPI artifact installation, dependency resolution, benchmark, network/telemetry capture, runtime, security, accessibility or cross-client integration test occurred. No claim of private-data suitability. GitHub release/body and main-package version divergence is retained rather than “corrected” by assumption. See sources.json, repos.json and independent qa.md.
