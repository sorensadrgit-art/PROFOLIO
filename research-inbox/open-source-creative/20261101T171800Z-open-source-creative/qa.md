# Independent documentary QA

Status: **APPROVED_DOCUMENTARY**  
Run: `20261101T171800Z-open-source-creative`  
Reviewed: 2026-11-01, approximately 17:20 UTC.

## Scope and result

Independently read research.md, sources.json, repos.json and the intentionally prepublication manifest.json. Re-fetched all ten cited source documents using read-only GitHub connector/web operations. No blocking factual discrepancy found. Approval covers documentary research only, not application suitability, deployment, runtime behavior, accessibility, performance or security certification.

## Actual source checks

- C1: Read the SHA-pinned cmdk README. Confirmed unstyled composable parts, custom filtering, Radix Dialog composition, application-owned shortcut, unique values/keys, no built-in virtualization, `shouldFilter={false}`, server-closed dialog guidance, client-component designation and manual-DOM-ordering caveat.
- C2: Independently opened tagged cmdk package manifest. Confirmed version 1.1.1, React/React DOM peers `^18 || ^19 || ^19.0.0-rc`, TypeScript declarations and the four listed Radix dependencies.
- C3: Read tagged LICENSE.md. Confirmed MIT text and notice-retention terms, copyright 2022 Paco Coursey.
- C4: GitHub latest-release API returned stable v1.1.1, published 2025-03-14T19:22:07Z, with the cited IME fix. Web access to this API endpoint failed; the read-only GitHub connector succeeded.
- C5: Read cited commit metadata and independently queried main. Both returned dd2250ed608443e8f32bafc5fa2d1d07a3746aa3, dated 2025-10-29T04:13:58Z.
- K1: Read the SHA-pinned kbar README. Confirmed provider/action structure, keywords/callbacks, nested actions, shortcuts, animation and virtualized result rendering. Report properly separates maintainer accessibility/performance claims from tests not performed.
- K2: Independently opened tagged kbar package manifest. Confirmed version 1.0.0, React/React DOM peers 17/18/19, TypeScript declarations, Radix Portal, TanStack React Virtual, Fuse 7, fast-equals and tiny-invariant.
- K3: Read tagged LICENSE. Confirmed MIT text and notice-retention terms, copyright 2021 Tim.
- K4: GitHub latest-release API returned stable v1.0.0, published 2026-08-10T16:10:35Z. Release notes support React 18/19, Fuse 7, virtualization replacement and multi-word/diacritic changes. Web API fetch failed; GitHub connector succeeded.
- K5: Read cited commit metadata and independently queried main. Both returned 26ec0f49f92ab34fa6ab59392782d56020f28098, dated 2026-08-10T15:59:57Z.
- The legacy pacocoursey/cmdk repository endpoint returned a permanent-move response; dip/cmdk content was independently accessible.

## Novelty

Read live `sorensadrgit-art/PROFOLIO`, branch `research-data`, `research-inbox/catalog.json`, blob SHA `037e278c72dfde29f69e6afb29d1e4c75ecefce8`. Confirmed 24 package entries and two prior open-source-creative packages: Embla/Keen carousel selection and PhotoSwipe/Yet Another React Lightbox detail viewing. The command-menu selection/action-ownership topic is distinct at catalog title/summary level. This is not a whole-corpus textual plagiarism audit.

## Structure, privacy and editorial review

- Python JSON parsing succeeded for all three JSON artifacts.
- Confirmed ten unique source IDs, two projects, matching run IDs, license-source references and all ten source URLs present in Markdown.
- Manually inspected Markdown headings, table, links, limitations and proposed acceptance checks. No broken structural syntax found; no rendered-layout test performed.
- Read all candidate content and ran patterns for private-key headers, common GitHub/AWS/OpenAI credential forms, credential assignments and email addresses. No matches found. No private contact data, copied media or raw upstream payloads are included. Pattern scanning is not proof of absence of every possible secret.
- Recommendations and implementation proposals are labeled original/untested. No installation, runtime, integration or benchmark result is falsely claimed.
- Minor nonblocking typography: consider adding spaces in “18 or19”, “React18/19”, “DOM17–19”, “August2026”, “peers17–19” and “from1.0.0”.

## Publication boundary and limitations

Manifest draft/pending state and null publication receipts are intentional at this review stage. Publisher must update publication/review metadata and independently verify the eventual commit/catalog write. This reviewer made no GitHub writes, installed nothing, ran no project code and edited only qa.md. Source metadata are point-in-time observations; mutable release endpoints/tags may later change. No dependency vulnerability scan, browser operation, accessibility test or legal review was performed. No blocking fixes requested.
