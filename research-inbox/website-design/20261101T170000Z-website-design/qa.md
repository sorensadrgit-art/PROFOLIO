# Independent documentary QA

Run: 20261101T170000Z-website-design  
Reviewed: 2026-11-01T17:08Z  
Decision: APPROVED_DOCUMENTARY for bounded research publication; no substantive blockers.

## Scope and performed checks

Read the complete local `research.md` and `sources.json`. Independently opened all four actual publisher URLs with the web tool, then read additional notification and status-message sections needed to check details. Ran Python JSON parsing, uniqueness/citation assertions and a limited common-credential-pattern scan. No application files were edited, no GitHub write was made, and no live form or assistive-technology test was performed.

| Check | Result |
|---|---|
| Source pages independently retrieved | 4 of 4, all W3C primary documentation |
| Unique source IDs / URLs | 4 / 4 |
| Source URLs cited in research | 4 of 4 |
| JSON parsing and structural assertions | Passed |
| Main source-backed findings checked | 4 of 4 supported |
| Common credential-pattern matches | 0 in both reviewed files |
| Substantive blockers | 0 |

## Evidence review

- S1: Explicit association and contextual exceptions for hidden labels are supported by [Labeling Controls](https://www.w3.org/WAI/tutorials/forms/labels/), especially its explicit-label and hiding-label sections.
- S2: Summary links, corrective guidance, local association and alternative validation timing are supported by [User Notification](https://www.w3.org/WAI/tutorials/forms/notifications/). Its displayed update date is 03 June 2022, matching the source record.
- S3: Status-message scope, focus-changing dialogs and removed waiting text are supported by [Understanding 4.1.3](https://www.w3.org/WAI/WCAG22/Understanding/status-messages.html). The report correctly avoids treating the informative explanation as a separate conformance standard.
- S4: Type-versus-purpose and the distinction between exposed purpose and actual autofill are supported by [Understanding 1.3.5](https://www.w3.org/WAI/WCAG22/Understanding/identify-input-purpose.html).

Publisher names, URLs and WCAG 2.2 applicability are consistent. Null release dates and commits avoid invented provenance. Source timestamps are the author's collection metadata, not independently reconstructed historical retrieval logs; this review independently retrieved the pages at review time. Living-page revision limitations are appropriately disclosed.

## Originality, safety and limits

Against the supplied prior-package scope (editorial foundation with only a brief contact-form recommendation, forced colors, Grid Lanes and headline wrapping), this proposal adds a distinct contact-form recovery/status contract. It does not merely repeat the visual-design topics. This reviewer did not independently reopen the catalog or prior packages; catalog counts and prior-reading assertions remain the author's run evidence, not a separate QA certification.

Proposed draft retention, uncertainty handling and acceptance-versus-delivery wording are explicitly original recommendations, not attributed backend guarantees. The markup is clearly unexecuted and incomplete; runtime, accessibility, usability, conversion and framework-integration claims are not asserted as verified results. Future checks are labeled unexecuted. No substantial copied source passage or third-party asset was observed.

Manual review found no private enquiry content, personal contact details, credentials or internal diagnostic URLs in the two publication files. Pattern scanning is only supplementary and is not an exhaustive secret or security audit. Documentation correctly cautions against publishing enquiry contents.

## Editorial note

One non-blocking wording issue was identified: “Keep three concepts separate” introduced five states. The author corrected it to “Keep these states separate:” and this reviewer verified the correction in the local file. No outstanding required corrections identified. Approval is documentary only, not authorization to implement, deploy or certify a form.
