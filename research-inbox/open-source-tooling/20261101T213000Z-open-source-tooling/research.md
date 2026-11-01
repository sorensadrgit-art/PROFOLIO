# Zero findings is not publication clearance: a Gitleaks coverage receipt

Run: `20261101T213000Z-open-source-tooling` · scheduled 2026-11-01 21:30 UTC · actual research began 21:26 UTC, before the scheduled slot.

## Question and novelty
What must a coding-agent workflow record before treating a Gitleaks scan as useful evidence for a public artifact?

The live catalog contained 34 published packages; all three existing tooling reports and the topic directory were inspected. Browser control, agent evaluation and MCP session diagnostics are already covered. None addresses secret-scanner input coverage or suppression policy. The topic directory had no unfinished or same-ID package. Its setup README still says no completed package exists; the live catalog and directory supersede that stale description. This is a one-project discovery, not a comparative benchmark.

## Verified project snapshot
[Gitleaks](https://github.com/gitleaks/gitleaks) is a Go CLI for secret detection. Inspected default-branch commit: `5f6ec56cfa6f8c67648d863582c7ae2f206d5c1c`, October 23, 2026. GitHub's latest-release endpoint returned **v8.30.1**, published March 21, 2026. These dates describe observed metadata, not a support promise. Its current README explicitly says feature development has ended and future releases are security patches, with maintainer focus moving to Betterleaks. Do not rank this project as actively expanding from recent commit timestamps alone. [S1, S7, S10]

The pinned LICENSE is MIT: redistribution requires the copyright and permission notice; there is no warranty. Dependencies and separate integrations retain their own terms. Public source availability does not grant rights to scanned private material. The Go module declares 1.24.11 and includes Cobra/Viper, go-gitdiff, archive and regex libraries. That directive concerns source builds, not a requirement to install Go beside a prebuilt binary. [S2–S3]

Installation options include release binaries, source builds and containers; examples and hook instructions appear in the README. Git-mode operation additionally requires Git and the intended local history. No hosted service, model inference, API key or MCP connection is required for the direct local CLI workflow proposed here. No MCP server was evaluated. Container mounts, filesystem access and report destinations still need least-privilege review. Costs are compute, storage, CI time and triage; no price quote or paid service is proposed. Demos/examples were read, not run. [S1, S5–S6]

## Findings that change the acceptance contract

**Input is not interchangeable.** Source confirms history scanning consumes local Git patches; staged/pre-commit modes use diffs. Default history arguments include all local refs, but custom log options replace that default list. A directory scan and an stdin scan address different inputs. A current-directory result is not evidence about deleted historical text, while a history result is not evidence about an unpublished generated artifact. [S1, S5–S6]

Git documents that shallow cloning truncates history and normally limits branch coverage. **Inference:** scanning that local clone cannot establish the absence of matching material in unavailable history. Record fetched refs, shallow state and exact range rather than writing “repository clean.” [S9]

**Configuration changes the question.** Baselines suppress existing findings; allowlists, ignore files and rule selection also narrow what is reported. A custom configuration can replace the default rules unless configured to extend them. A new empty report can therefore mean “no additional unsuppressed matches,” not “old findings were remediated.” [S1, S4]

**Defaults can be misdocumented.** The pinned README's help example says decoding depth defaults to zero, but both inspected master source and v8.30.1 root-command source set it to **5**. Archive depth is zero in pinned source. Redaction's stored default is zero; a bare `--redact` flag selects 100. Therefore explicitly select decoding/archive scope and redaction, and verify the installed binary's help/version later. Do not extrapolate these source observations into a tested binary result. [S1, S4, S8]

**Completion matters separately from count.** The inspected command distinguishes partial scans and returns an error status when an error reaches its final handler; findings normally also produce a nonzero status, configurable by flag. Counting JSON findings alone discards that distinction. This source inspection is not proof that every internal read failure propagates correctly. Treat warnings, skipped inputs, timeout and missing report as reviewable failures, not successful clearance. [S4]

## Original synthesis: two receipts, one publication decision
Proposed, not deployed:

1. **Coverage receipt:** artifact hashes, scanner version/build, mode, local refs/range/shallow state where applicable, rule/config hash, suppression inventory, size/decoding/archive limits, exit status, warnings and completion status. Never put secret values in this receipt.
2. **Review receipt:** a human-readable decision about confidential prose, private identifiers, credentials, permissions and provenance. The scanner only contributes pattern evidence; it cannot decide whether an ordinary sentence is confidential or authorized for publication.

Scan the exact export bytes before publication, not merely the source folder that produced them. Keep scan reports private until separately sanitized: even redacted findings can retain paths, surrounding context or other sensitive metadata. A baseline should have a reviewer, justification and expiry/review date. Never allow agent-generated ignore rules to silently approve the agent's own output. These are original engineering recommendations, not guarantees offered by the tool.

## Small shortlist and alternatives
**Conditional shortlist: Gitleaks** for a pinned, reviewable local secret-pattern gate when its existing rules and maintenance posture suffice. It is not a complete DLP, confidentiality or authorization system. The maintainer names Betterleaks as the future direction, but that repository's licence, compatibility and behavior were not evaluated here; it is a follow-up candidate, not an endorsed replacement. A small bespoke pattern check plus explicit content review is a limited alternative for a narrowly defined export, not an equivalent scanner. Do not install another tool solely to make a checklist green.

Original future application: an agent-produced evidence bundle carries separate artifact, scan-coverage and publication-review receipts, bound to the same hashes. Changed bytes invalidate both approvals. This prevents a passing scan of an earlier draft from authorizing a later export.

## Later acceptance experiment — unperformed
Use only synthetic fixtures: a detectable marker in current output; a marker removed from old history; an ignored finding; an encoded marker; an archive; a deliberately truncated history; and an intentional scan error. Assert which modes and configured limits should include each input, then verify exit status, report contents and redaction. Measure runtime and resource usage only then. No live credentials or production repositories are needed.

## Limitations
Documentary research only: no installation, binary execution, security exploit, performance, integration, accessibility or traffic tests. No claim of complete secret detection, safe private-data handling or vulnerability-free dependencies. Sources record actual retrieval before the scheduled slot rather than inventing a future evaluation date. Independent review and publication checks are recorded in qa.md.

