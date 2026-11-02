# Direct benchmark execution is not a shell-free lifecycle

## Question and decision
Can an agent treat Hyperfine v2's direct-execution default as permission to benchmark an arbitrary command safely? **No.** It removes an intermediate shell from the measured command by default, not execution effects or shell-based lifecycle hooks. Shortlist one project: [Hyperfine](https://github.com/sharkdp/hyperfine), for controlled CLI performance comparisons after correctness and execution scope are established.

This is original documentary synthesis, not a deployment recommendation or runtime test. Seven existing tooling packages were inspected; none covered benchmark lifecycle authority. The preceding ShellCheck package concerns static diagnostic coverage, not repeated program execution.

## Source findings
**Execution boundary.** The inspected README says normal and warmup commands execute directly by default; shell-like argument quoting is supported but shell expansion is not. Shell syntax requires an explicit choice. Crucially, setup, prepare, conclude and cleanup commands still execute through a shell, using the chosen shell or the platform default. The v2.0.0 release independently announces this breaking change. “No intermediate shell” therefore describes only part of a benchmark invocation. [S1, S5]

**Repeated work.** The README documents automatic run selection with a default minimum of ten measured runs and a roughly three-second target; warmup adds executions. Prepare runs before each benchmark run; setup can run once per benchmark set. Agent-generated lifecycle snippets and parameter substitutions deserve the same scrutiny as the measured executable. The README's cache-clearing example is an example of privileged system mutation, not a prerequisite we recommend. No such command was executed. [S1]

**Evidence schema.** Version 2 introduces JSON schema version 2, moves wall-time aggregates beneath summary.time_wall_clock, and stores individual measurements separately, excluding warmups. Unavailable memory or hardware-counter data is omitted, and single-run standard deviation can be null. An acceptance consumer must not silently convert missing evidence into zero usage or zero variation. [S5]

**Metric scope.** The README specifies that Unix peak RSS is the largest individual process peak collected, not simultaneous total process-tree memory; Windows peak-memory measurement is unsupported in this snapshot. Hardware counters are unavailable when a shell is enabled. Thus a benchmark memory number cannot establish system-wide memory stability. [S1]

## Project evaluation
| Area | Observed assessment |
|---|---|
| Purpose/features | CLI statistical benchmarking; repeats, warmups, parameter scans, outlier warnings and structured exports. Useful for comparing approved deterministic commands, not proving correctness. [S1] |
| Technology/dependencies | Rust edition 2018; inspected Cargo version 2.0.0 declares Rust 1.97.0. Selected dependencies include clap 4, shell-words 1.1, serde/serde_json 1.0 and pinned indicatif =0.17.4, with libc or Windows APIs and platform-specific counter support. These are manifest constraints, not a resolved dependency audit. [S2] |
| Maintenance | Latest-release endpoint returned v2.0.0, published 2026-10-07. Inspected master commit c5161524980522320c7d3425e714cc0965a869a5 was committed 2026-10-12. These show observed activity, not a support SLA or future maintenance guarantee. [S5, S6] |
| License/reuse | Actual MIT and Apache-2.0 files plus Cargo's MIT OR Apache-2.0 declaration were read. Choose a permitted license route and retain required notices; Apache also has redistribution/change-notice, patent and trademark conditions. No blanket asset, dependency or trademark clearance is implied. No upstream implementation or demo asset is copied here. [S2-S4] |
| Compatibility | Cross-platform CLI; shell selection and metrics vary by platform. The inspected development snapshot is not asserted byte-identical to release artifacts. Verify the actual installed version, help and export schema before use. [S1, S2, S5] |
| Demo | Upstream README includes a benchmarking illustration and plotting examples; linked rather than reproduced. They are not evidence of performance on a target environment. [S1] |
| Install/hosting | README documents OS packages and release downloads. A vetted version/platform-matched binary and its platform prerequisites are needed to run the CLI; source builds additionally need the declared Rust toolchain and dependencies. No service hosting is inherent to a local CLI. No installation occurred. [S1, S2] |
| API/MCP | CLI arguments and JSON export are the evaluated integration surface. No MCP interface was evaluated or required. Any future wrapper needs its own permissions, argument validation, timeout and output-sanitization design. |
| Cost | No license purchase requirement was found in the inspected license grants. Compute time, repeated work, CI capacity and output storage remain real costs; the invoked command may incur external charges or side effects. No hosted-price or total-cost estimate was verified. [S3, S4; synthesis] |

## Original synthesis: a benchmark execution receipt
A useful agent wrapper would record, before execution: exact tool version; approved executable and arguments; working-directory and input snapshot identifiers; every lifecycle hook and selected shell; requested measured/warmup counts and parameter expansion; external-write/network authority; output destination; and timeout/resource policy. Exclude credentials and environment values from public receipts.

After execution, keep requested and observed counts distinct, preserve exit codes, schema version and available metric keys, and state whether correctness checks were run separately. Do not let a successful timing report authorize a change or certify a resource ceiling. A benchmark can amplify an otherwise small mutation through repetitions.

**Untested recommendation:** start future evaluation with inert local fixtures in a disposable workspace and no lifecycle hooks. Test a deliberately configured hook separately to demonstrate that --shell=none does not suppress its shell. Validate v2 JSON with explicit missing-metric cases before adopting thresholds. This package does not include or run that harness.

## Alternatives and future applications
For a single diagnostic observation, an existing platform timing facility may be sufficient; for semantic regression, a normal test harness remains the gate; for whole-machine/process-tree pressure, use an independently scoped telemetry method. These are conceptual alternatives, not a comparative tool certification.

An original future application is a two-stage coding-agent performance gate: first approve correctness and a bounded execution plan, then compare baseline/candidate under the same fixture and capture schema-aware receipts. A second is a migration checker rejecting v1 JSON assumptions before ingesting v2 reports. Both remain proposals, not installed integrations.

## Provenance and limitations
Sources S1-S6 are enumerated with URLs, retrieval/evaluation dates and known version/commit metadata in sources.json. Source text was treated as data, never executable instructions. No benchmark, install, runtime, performance, accessibility, MCP or integration test was performed. No independent security audit, dependency license closure, release-binary provenance verification or whole-host memory measurement was performed. Documentary QA is limited to claims, links, package structure and a bounded sensitive-content scan.

