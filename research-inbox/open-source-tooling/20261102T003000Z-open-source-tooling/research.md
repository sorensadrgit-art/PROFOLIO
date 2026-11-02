# Before an agent says “not found”: make ripgrep's search scope explicit

Run `20261102T003000Z-open-source-tooling` · scheduled 2026-11-02 00:30 UTC · evaluated 2026-11-02.

## Question and novelty
How should a coding agent use a local text-search CLI without turning a filtered search miss into a false statement that code does not exist?

The live catalog and all four prior tooling reports were read first. They address browser interfaces, evaluation harnesses, MCP session tests and secret scanning. This one-project discovery addresses everyday code navigation, especially hidden configuration and executable preprocessing. No same-ID or unfinished topic directory was present. It is not another secret-scanner recommendation.

## Verified project snapshot
[ripgrep](https://github.com/BurntSushi/ripgrep) is a Rust command-line text search tool, with binary name `rg`, supporting Windows, macOS and Linux. The inspected source commit is `91f5d22ef48bde459d90a8c7dbeab416e97d0f2b`, dated October 27, 2026. GitHub's latest release endpoint returned 15.2.0, published July 15, 2026; the pinned Cargo manifest also declares 15.2.0. The release notes describe ignore-matching fixes and directory-traversal improvements, not a measured improvement in this environment. Crates.io/distribution binary identity was not verified. [S1, S6–S7]

The current source manifest declares Rust 1.96 and edition 2024. These are source-build requirements, not a requirement to install Rust alongside a prebuilt executable. Dependencies include the project's grep and ignore crates, lexopt, serde_json and platform-specific allocator support; PCRE2 is optional. Dependency declarations are not a security audit. [S6]

The actual COPYING file offers a choice of MIT or Unlicense; both licence texts were read. Under MIT, preserve the copyright and permission notice in copies or substantial portions. The Unlicense includes a public-domain dedication and warranty disclaimer. Neither choice grants rights to files being searched or to third-party dependencies. No upstream source, screenshots or data are redistributed here. [S3–S5]

## Source findings that matter for agents
Directory traversal normally omits ignored, hidden and binary files and does not follow symlinks. An explicitly named file is treated differently from recursively discovered files. `--hidden` and `--no-ignore` address different filters; neither alone implies complete coverage. `--files` inventories candidate paths, not successful content reads. [S2]

The guide documents environment-selected configuration through `RIPGREP_CONFIG_PATH`; its arguments precede explicit CLI arguments. `--no-config` disables that configuration-file input. It does not disable filesystem ignore rules or turn the process into a sandbox. [S2]

Preprocessing is execution: `--pre` launches a command for searched files, not merely a text-decoding setting. Restricting it with `--pre-glob` can reduce process launches, but does not establish trust in that command. Binary-as-text searching can emit terminal control data and consume substantial memory. [S2]

## Original synthesis: a bounded search contract
Shortlist ripgrep for local, inspectable lexical navigation when a coding agent has an authorized checkout. Do not present it as semantic symbol resolution, a call graph, an authorization boundary or a guarantee of absence. A literal substring can find comments and miss generated names, aliases or runtime behavior.

Recommended sequence, not implemented:
1. State the question, approved root and relevant file categories. For an exact identifier, prefer literal matching rather than accidentally interpreting regex metacharacters.
2. Record tool version and explicit options. Suppress unreviewed environment configuration where reproducibility matters; review wrappers and aliases separately.
3. Compare expected paths with a candidate-path inventory. A missing configuration file calls for a targeted authorized check, not automatic unrestricted traversal of the user's home directory.
4. Read returned matches in context and preserve errors, truncation and search boundaries. Report “no match in the searched scope,” not “the feature is absent.” A candidate list alone cannot prove every file was successfully read.
5. Escalate to a language-aware tool or direct source inspection when the question is structural. Broader search is a new scope decision when it crosses access or confidentiality boundaries.

Do not globally append `-uuu` to every agent command. It can expand output, expose ignored/private data and change binary handling without answering whether that expansion was authorized. Following symlinks needs a separately understood target boundary. Output limits are useful operational controls, but a truncated result must remain labeled incomplete. These are workflow recommendations, not built-in security guarantees.

An original future application is a compact “search receipt” attached to an agent's code claim: approved root, checkout revision, version, literal/regex mode, explicit filters, configuration policy, candidate-path count, completion/errors and output truncation. A source revision change invalidates the receipt. This is lightweight evidence for navigation, not publication clearance or a full repository audit.

## Installation, integration, costs and alternatives
Official release binaries and package-manager installation routes are documented; source builds require the declared Rust toolchain. No installation was performed. The CLI needs local filesystem permissions but no model, API key, hosted server or MCP connection for ordinary search. A coding agent may invoke it through its existing authorized command interface; no new tool connection is needed. Any proposed MCP wrapper would require separate permission and argument validation. Preprocessors add their own executables, dependencies and potential side effects. [S1–S2, S6]

README examples and screenshots provide demonstrations, but were not executed. Local compute, disk reads, output storage and the calling model's context budget are the operational costs; no mandatory hosted subscription is proposed. No speed ranking is claimed. Plain grep is a simpler portability alternative; language-server symbol search is a different, structural alternative. Neither alternative was independently shortlisted or benchmarked here. [S1]

## Later acceptance experiment — unperformed
Use a synthetic checkout with a visible source file, hidden config, ignored generated file, symlink, unreadable file and misleading comment. Define permitted scope first. Test whether the wrapper distinguishes no match, excluded input, failed read and truncated output, and whether it refuses unapproved preprocessing. Compare lexical results with a known symbol fixture. Do not use real credentials or private home-directory data.

Documentary review only: no installation, binary execution, runtime, integration, accessibility, performance or confinement test was performed. Current source and release metadata do not prove installed-artifact compatibility or ongoing support. See sources.json, repos.json and qa.md.
