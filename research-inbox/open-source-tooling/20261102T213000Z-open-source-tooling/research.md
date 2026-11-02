# A frozen lockfile is not a clean Python environment

## Question and decision
When evaluating uv for coding-agent CI, what separately establishes lock freshness and environment exactness? **Shortlist uv for a controlled trial, not deployment approval.** A retained virtual environment and a lockfile are different evidence objects. This is original documentary research, not a benchmark or a tested installation recipe.

Run: `20261102T213000Z-open-source-tooling`; scheduled 2026-11-02 21:30 UTC. Evaluation began before that slot, on 2026-11-02 around 21:25 UTC. The live catalog contained 90 packages; all 11 earlier tooling reports and the live topic directory were inspected. No same-ID artifact or unfinished topic directory was found. Prior topics concerned browser tools, evaluators, MCP sessions, secrets, search, JSON, shell diagnostics, benchmarking, codemods, workflow linting and task runners—not Python environment consistency.

## Source findings
The pinned official sync documentation [S1] distinguishes these controls:
- `--locked` verifies that the lock matches project metadata and errors if an update is needed. `--frozen` uses the lock without that freshness check.
- `uv sync` is exact by default: it removes extraneous packages. `uv run` is inexact by default, retaining extras; `--exact` changes that behavior.
- `--no-sync` bypasses environment synchronization. Optional extras are not selected by default, while the dev group is; selection matters.
These are documented behaviors, not observations from executing uv.

The project environment defaults to `.venv`. An absolute `UV_PROJECT_ENVIRONMENT` path is used directly; sharing it across projects risks overwriting the environment. The maintainers explicitly discourage targeting the system Python environment because synchronization may remove packages and break it. [S2]

## One actionable shortlist
**astral-sh/uv** — Rust-based Python package/project management, Python version management and tool execution. Official README supports Linux, macOS and Windows and describes standalone and Python-package installation routes; prebuilt installation does not require a Rust toolchain. [S3] The inspected workspace declares Rust edition 2024, minimum Rust 1.97.0 and dependencies including clap, reqwest, serde and Tokio; those are declared requirements, not a reproduced dependency inventory. [S4]

- **Version/provenance:** latest-release endpoint returned 0.13.6, published 2026-10-27T00:47:49Z [S7]. Documentation/source inspected at `9b11efe914e01b9dfaa53beebc2180f37dafb19b`, committed 2026-11-02T20:02:08Z [S8]. Workspace uv version is also 0.13.6, but this later source snapshot is not proof of released-binary parity.
- **Licence:** `MIT OR Apache-2.0` in the workspace manifest, with both actual licence files inspected [S4–S6]. Choose and comply with a grant, including applicable notices; dependencies remain separately licensed. Public source is not unrestricted reuse.
- **Integration:** local CLI or CI process; explicit Python, platform, environment path and dependency selections belong in the job specification. No MCP server, hosted API, credentials or new service is required for this proposed local trial. Package indexes/network access are needed when required artifacts are not cached.
- **Demonstrations:** README command walkthroughs, not an interactive hosted demo tested here. No installation, dependency resolution or command execution was performed.
- **Costs:** no licence fee imposed by the inspected grants; compute, storage/cache, downloads and engineering maintenance remain operational costs. Monetary estimates are null.
- **Compatibility:** platform support is documented, not validation of every Python version, wheel, native extension or private index. Builds and repository-defined commands are execution surfaces; an exact environment is not a sandbox.
- **Maintenance:** recent release and commit are activity signals, not an SLA or assurance of absence of vulnerabilities. No release cadence or performance claim inferred.
- **Alternatives:** plain venv plus pip/requirements can be retained when already adequate; Poetry or PDM are candidate comparisons rather than independently evaluated shortlist entries. Compare equivalent dependency selections and clean-state policies before migrating.

## Original synthesis: two independent acceptance questions
1. Is the selected lock consistent with the declared project intent?
2. Does the installed environment contain only the selected dependencies and intended project installation?

A successful frozen invocation does not by itself answer the first; an inexact invocation does not by itself answer the second. Consequently a warm agent workspace may conceal an undeclared dependency. That last statement is a risk inference, not a reproduced bug.

**Untested recommendation:** use an isolated disposable environment for acceptance, establish metadata consistency, explicitly select dependency groups/extras and record whether the project is editable. Keep the actual environment path separate from system Python and other projects. Record uv binary version/hash, source checkout, pyproject/lock hashes, interpreter/platform, flags, resolved installed inventory, command and exit status. Treat this as an evidence receipt, not a security certificate.

## Proposed experiment and future application
In a synthetic fixture, add a dependency only to a reused environment, and separately change project metadata without refreshing its lock. Compare locked versus frozen and exact versus inexact modes, then run the same import/test in a fresh environment. Record selections so a missing optional group is not mistaken for resolver failure. Expected differences are hypotheses until executed; no commands or outcomes are certified here.

A future agent acceptance dashboard could display three independent states: metadata consistency, environment inventory match and test result. A failed state should preserve the receipt rather than silently “repair” the project and erase evidence. This is an original application proposal, with no integration implemented.

## Limitations and evidence
Sources retrieved 2026-11-02; source-level and release-level provenance are distinct. No runtime, performance, accessibility, integration, install, security penetration or cross-platform tests were run. Documentary QA approval concerns only this package. See sources.json for pinned links, repos.json for structured shortlist and qa.md for review.
