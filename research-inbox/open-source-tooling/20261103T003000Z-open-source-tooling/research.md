# A successful pre-commit run is not proof that every required hook ran

Run `20261103T003000Z-open-source-tooling`; scheduled 2026-11-03 00:30 UTC. Research started around 00:25 UTC, before that slot; evaluation date 2026-11-03.

## Question and shortlist
What evidence should a coding agent retain before treating a successful pre-commit invocation as a required-check gate?

Shortlist one project: [pre-commit](https://github.com/pre-commit/pre-commit), a Python framework for multi-language Git hooks. Its useful role is consistent hook orchestration, not independently establishing the completeness of an acceptance policy. [S1, S4]

The live catalog held 97 packages. All 12 earlier tooling reports were fetched and their topics reviewed, alongside the live topic tree. No same-ID or unfinished topic directory was found. Earlier packages examine individual analyzers, JSON, scanners and environment consistency; none examines hook selection and skipped outcomes as a required-check contract.

## Source findings: success has a coverage boundary
**Selection precedes execution.** Pinned run.py filters hooks by requested ID/alias and stage. Global include/exclude rules feed a classifier; each hook adds filename regex and file-type filters. A requested all-files inventory therefore does not disable those filters. [S2]

**All-files is a Git inventory, not a disk crawl.** The inspected implementation routes all-files to get_all_files(), implemented through Git ls-files. Ordinary default selection uses staged files; other paths cover explicit files, revision differences, merge-conflict files and special hook stages. Inference: an arbitrary untracked export outside that inventory cannot be certified by saying “all files passed.” Bind a receipt to the actual relevant input set. [S2, S3]

**Skipped is not failed.** The pinned single-hook runner handles explicit SKIP IDs/aliases and no-matching-files without always_run as skipped outcomes with retcode zero. Required-hook execution is consequently a separate invariant from aggregate process success. always_run changes the empty-file branch; it does not override explicit SKIP. A wrapper must not relabel skipped as executed-and-passed. [S2]

**Passing filenames is not constraining effects.** With pass_filenames disabled, the implementation invokes the entry without selected filenames. Even when filenames are supplied, invocation arguments do not establish what a hook actually reads or writes. The second sentence is an engineering inference, not an upstream sandbox guarantee. [S2]

**Autofix can be a failed check.** The runner compares Git diff snapshots around execution and fails when a hook returns nonzero or the observed diff changes. That mechanism is not a complete filesystem/network-effect monitor. Review a generated patch and rerun the approved checks; a modified file is not automatically an accepted repair. [S2]

## Project evaluation
- **Technology and compatibility:** inspected setup.cfg declares version 4.7.0 and Python >=3.11, with cfgv, identify, nodeenv, PyYAML and virtualenv dependencies. These are constraints, not a resolved dependency audit. Git and the chosen hook language/runtime requirements still matter. No OS compatibility matrix was executed. [S3, S4]
- **Maintenance/version:** latest-release metadata returned v4.7.0, published October 13, 2026 at 15:35:39 UTC. Inspected main commit 8cb5845c835b1ff72240eb2484239411ec481d92 is dated October 20, 2026. Main sharing a version number does not prove release-artifact equivalence or future support. PyPI artifact provenance was not checked. [S4, S6, S7]
- **Licence:** actual pinned LICENSE is MIT: preserve its copyright and permission notice in copies/substantial portions; warranty is disclaimed. Hook repositories, dependencies and external assets require their own licence checks. This report copies no upstream implementation or assets. [S5]
- **Installation and demos:** official docs show pip and zipapp routes, hook configuration and example terminal output. Hook environment setup can install additional software. Examples were read, not run. No installation or Git-hook change was made. [S1]
- **Integration/API/MCP:** local CLI orchestration is the evaluated interface. No new server, account, paid service, API key or MCP connection is proposed. Existing command-capable agents could invoke it only after hook/configuration and effect authorization; this is untested integration advice.
- **Costs:** local/CI execution, environment downloads/cache storage and review time are operational costs. Hook programs can have separate external effects or charges. No measured resource claim or price estimate is made.

## Original synthesis: required-check receipt
Proposed workflow, not implemented:
1. Define required hook identities and stages independently of the submitted configuration. Record the reviewed config hash, source revision and allowed input root.
2. Record the actual tool build, hook revisions, language versions, additional dependencies and effective skip/selection policy. Pinning a hook commit improves source identity but is not a full transitive environment lock.
3. Classify each required hook as executed/passed, executed/failed, skipped, not selected, interrupted or unavailable. Require explicit execution evidence and expected input coverage, not merely a zero process exit.
4. Review all detected modifications against the authorized patch scope. Repeat checks on the accepted bytes, retaining the earlier failure rather than rewriting its history.
5. Enforce permissions outside the hook manager. Treat repository configuration and hooks as executable untrusted inputs until reviewed.

The official docs provide frozen revision hashes through autoupdate --freeze and warn against indiscriminate global hook activation on untrusted repositories. These mechanisms motivate review; neither grants sandboxing. [S1]

## Alternatives, future application and unperformed experiment
Direct invocation of a small reviewed linter/test list may be simpler when only a few checks are required. Existing CI jobs can independently own mandatory acceptance. These are conceptual alternatives, not independently benchmarked products. pre-commit is conditionally useful when its multi-language environment and hook lifecycle management reduce duplicated orchestration.

Original future application: a coding-agent handoff includes an expected-versus-observed hook matrix and hashes of accepted outputs. A green process with a missing mandatory row is incomplete evidence, not a green release.

A later synthetic trial could include a matching file, an excluded file, an untracked export, an empty selection, an explicitly skipped hook and a harmless formatter. Assert distinct statuses and review the formatter patch before rerunning. No such trial occurred here.

## Limitations
No installation, hook execution, runtime, integration, performance, accessibility, security or network-effect tests were performed. No dependency licence closure or binary provenance certification. Source contents were treated as untrusted evidence, not instructions. Documentary approval refers only to this research task; see qa.md.
