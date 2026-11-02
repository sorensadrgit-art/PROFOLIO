# Valid JSON is not a passing acceptance gate

Run `20261102T033000Z-open-source-tooling` · scheduled 2026-11-02 03:30 UTC · evaluated 2026-11-02.

## Question and novelty
How should a coding agent use jq to inspect structured tool results without mistaking parse success, the last output, or an empty collection for acceptance?

The live catalog and all five prior tooling reports were read first. Those address browser interfaces, evaluation harnesses, MCP sessions, secret scanning and lexical search. This one-project discovery examines the small JSON-processing boundary between tool output and a workflow decision. The live topic directory contained no unfinished or same-ID package.

## Verified project snapshot
[jq](https://github.com/jqlang/jq) is a portable C JSON-processing CLI. The inspected default-branch commit is `992db9b7e5e3059b764bf430bd97716829e99578`, dated November 1, 2026. GitHub's latest-release endpoint returned **jq-1.8.2**, published June 20, 2026, with security and bug fixes. A recent documentation-dependency commit is not a runtime-support guarantee. Release binaries, installation and distribution-package identity were not verified. [S1–S2, S5]

The README provides standalone executable and container routes, source-build instructions, and an online playground. Source builds use C, autotools, make and optional/bundled Oniguruma; configure also exposes decimal-number support. Build dependencies are not all runtime prerequisites for a prebuilt binary. No demo was executed or data submitted to the playground. [S1, S4]

Actual COPYING distinguishes MIT software, CC BY 3.0 documentation, and additional included-code notices, including ICU and BSD-style conditions. Preserve applicable notices and documentation attribution when redistributing; do not describe the whole repository as uniformly MIT. Other components and input data retain their own rights. This package contains original synthesis, not copied source or documentation. [S3]

## Source findings
The manual documents that normal invocation can process multiple input values. For ordinary completed evaluation without execution errors, `-e` bases its result status on the **last** output: false/null fail, another value succeeds, and no valid output has its own status. Empty arrays and objects are not false/null. `all` over an empty array returns true. Optional operators can suppress errors; a missing object field yields null. `--slurp` collects the input stream into an array rather than proving it contained one document. `--arg` binds a string; `--argjson` parses a JSON value. These semantics make jq useful, but do not supply an application's acceptance policy. [S6]

## Original synthesis: make the gate return one deliberate decision
Conditional shortlist: jq for a small, reviewed CLI adapter when structured data is already available. It is not automatically a JSON Schema validator, authorization system, factual verifier or complete execution receipt.

Recommended design, not implemented:
1. Establish the input contract before writing a filter: one envelope or a stream, expected item count, required types, mandatory fields, accepted states and whether an empty result is valid.
2. Keep producer execution status separate from parsing and business acceptance. A valid error envelope can parse successfully. A valid result fragment can coexist with a failed or interrupted upstream operation.
3. Require the acceptance program to emit exactly one boolean. Inspect exit status and diagnostics as well as output; a list of per-item booleans is not the same decision as an aggregate gate.
4. Specify cardinality separately from universal predicates. “Every received item is valid” does not establish that any required item arrived or that all requested items arrived. Compare stable requested identifiers with returned identifiers, including missing and duplicate entries.
5. Avoid silently converting absent, malformed or suppressed-error data into an ordinary empty result. Distinguish “not applicable,” “none found,” “producer failed” and “incomplete.”
6. Bind untrusted values as data rather than interpolating them into filter source or shell commands. Review shell quoting, argument boundaries and output handling independently. A jq filter does not confine the surrounding shell or grant access to private inputs.

An original future application is a compact tool-result receipt containing producer status, input hash, expected/observed cardinality, schema/contract revision, filter hash, parser status and final decision. Bind the decision to the exact bytes evaluated. Do not publish raw result payloads merely because the gate accepted their structure. This complements the earlier search and scan receipts without repeating their coverage questions.

## Installation, integration, costs and alternatives
Ordinary local CLI use requires an authorized executable and readable input, but no hosted server, model API, credentials or MCP connection. A calling agent's existing command interface can invoke it; adding an MCP wrapper is unnecessary for this narrow use and would introduce separate argument and permission review. Container use adds runtime and mount management. Local CPU, memory, storage, triage and model context are costs; no paid service is proposed. Large slurped inputs need an explicit size budget. No resource measurements were taken. [S1, S4, S6]

A small language-native parser plus explicit assertions may be simpler when the application already owns the contract; a dedicated schema validator is an alternative for complex reusable structural rules. Neither alternative was independently shortlisted or benchmarked here. jq is a workflow-fit candidate, not a claimed fastest or safest choice.

## Later acceptance experiment — unperformed
Use synthetic inputs only: one valid envelope; two concatenated documents; an empty array; a false result followed by true; missing required fields; wrong types; duplicate IDs; a valid error envelope; malformed trailing input; and an upstream failure that produced partial output. Define expected outcomes first. Verify that the adapter never approves based only on parse success or the last per-item result. Test shell handling and bounded input size separately.

No installation, jq execution, runtime, integration, security, performance or accessibility tests were performed. Registry/distribution artifacts, installed build options and application compatibility remain unverified. Documentary QA and bounded publication scanning are recorded in qa.md.

