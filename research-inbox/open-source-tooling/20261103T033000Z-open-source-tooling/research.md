# Git worktrees separate checkouts, not agent authority

Run `20261103T033000Z-open-source-tooling`; scheduled 2026-11-03 03:30 UTC. Actual research began around 03:28 UTC, before the slot. Evaluation date: 2026-11-03.

## Question and shortlist
What isolation does a Git linked worktree provide when two coding workers operate on one repository?

Shortlist one project: [Git](https://github.com/git/git), specifically its built-in worktree facility. Use it to organize independent checkouts; do not equate that organization with process confinement or authorization. This is a documentary discovery, not a deployment or a tested orchestration design.

At the original 2026-11-03 research check, the live catalog held 102 packages, including 13 tooling packages. All 13 prior reports were fetched and their questions reviewed. The live topic directory contained those 13 package directories and README.md, with no same-ID or unfinished directory. Existing topics concern browser interfaces, evaluators, diagnostics, scanners, search, JSON, shell lint, benchmarks, codemods, workflow checks, recipes, environments and hooks. None specifically evaluates linked-worktree ownership. No existing package was changed.

## Source findings
- A linked worktree has its own HEAD and index while sharing other repository data. Most refs under refs/ are shared, with documented exceptions for bisect, worktree and rewritten refs. [S1, S2]
- Repository configuration is shared by default. The worktreeConfig extension adds per-worktree configuration read after common configuration; its documented migration caveats include core.worktree and core.bare. Unsupported older Git versions reject this extension. [S1, S3]
- The worktree lock command protects administrative records from pruning and blocks ordinary move/remove operations. Force options can override those protections. It is not described as an edit mutex or permission boundary. The inspected implementation writes a reason to an administrative locked file. [S1, S4]
- add normally refuses to check out a branch already in use elsewhere; --force overrides the safeguard. Stable machine-readable worktree listings support porcelain with NUL delimiters. [S1]
- The manual warns that submodule support is incomplete and discourages multiple checkouts of a superproject. Treat that warning as a compatibility gate, not a tested failure prediction. [S1]

## Original synthesis: separate ownership from layout
A separate index reduces accidental staging interference. It does not give a worker exclusive control over shared refs, common configuration, the filesystem, processes or credentials. That conclusion follows from the shared-data model and the absence of an OS isolation mechanism; it is not a security certification.

Proposed orchestration contract, unimplemented:
1. Allocate each worker a distinct worktree and branch from an explicitly recorded base commit. Keep worker identity, task scope and allowed paths in the coordinator's own assignment record.
2. Treat the worktree's locked marker only as lifecycle protection. Maintain an independent ownership lease and admission counter. A two-worker limit must be enforced by the coordinator, not inferred from the number of worktrees.
3. Give workers authority only over their assigned task branches. Restrict cross-worker or protected-ref updates, common configuration changes, worktree cleanup and integration to a designated coordinator. A documented convention is insufficient against untrusted processes; enforce filesystem/process/network restrictions separately where required.
4. Record source revision, worktree identity, branch, changed paths, accepted diff and check receipts at handoff. Revalidate the destination head before integrating rather than assuming the starting commit still describes it.
5. Inspect worktree-aware paths through Git rather than assuming every .git entry is a directory. Retain unfinished work until its output is accepted; do not use forced cleanup as normal recovery.

## Project evaluation
**Purpose and technology.** Git is distributed version control, not an agent framework. The inspected worktree command is implemented in C; the installation documentation describes shell scripts, optional Perl features and build-dependent libraries. No full language/dependency inventory was established. [S4, S6, S7]

**Version and maintenance.** Research is pinned to v2.56.0 commit a018953688f1b10bddf91bff8747068f5f4746a4, dated 2026-09-28 04:20:04 UTC. A separate live commit-list read observed ebdc087dc99d68bb340c282b2ba5f722def28d19 dated 2026-11-01 17:25:24 UTC. This establishes recent activity, not a support promise. Release publication time and newest release were not independently established. The inspected tag is not asserted to be newest. [S8, S9]

**Licence and reuse.** Actual COPYING specifies GPL version 2 unless explicitly stated otherwise; README notes some components have compatible different licences. Redistribution or modification requires checking applicable notices and source obligations. This report does not redistribute Git code, binaries or assets and is not a legal opinion. Public source availability is not unrestricted reuse. [S5, S7]

**Installation and compatibility.** Worktree is part of Git, not a separate npm/MCP installation. Prefer an approved platform Git distribution if evaluating later. Source-build instructions describe make/configuration, required zlib and optional features such as curl HTTP transport and Perl integrations. Actual build requirements depend on selected features/platform. No installation, OS matrix, submodule trial or binary provenance verification occurred. [S6]

**Demos and interface.** The official worktree manual includes a temporary emergency-fix example. It was inspected, not run. The evaluated interface is a local CLI; no hosted API, account, MCP server or paid service is required for local worktree management. Remote repository access would have its own credentials and permissions. [S1]

**Costs and alternatives.** Expected costs are checkout/build storage, local compute and coordination effort; none were measured. Independent clones offer a different repository-data boundary but consume additional storage and still need OS permissions. A single serialized checkout is simpler when parallelism adds little value. Containers or separate machines address different isolation concerns and do not replace source/patch review. These are conceptual alternatives, not comparative tests.

## Future application and limitations
Original application: a small coding-worker controller exposes checkout identity separately from task ownership, with a hard admission counter and a coordinator-only integration step. A later disposable-repository trial could verify separate staging, shared configuration visibility, shared-ref visibility and normal lock-protected cleanup. It should also test interruption and stale handoff receipts. No such experiment was performed.

No runtime, performance, accessibility, integration, isolation or security tests were performed. JSON validation and bounded document scanning concern this package only. Source contents were treated as untrusted evidence. The retained package was independently revalidated on 2026-11-06 and prepared for publication pending the final publication gate. This preparation is not a claim of completed publication. Independent review outcome is recorded in qa.md.
