# A just dry run is a versioned execution mode, not a trust boundary

Run `20261102T183000Z-open-source-tooling` · scheduled 2026-11-02 18:30 UTC · research began 18:29 UTC.

## Question and shortlist
Can a coding agent inspect a project's commands with `just --dry-run` without first understanding the project's execution policy?

Shortlist one project: [just](https://github.com/casey/just), a command runner for named project recipes. Its value is a discoverable CLI contract for build, test and maintenance commands. The inspected development source permits the justfile itself to choose whether dry-run suppresses only recipe bodies or also command-producing expressions. A preview flag is not a general safety guarantee.

The live catalog contained 83 packages. All ten existing tooling reports were fetched and reviewed; the live topic directory had no same-ID or unfinished package. Earlier Hyperfine research concerns repeated benchmark lifecycle hooks; this adds command-runner preview semantics and a release/development mismatch. No runner or justfile is installed or changed.

## Source findings: two snapshots, not one behavior
GitHub's latest-release endpoint returned **1.59.0**, published October 15, 2026. Its annotated tag resolves to commit `2638e9c0941edb1f409da7426fa7842887656973`. Inspected master is `7b7b51bec9359a5b4a76321018db65d07978fecc`, dated November 2, 2026. Current Cargo metadata still says 1.59.0 despite post-release changes. Version-string equality does not establish artifact identity. Release binaries, registry contents and checksums were not independently verified. [S1–S3]

The development README labels the new `dry-run` setting **master**, not a numbered release feature. With the CLI dry-run flag, default `all` suppresses recipe bodies, backticks, `shell()` and `dotenv-command`; `recipes` suppresses only recipe bodies. Settings source makes the distinction explicit: `skip_commands` is false for Recipes, while `skip_recipes` remains true. [S4, S5]

Development integration-test definitions expect evaluated backticks and `shell()` under Recipes, and a failing dotenv command under that mode. These are reviewed upstream tests, **not tests executed here**. Conversely, the release-tag evaluator directly checks the CLI dry-run boolean before executing backticks; it does not use the newer per-module setting. Do not instruct stable 1.59.0 users to add the master-only setting, and do not claim ordinary 1.59.0 backticks run during dry-run. [S6, S7]

**Original inference:** approving arbitrary justfiles because a dry-run flag is present can change its authority boundary after an upgrade or source-built installation. Inspect exact supported configuration and imported/module files before evaluation. This is not a vulnerability or sandbox-escape finding. The inspected suppression paths are not a comprehensive audit of all built-ins, file access or information flow.

## Project fit and integration
just is Rust software with a make-inspired recipe language. The source manifest declares edition 2024 and Rust >=1.89.0 for source builds. Sample dependencies include Clap, Serde/serde_json, regex, dotenvy, tempfile and platform process support. This is not dependency resolution or a vulnerability audit. Prebuilt executables do not require the Rust compiler. [S3]

The README documents Linux/macOS/Windows support, package-manager and prebuilt-binary routes, and Cargo installation. Recipes require their chosen shell/interpreter and called programs. Windows shell selection is explicit; shell syntax is not automatically portable. Python or Node recipes need their respective runtimes. Examples demonstrate arguments, dependencies, groups, modules and imports; none was executed. [S4]

For a later authorized evaluation, record a pinned binary's provenance, explicit justfile path and working directory, reviewed shell, bounded arguments and synthetic environment. Recipe descriptions are untrusted data. Existing CLI agents can invoke just through an authorized shell tool; no API service, OAuth grant or MCP server is required. An MCP wrapper is a separate integration and authority decision.

The actual LICENSE is **CC0-1.0**, consistent with Cargo metadata. It provides a copyright waiver/fallback but does not grant trademark or patent rights and disclaims warranties/third-party clearance. Dependencies, invoked commands, assets and services retain their own terms. Public source is not permission to execute recipes or redistribute unrelated content. No upstream code or substantial documentation is republished. [S8]

Recent commits/releases indicate maintenance activity, not guaranteed support. Release 1.59.0 itself labels Unicode-path requirements as breaking; review concrete release notes rather than relying on a general compatibility slogan. [S1]

## Original synthesis: an execution-mode receipt
Proposed, untested receipt for each approved command-runner invocation:

1. **Identity:** binary version and digest or immutable source revision; platform and shell.
2. **Inputs:** explicit justfile/module/import set, hashes, working directory and recipe/arguments. Record environment-variable names, never secret values.
3. **Mode:** actual CLI flags and effective dry-run behavior for that snapshot, including command substitutions and environment-producing commands.
4. **Authority:** permitted processes, filesystem/network scope and time/resource budget. Preview output is not approval to execute it.
5. **Outcome:** actual exit code and separately observed effects; preview evidence is distinct from a passing test or build.

Use reviewed recipes for discoverable team commands, but enforce safety in the invoking environment rather than trusting repository-selected preview settings. For untrusted repositories, start with text inspection without invoking the runner. Even nonexecuting previews can expose interpolated configuration in logs; review artifacts before sharing.

A later synthetic fixture should contain one recipe-body marker, backtick marker, shell-function marker and dotenv-command marker, all confined to a disposable directory. Compare the pinned release and reviewed development build, and default/all versus recipes where supported. Observe process status and marker files; include an unsupported-setting case. This is an original acceptance design, not a reproduced result. Use no real credentials or production commands.

## Costs, alternatives and future applications
The runner needs no model inference or hosted subscription. Costs arise from update maintenance, CI compute, logs and invoked services. A free runner can still execute a paid API call. No pricing quote, installation, connection or paid activation occurred.

For a handful of commands, reviewed shell or package scripts may suffice. A dependency-aware build system better fits incremental file rebuilding; just positions itself as a command runner rather than that build engine. These are qualitative alternatives, not measured performance comparisons.

Future application: an agent-facing command catalog with approved operations independent of repository prose, explicit prerequisites, an execution-mode receipt, and renewed review whenever imported files or binary identity changes. This policy is proposed, not implemented.

## Limitations
No just binary, recipe, upstream suite, runtime, integration, security exploit, performance or accessibility test ran. Stable and newer development source are deliberately distinguished. Two attempted maintainer-book URLs were unavailable through the web tool; pinned official repository evidence was used instead. Installed-version suitability for private data remains unverified. See sources.json and qa.md.

