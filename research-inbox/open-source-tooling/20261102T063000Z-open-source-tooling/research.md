# Following a sourced script is not the same as reporting its warnings

Run `20261102T063000Z-open-source-tooling` · scheduled 2026-11-02 06:30 UTC · evaluated 2026-11-02.

## Question and novelty
How should a coding agent use ShellCheck without treating a quiet result for a wrapper script as evidence that its sourced helpers were checked and reported?

The live catalog and all six prior tooling packages were read first. They cover browser tools, evaluation harnesses, MCP sessions, secret scanning, lexical search and JSON acceptance. This one-project discovery addresses shell-script analysis coverage and dialect ownership. No same-ID or unfinished directory was present in the live topic listing.

## Verified project snapshot
[ShellCheck](https://github.com/koalaman/shellcheck) is a Haskell static-analysis CLI for shell scripts, maintained by Vidar Holen and contributors. The inspected development commit is `919d37c4d77a7ce16cfc76ebdd781dd6e8081fa5`, dated October 26, 2026. Its Cabal manifest declares 0.11.0. GitHub's latest-release endpoint returns v0.11.0, published August 4, 2025; the release body dates its changelog August 3. Matching version strings do not make newer development documentation identical to that released binary. Recent documentation maintenance is not a support guarantee. [S1–S5]

The pinned manifest exposes a Haskell library and executable, with dependencies including Aeson, Parsec, containers, regex-tdfa and graph/dataflow support. Source builds use GHC/Cabal; prebuilt executables do not require users to compile the Haskell project. The README documents package-manager, binary and container installation routes for multiple operating systems, editor integrations, CI use and a web demo. None were installed or executed. [S1, S4]

The actual LICENSE contains GNU GPL version 3; the pinned manual states version 3 or later, while the manifest labels GPL-3. Preserve applicable licence notices and satisfy source-distribution obligations when conveying covered software or modifications; inspect dependencies and the exact distributed artifact separately. Running a tool to inspect a script is not a licence grant over that script. This publication contains original research, not copied upstream code or assets. [S2–S4]

## Source findings: three distinct choices
The manual separates **following** sourced files (`-x`) from **emitting warnings inside** those files (`-a`). Shell dialect also changes the analysis; `sh` means POSIX sh, not whichever executable happens to be installed as the local default. A quiet finding set must therefore be interpreted against input scope, reporting scope and dialect. [S2]

Dynamic source paths may not be resolved. SC1090 describes a fixed `source` directive and limited handling of a leading dynamic path component; it is not general runtime path evaluation. SC1091 covers other reasons a helper cannot be followed, including unavailable files and access restrictions. Substituting `/dev/null` deliberately omits the helper from analysis; it does not validate that helper. [S6–S7]

Configuration matters: the development manual documents rc/editor configuration, environment defaults, exclusions, severity thresholds and optional checks. `--norc` disables configuration-file lookup, not script directives or every environmental influence. Exit statuses distinguish findings from processing/invocation problems. Neither a suppressed warning nor a processing failure is successful repair. [S2]

## Original synthesis: shortlist ShellCheck with an explicit coverage receipt
Conditional shortlist: use ShellCheck for reviewable shell-language diagnostics before separately authorized execution. Do not use a clean result as a security approval, proof of safe external commands, successful deployment or semantic correctness of the whole task. Static analysis can flag suspicious constructs without proving what real services, files, permissions or network endpoints will do.

Recommended workflow, not implemented:
1. Inventory entry scripts and intended helper files under an approved root. Record the actual shell dialect for each entry point. A Bash wrapper does not establish that every helper is portable POSIX shell.
2. Decide which helpers may be read and which must produce diagnostics. Treat source resolution and warning emission as two separate checklist items. Do not enable broad external following merely to suppress a message about an unavailable private path.
3. Record the executable version, input hashes, resolved helper inventory, dialect, configuration sources, suppressions, optional checks and severity policy. Review new inline disable directives as code changes, not as automatic fixes.
4. Preserve exit status and diagnostics. Classify an unreadable helper or unresolved dynamic dependency as incomplete coverage unless a reviewed contract explicitly excludes it. Do not relabel an exclusion as remediation.
5. Review proposed fix diffs before applying them. Then run separately approved synthetic behavior tests for the script's actual task. Never execute a generated maintenance command solely because the linter found nothing.

Original future application: an agent emits a shell-review receipt with two independent outcomes, language diagnostics and effect authorization. For example, a clean wrapper that invokes an unapproved destructive external utility must still fail the action gate. A changed helper invalidates the old receipt even if the entry script is unchanged. This extends evidence tracking into shell boundaries without claiming ShellCheck provides sandboxing.

## Integration, costs and alternatives
Local CLI analysis needs a trusted installed executable and authorized readable inputs, not a model API, cloud server, account or MCP connection. A shell-capable agent can use its existing command interface; a new MCP wrapper is unnecessary for this narrow use. Container use introduces runtime and mount choices. The online demo would receive submitted script text, so use synthetic examples rather than private operational scripts. [S1]

Operational costs are local/CI compute, memory, output storage and review time; no mandatory paid service is proposed. Large generated scripts need explicit resource limits and coverage accounting. No resource measurements or speed comparisons were made. A shell's syntax-only check is a narrower alternative, while behavioral tests address a different layer. A formatter such as shfmt addresses presentation rather than replacing ShellCheck's diagnostics; it is not independently shortlisted here. [S1]

## Later acceptance experiment — unperformed
Create a synthetic entry script plus a helper containing a known diagnostic, a missing helper, a dynamic source path, a deliberately suppressed issue and a dialect-dependent construct. Compare explicit source/reporting policies, processing errors and emitted file identifiers. Add a harmless fake external command whose behavior violates the task contract despite clean shell syntax. Verify that lint success cannot authorize execution. No real credentials, production services or destructive commands are needed.

No installation, ShellCheck execution, runtime, integration, security, performance or accessibility tests were performed. Distribution artifact identity, exact stable-versus-development feature parity and application compatibility remain unverified. See sources.json, repos.json and qa.md for provenance and independent documentary review.
