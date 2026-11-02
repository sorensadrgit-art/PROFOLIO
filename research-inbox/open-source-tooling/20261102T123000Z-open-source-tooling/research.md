# ast-grep: a structural codemod still needs a semantic acceptance test

Run `20261102T123000Z-open-source-tooling` · scheduled 2026-11-02 12:30 UTC · evaluated 2026-11-02.

## Question and shortlist decision
When can an agent use structural search and rewrite rather than lexical replacement, and what evidence must remain outside that tool?

Shortlist **one project: [ast-grep](https://github.com/ast-grep/ast-grep)** for reviewable, syntax-shaped migrations and project-specific lint rules. Do not treat a matched AST, accepted snapshot or generated patch as proof that behavior is preserved. This is a discovery and evaluation plan, not installation or permission to apply a codemod.

The live catalog contained 69 packages. All eight existing tooling reports were read, and the live topic directory contained no same-ID or unfinished package. Prior ripgrep research covers lexical search scope; this adds structural rewrite selection, parser identity and rule-test ownership. Prior ShellCheck research concerns shell diagnostics, not codemod correctness.

## Source findings
**Syntax is the boundary.** The README describes tree-sitter-based structural matching, YAML rules and rewriting. The official FAQ explicitly excludes scope/type information, control/data flow, taint analysis and constant propagation. It also explains that CLI and playground results can differ because parser versions and encodings differ. Thus a browser demonstration is not an installed-CLI acceptance result. [S1, S6]

**A patch is not necessarily valid code.** The rewrite guide distinguishes preview, interactive acceptance and `--update-all`, which accepts changes without confirmation. Captured variables can populate fixes; the guide explicitly illustrates a rewrite that can produce invalid assignments. It also states that an unmatched metavariable becomes an empty string in a fix. A visually plausible template can therefore erase intended content. [S7]

**Rule tests and baselines answer different questions.** The testing guide distinguishes expected matches from expected nonmatches; snapshots additionally retain diagnostic output such as spans/messages. `--skip-snapshot-tests` omits that layer; updating a snapshot creates or changes the expected baseline. A newly accepted baseline is not independent evidence that a change is correct. [S8; final sentence original inference]

**Inspect the actual invocation.** The run reference documents configuration discovery in the current directory and parents, explicit language selection, structured JSON output, and inspection diagnostics on stderr. A wrapper should retain its configuration choice and diagnostic stream rather than consuming only match JSON. [S9]

## Project evaluation
| Area | Observed facts and limits |
|---|---|
| Version/maintenance | GitHub latest release was **0.51.1**, published **2026-11-02T05:33:13Z**. Tag commit **5a204b1ef3facef94de125d990dddc7cf90011c0**, committed **2026-11-02T05:29:50Z**; pinned Cargo/npm manifests also say 0.51.1. Release notes include skipped-file reporting and rewrite fixes. Activity is not an SLA. [S2–S5] |
| Technology | Rust workspace, edition 2024, declared source-build minimum Rust 1.90.0; tree-sitter, serde, regex, ignore and project crates are declared dependencies. Constraints are not a resolved dependency/security audit. [S3] |
| npm route | Source manifest names `@ast-grep/cli`, declares Node >=12, detect-libc 2.1.2, a postinstall script and platform-specific optional binary packages. This old minimum is not advice to use an unsupported Node release. npm registry retrieval failed; published package identity/integrity and install behavior remain unverified. [S5] |
| Compatibility | Pinned npm optional packages cover specified Windows, macOS and GNU Linux architectures; do not infer every OS/libc combination. Grammar/language selection affects results; validate the intended CLI/parser combination. [S5–S6] |
| Licence | Actual pinned LICENSE is MIT, requiring copyright/permission notices for copies or substantial portions and disclaiming warranty. Dependencies and externally linked assets retain separate rights. No upstream code, screenshots or source corpus is copied into this report. [S4] |
| Demo | Official playground and README illustration are available; neither was executed or populated with private code. Treat a demo as exploration, not certification. [S1, S6] |
| Installation/hosting | README documents npm, pip, Cargo and OS package routes. A trusted platform-matched executable plus authorized inputs suffice for local CLI work; source builds additionally require toolchain/dependencies. No server or new hosting is inherent. No installation occurred. [S1, S3] |
| API/MCP | CLI plus structured output is the evaluated surface; README also advertises AST traversal/manipulation APIs. No MCP registration, API binding interoperability or wrapper was tested. A new MCP connection is unnecessary for this evaluation. [S1, S9] |
| Costs | MIT grants do not require a licence purchase. Local/CI compute, storage and reviewer time still cost resources; optional agent inference is separate. No benchmark or hosted-price estimate was made. [S4; synthesis] |

## Original synthesis: a three-gate codemod workflow
Proposed only, not implemented:
1. **Specify the transformation contract.** Name the allowed root, exact revision, language/parser, rule/fix version, intended syntactic shape and behavioral invariants. If identity depends on an imported symbol or inferred type, obtain compiler/language-service evidence rather than pretending an identifier spelling establishes identity.
2. **Validate candidates before writes.** Use synthetic positive and negative fixtures, including a shadowed identifier, same spelling in a different syntactic role, comments/strings, nested calls, missing optional capture and malformed input. Review diagnostics and proposed diffs. Bind approval to input/rule hashes and the exact file set; a changed input invalidates an old preview.
3. **Verify semantics after an authorized application.** Compile/type-check where applicable, run focused behavioral tests, inspect unintended file changes and repeat the rule to test idempotence. These checks complement, rather than follow automatically from, ast-grep rule tests. Keep snapshot approval separate from automatic baseline generation.

An original future application is a **codemod evidence bundle** with a candidate patch, positive/negative fixture results, reviewed snapshot delta, input/rule/parser identity and independently measured application test results. An agent can prepare that bundle while a reviewer owns the baseline and write approval. Record incomplete coverage and skipped files; never label an omitted language or failing parser “clean.”

Choose ordinary lexical search when the question is spelling or text occurrence; choose language-service/compiler-assisted refactoring when symbol identity or types are essential. For a tiny migration, a manually reviewed patch with existing tests may be simpler. These are conceptual alternatives, not newly benchmarked or licensed shortlist entries. No claim that ast-grep is fastest or safest is made.

## Later experiment — not performed
On disposable synthetic files, compare a rule's intended matches and nonmatches, missing captures, parser differences, snapshot regeneration, preview versus write mode, and repeated-application behavior. Include a structurally matching change that intentionally alters runtime behavior so the semantic test must fail even if the structural rule passes. Capture installed version and artifact identity first. No production code, service or credentials are required.

## Limitations
Documentary review only. No installation, rule execution, compilation, runtime, performance, accessibility, security exploit or integration tests were performed. Dynamic website documentation is not pinned to release 0.51.1; exact installed feature parity must be checked later. Registry artifacts, transitive licences, dependency vulnerabilities and target environment compatibility are unverified. See sources.json, repos.json, manifest.json and qa.md.

