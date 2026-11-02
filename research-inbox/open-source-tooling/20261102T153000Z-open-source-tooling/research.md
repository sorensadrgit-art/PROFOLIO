# actionlint: a clean workflow check has an environment-dependent coverage boundary

Run `20261102T153000Z-open-source-tooling` · scheduled 2026-11-02 15:30 UTC · evaluated 2026-11-02.

## Question and shortlist
When should a coding agent shortlist actionlint, and what must it record before interpreting a clean report as workflow coverage?

Shortlist one project: [actionlint](https://github.com/rhysd/actionlint), a Go static checker for GitHub Actions workflows. It adds workflow syntax, expression types, action interfaces and dependency checks beyond generic YAML validation. Its useful boundary is pre-execution diagnostics, not proof that a workflow ran correctly or is authorized to run. [S1]

At intake the live catalog held 75 packages. All nine prior tooling reports were fetched and reviewed; the live topic directory contained no same-ID or unfinished package. Earlier ShellCheck research concerns sourced-shell diagnostic coverage. This report instead addresses the workflow wrapper's optional analyzers and local/remote action interface coverage. It introduces no workflow file or CI installation.

## Source findings
**The environment changes the check set.** actionlint discovers ShellCheck and Pyflakes executables when available. Each can be selected by path or explicitly disabled with an empty option. ShellCheck applies to bash/sh scripts, not every shell; Pyflakes applies to Python run steps. The checks guide also describes expression substitution and selected ShellCheck rules disabled by the integration. A clean workflow result therefore cannot imply that every embedded language was checked identically on two machines. [S2, S3]

**Action interface coverage is not arbitrary remote inspection.** The checks guide describes a bundled database for popular action inputs, avoiding remote metadata fetches. It documents support for major-version references and limits for exact version tags and branch references in that database check. Local action metadata is inspected when used by workflows, but the same guide explicitly says composite action steps are not checked and action.yml cannot be passed directly as a workflow CLI input. Do not weaken a separately required immutable action-pin policy just to match a lint database; supplement uncovered interface checks instead. The last sentence is our recommendation. [S3]

**A clean process result is narrower than a successful workflow.** Documented exits distinguish no reported problems (0), findings (1), invalid options (2), and fatal errors (3). Filtering diagnostics with ignore patterns or disabling external analyzers changes what remains visible. JSON output is supported through a formatting template; preserve both the process status and the configured coverage, not just an empty findings array. [S2]

**Security checks are useful but bounded.** The README includes checks for certain unsafe expression interpolation and hard-coded credentials. Their presence is not a complete action-source, runner-isolation, permissions or supply-chain audit. Treat workflow contents and generated diagnostic snippets as untrusted data; never execute a proposed workflow because lint is clean. [S1, S3; boundary is original synthesis]

## Project evaluation
| Area | Assessment |
|---|---|
| Purpose and useful patterns | Static workflow-specific checks before execution; typed expressions, needs relationships, local/reusable interfaces, script integrations and machine-readable diagnostics. [S1–S3] |
| Technology/dependencies | Go; inspected go.mod declares Go 1.25.0. Dependencies include go.yaml.in/yaml/v4 v4.0.0-rc.3, doublestar/v4 v4.10.0, go-shellwords v1.0.12 and cron/v3 v3.0.1. Optional external ShellCheck/Pyflakes processes are separate from Go dependencies. Declarations are not dependency-resolution or vulnerability results. [S3, S4] |
| Maintenance/version | Latest-release endpoint returned v1.7.12 published 2026-03-30. Inspected main commit 011a6d15e749bb3f2d771eed9c7aa0e7e3e10ee7 is dated 2026-04-19 and fixes a ShellCheck integration deadlock on macOS. Do not assume that post-release fix is in the published v1.7.12 binary. Activity does not establish an SLA. [S6, S7] |
| License/restrictions | Actual LICENSE.txt is MIT. Copies/substantial portions require copyright and permission notices; warranty is disclaimed. Dependencies, linked assets and marks have separate rights. No upstream implementation, demo image or full document is copied here. [S5] |
| Compatibility | Platform and shell inference affect diagnostics; explicitly declare intended shells and check actual analyzer availability. Go source-build requirements are distinct from using a prebuilt executable. Stable artifact versus newer pinned source parity remains unverified. [S2–S4, S6–S7] |
| Demo | Maintainer provides a WebAssembly playground and repository examples. Documentation says processing stays in the browser; no traffic verification or demo execution was performed. Use synthetic content for later evaluation. [S1, S2] |
| Installation/hosting | Official README offers released binaries, Homebrew and Go installation; usage documents a Docker image containing external analyzers. Local CLI use needs no hosted service, account or model API. Container mounts and executable selection require separate approval and review. Nothing installed. [S1, S2] |
| Integration/API/MCP | CLI output and Go API are documented; integrations include editors, reviewdog and pre-commit. No new MCP server is needed for a shell-capable agent to perform a later authorized local check. No integration tested or connected. [S1, S2] |
| Operational cost | No license purchase requirement in the inspected grant; local/CI compute, analyzer subprocesses, artifact storage and review time still cost resources. Optional CI/provider charges are separate. No speed or budget estimate measured. [S2, S5; synthesis] |

## Original synthesis: require a coverage receipt
Proposed later workflow, not implemented:
1. Record the input revision and explicit workflow file inventory, actionlint executable/version, config and ignore patterns. Record whether local action/reusable-workflow metadata was available with the correct repository layout.
2. Record external analyzer identities, versions, configured executable paths, enabled/disabled state and relevant options. Do not store raw environment values or credentials. Mark PowerShell and other uncovered language checks separately.
3. Separate supported action-interface coverage from unknown references, composite internals and dependencies outside the inspected input set. Retain immutable dependency pins where required; obtain independent evidence for their metadata instead of changing pins to make warnings disappear.
4. Preserve exit code, diagnostics and processing errors. A missing analyzer or excluded file is a coverage fact, not remediation. Keep workflow authorization, runner trust, runtime behavior and business acceptance in separate gates.

An original future application is a CI-review assistant that compares baseline and candidate coverage receipts as well as findings. If a candidate becomes green only because Pyflakes disappeared or ignore patterns widened, flag the coverage regression rather than report a fix. Another is a workflow-interface inventory linking pinned action identities to independently reviewed metadata and noting which entries the bundled database does not validate.

## Alternatives and unperformed acceptance experiment
For YAML style, a general YAML checker is a narrower alternative, explicitly mentioned by upstream. For script behavior, language-specific tests and an authorized sandboxed workflow run answer different questions. Manual review may be sufficient for a small one-off workflow. These are conceptual alternatives, not additional evaluated shortlist repositories. [S3; synthesis]

A later synthetic fixture should contain an invalid workflow key, undefined expression property, bash diagnostic, Python undefined name, a PowerShell step, a local composite action, and an unsupported remote action-reference form. Compare explicit analyzer-enabled and analyzer-disabled configurations; require the receipt to show changed coverage even if findings decrease. Also test invalid CLI options and missing local metadata. Do not run the workflow itself to test the static checker. No fixture, analyzer, workflow, model or service was executed in this research.

## Limitations
Documentary review only. No installation, runtime, CI, integration, accessibility, performance, network capture or exploit testing. No full transitive-license audit, binary provenance verification or target-environment compatibility certification. Source references are pinned except the observed latest-release lookup; source and stable release deliberately remain distinct. See sources.json, repos.json, manifest.json and qa.md.
