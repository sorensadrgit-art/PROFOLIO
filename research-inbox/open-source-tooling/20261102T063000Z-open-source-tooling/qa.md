# Independent documentary QA

- Reviewer: independent `qa_shellcheck` research-review agent.
- Reviewed at: 2026-11-02T06:40:00Z.
- Outcome: **PASS — documentary research only.** No substantive correction required to the technical findings. This is not runtime, deployment, security, licence-compliance, or compatibility certification.
- Scope: research.md, sources.json, repos.json and manifest.json; independently retrieved primary sources rather than relying solely on the author’s summaries.

## Independently reviewed sources

S1–S4 were fetched with the GitHub connector at the exact commit `919d37c4d77a7ce16cfc76ebdd781dd6e8081fa5`: README.md, shellcheck.1.md, LICENSE and ShellCheck.cabal. Direct web retrieval of their raw URLs failed with cache misses; connector retrieval succeeded. Canonical pinned links remain in sources.json.

S5 was independently checked through https://api.github.com/repos/koalaman/shellcheck/releases/latest. It returned the official v0.11.0 release, published `2025-08-04T00:27:19Z`, with changelog heading dated 2025-08-03. Commit metadata was independently retrieved from https://github.com/koalaman/shellcheck/commit/919d37c4d77a7ce16cfc76ebdd781dd6e8081fa5 and confirmed `2026-10-26T01:55:51Z` and a manual-documentation change concerning --norc/--rcfile.

S6 and S7 were independently opened through the web tool at https://www.shellcheck.net/wiki/SC1090 and https://www.shellcheck.net/wiki/SC1091. These are living wiki pages, not pinned revisions.

## Claim checks

1. **Source-following versus reporting: supported.** The pinned manual distinguishes -x/--external-sources (permits following files not supplied as inputs) from -a/--check-sourced (reports issues within sourced files). Files supplied as inputs can already be followed without -x. The research does not claim that -x alone emits every helper warning.
2. **Dialect: supported.** The manual explicitly distinguishes POSIX sh from the system shell and documents dialect-specific analysis. The recommendation to record dialect and scope is original workflow synthesis, not a claimed built-in coverage guarantee.
3. **SC1090/SC1091 gaps: supported.** SC1090 documents fixed source directives and stripping one leading expansion followed by a slash, not general evaluation of runtime paths. SC1091 documents missing files, permission and source-policy failures. Substitution with /dev/null excludes the helper rather than validating it.
4. **Configuration and exit status: supported.** The inspected development manual documents rc files, EditorConfig, SHELLCHECK_OPTS, directives, exclusions, optional checks, severity and exit codes 0–4. --norc disables configuration lookup, including --rcfile, without promising to erase inline directives or environment defaults.
5. **Licence evidence: accurately differentiated.** LICENSE contains the GPL version 3 text; the manual says version 3 or later; the Cabal manifest labels GPL-3. The report preserves these distinctions rather than treating one metadata label as a complete artifact/dependency legal determination. GPL section 2 distinguishes running the program from conveying covered works. Distribution-specific compliance remains unverified.
6. **Version provenance: supported with appropriate caveat.** The development Cabal version is 0.11.0 while the inspected commit postdates the release. Matching strings do not establish stable/development feature parity. Exact binary or package identity was not checked.
7. **Other claims: appropriately bounded.** README supports documented installation/integration routes and its recommendation of shfmt for formatting; the Cabal file supports Haskell library/executable and named dependencies. Resource limits, receipts and action gates are clearly recommendations. No benchmark, tested integration or sandboxing claim was found.

## Artifact checks and corrections

All three JSON files parsed successfully. The seven unique source IDs match repos.json references and manifest.json source_count. Source URLs, commit/date/version distinctions and living-wiki limitations are present. The author reported correcting the non-substantive S5 spacing typo `dated2025` to `dated 2025`. No technical correction is required.

The author separately reported a bounded sensitive-data scan with no hits; this reviewer inspected the supplied text and found no apparent credentials or private operational data. Neither inspection is a guarantee that all sensitive content classes are detectable.

## Limitations and unverified assertions

No ShellCheck installation or execution, source build, runtime test, application integration, behavior test, performance measurement, accessibility test, binary signature/hash verification or deployment occurred. The tiny local Python check only parsed the research JSON and compared its source references; it did not exercise ShellCheck.

The author's live-catalog, six-prior-package and duplicate-directory checks were not independently repeated by this reviewer; the novelty/process statements depend on that separate evidence. Repository ownership, distribution dependencies, exact stable/development parity and all operating-system combinations were not exhaustively audited. Approval applies to the research's bounded documentary claims only, not future installation, execution or publication authority. manifest.json was still in pending-review state during review and should be updated by the publishing owner after this result.
