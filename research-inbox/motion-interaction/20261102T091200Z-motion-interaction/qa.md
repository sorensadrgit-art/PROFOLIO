# Independent documentary QA

Outcome: **APPROVED_DOCUMENTARY**  
Reviewed: 2026-11-02T09:32:11Z  
Reviewer: separate QA worker (not the research author)

## Checks completed

- Reopened all six primary source URLs. Checked GSAP option placement/default against `gsap.fromTo()` parameters and immediate-render note; checked the timeline variant, the maintainer lesson and `Timeline.set()` default. Core claims are supported.
- Independently read the pinned repository package metadata: version 3.15.0, ESM/CommonJS exports and TypeScript declaration paths match. Independently fetched the exact commit metadata and confirmed 2026-04-13T13:08:30Z. Commit time is not represented as release time.
- Checked actual licence restrictions and effective/modified dates. No unrestricted-open-source or MIT claim is made.
- Read the full draft for source/synthesis separation, untested-example labelling, copyright scope and useful limitations. No copied assets or substantial source text found. No unsupported performance or browser-certification claim found.
- Parsed both JSON files successfully; six unique source IDs agree with manifest count. Manifest review status was pending at QA time and is to be finalized by the author without changing substantive findings.
- Bounded regex scan plus manual review of the three package files found no credentials, private keys, token patterns, private addresses, account emails, host details, private filesystem paths or operational records. This is not an exhaustive security audit.

## Scope and limitations

No corrections required. Live catalog/directory duplicate checks were author-owned, not independently repeated here. The author must still finalize manifest, revalidate/scan final files and perform atomic publication and remote byte/catalog verification. Repository version provenance does not verify an npm artifact. No browser, runtime, installation, integration, performance or accessibility test was executed. Approval covers only this bounded documentary package, not deployment or any separate approval workflow.
