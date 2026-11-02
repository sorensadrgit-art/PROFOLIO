# Independent documentary QA

Run: `20261102T121200Z-motion-interaction`  
Reviewer: separate QA worker, not the research author  
Reviewed: 2026-11-02T12:13:39Z  
Outcome: **APPROVED_DOCUMENTARY** for the reviewed research and source metadata; publication still requires the publisher's final allowlist, JSON and remote verification checks.

## Checks actually performed

- Read research.md, sources.json and manifest.json independently.
- Reopened all five source resources: official gsap.to(), Tween.invalidate(), actual licence, commit-pinned package metadata and GitHub commit metadata. The invalidate page initially returned a fetch error; one retry succeeded. The material claims match those sources.
- Confirmed the distinction between function-value sampling, repeatRefresh and timing. The yoyo exclusion and duration/delay/stagger exception are explicit in the official documentation. The proposed policy is labelled original and untested.
- Confirmed repository version 3.15.0, module/type metadata and commit date. No release date, npm-latest, installed-version or maintenance guarantee is inferred.
- Confirmed custom licence restrictions and dates; the package does not present public source access as unrestricted reuse.
- Independently reread the catalog at base commit 3a1478b6d1a3541bdfdcc23cb20876edae1eb69e and fetched all eleven indexed motion research bodies. None contains repeatRefresh; titles and stated scopes do not duplicate this focused question. Author's recursive-tree inspection supplies the unindexed-package check; publisher must recheck current head before writing.
- Parsed both JSON files successfully and confirmed five unique source IDs.
- Ran a bounded pattern scan over the three reviewed package files for private-key markers, GitHub/AWS token formats, quoted credential assignments, private IPv4 ranges and email addresses: zero findings. Manually reviewed their prose for private account/host details, raw logs and copied assets: none found.

## Correction and limits

The author's pinned-package source retrieval/evaluation was corrected to 2026-11-02T12:12:44Z before approval, distinguishing it from the earlier master-branch fetch. Scheduled time is not misrepresented as actual evaluation time.

No blocking factual issue remains in the reviewed package. Scan coverage is bounded, not a guarantee that every possible sensitive format is detectable. No runtime, browser, performance, accessibility, integration, deployment or npm-artifact test was performed. No Gemini approval is claimed. This QA is not permission to deploy. The manifest was reviewed in draft state; publisher may update status/check receipts after completing its final checks without changing the research claims.
