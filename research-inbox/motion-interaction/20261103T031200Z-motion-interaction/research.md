# Timeline positions are placement instructions, not live dependencies

Run: 20261103T031200Z-motion-interaction  
Scheduled: 2026-11-03T03:12:00Z · Evaluated: 2026-11-03 · Documentary research only.

## Question and gap
When a cinematic sequence changes, do relative positions and labels keep later animations attached to the original timing relationship?

Treat them as insertion-time placement, not a reactive dependency graph. The live catalog, all sixteen motion reports and topic directory were read first. Earlier work covers initialization, repeating values, overwrite and Flip transactions; this note isolates sequence placement and later retiming. All observed topic directories were cataloged published runs; this run did not exist. No unfinished same-topic package was observed.

## Source findings
- The default position appends at the timeline end. In contrast, "<" and ">" reference the most recently inserted animation's start/end, even if that animation is not last chronologically. Unknown label names create labels at the current end. [S1]
- Percentage syntax has two bases: "<25%" uses the previous animation's total duration, whereas "<+=25%" uses the inserted animation's total duration. Percentage positioning is documented from GSAP 3.7.0. The official page includes interactive demos; they were not executed. [S1]
- In the inspected 3.15.0 source, _parsePosition returns a number; _addToTimeline stores the resulting start plus delay. addLabel assigns a numeric label position. There is no retained label-reference relationship in those paths. Changing a label entry does not automatically reinsert children previously placed there. This is a bounded source inference, not a runtime test or a claim that animation start times never change. [S2]
- smoothChildTiming can reposition children to maintain playhead continuity during playback changes. That behavior is distinct from re-solving a storyboard's original relative expressions. Timeline nesting and timeScale provide whole-sequence composition/speed controls. [S3]
- shiftChildren explicitly moves qualifying child start times and can move labels too; adjustLabels defaults false. Its threshold is a child's start time, not every animation whose span crosses the threshold. [S4, S2]

## Original synthesis: compile a timing plan
Keep the editorial timing plan as application-owned data and build an owned timeline from it. A relative string is convenient authoring syntax, but should not be the only record of why two beats align.

1. Give each meaningful beat a stable identity. Define durations and intended anchors before constructing the sequence.
2. Use explicit labels for semantic anchors and "<"/">" for genuinely adjacent construction. A helper that inserts another tween can change what "recent" means for the next insertion.
3. When timing inputs materially change, rebuild the owned sequence from the plan, or explicitly reposition the intended children. Do not assume renaming/moving a label updates existing placements.
4. Choose a playback migration policy: restart, finish current beat, or map semantic beat plus local progress. Preserving overall normalized progress is not necessarily preserving the same story moment after durations change.
5. Use timeScale for uniform speed changes when the relative arrangement should remain intact. Use shiftChildren only when a deliberate splice matches its start-time threshold; decide separately whether labels should move.
6. Retire the old timeline through the existing ownership/cleanup policy. Rebuilding timing must not create a second active writer or replay consequential callbacks accidentally.

Derived example, not executed: insert A at time zero for four seconds, then B at zero for one second. Adding C at ">" references B's end, while appending with "+=0" uses the sequence end. A seemingly harmless helper insertion can therefore change a later relative anchor. This is a proposed review fixture, not a measured result.

## Fit, requirements and alternatives
GSAP is a JavaScript animation/sequencing library. Pinned package metadata reports 3.15.0, ESM/CommonJS entry points and TypeScript declarations; no dependencies field is declared. Observed repository head 13e2b790546426a1a2e0e9b409f3f8dc6d6611f2 is dated 2026-04-13T13:08:30Z. The latest-release endpoint returned 404, so release date is unknown. This establishes an observed source state, not a support guarantee. [S5, S6]

Installation supports npm or script loading. A future adoption can pin gsap@3.15.0 and initialize DOM targets only in the browser lifecycle; a timing-plan builder itself can use plain data. No hosted API, MCP server, account connection or paid service is required for this proposed client-side sequence. Nothing was installed. Actual framework/browser compatibility remains untested. [S7]

GSAP uses its Standard No Charge licence, not unrestricted MIT reuse. Commercial websites are permitted subject to terms, including restrictions on specified competing no-code visual animation builders and removing proprietary notices. Effective date 2025-04-30; modified 2025-05-30. Do not copy showcase assets. No licence fee is indicated for this ordinary proposed site use; bundle, rendering and engineering costs remain unmeasured. [S8]

Alternatives: keep a static final layout when choreography adds little value; CSS transitions suit isolated state changes. For a complex sequence, a rebuilt timeline favors clarity; targeted retiming may retain continuity but demands more bookkeeping. These are untested design choices, not a comparative benchmark. Future application: an editorial case-study sequence compiled from reviewed beat data, with a development report of expected versus actual start times.

## Acceptance exercise — NOT RUN
- Compare chronological end against last-inserted end using the A/B/C fixture.
- Insert a helper tween between beats and check intended anchors remain stable.
- Move a label after adding a child; inspect existing child position versus a newly inserted child.
- Compare "<25%" and "<+=25%" with unequal durations and finite repeats.
- Splice at a threshold crossed by a long-running child; verify start-threshold behavior and optional label adjustment.
- Change the timing plan during playback and verify chosen beat continuity, callback policy, teardown, reduced-motion fallback and keyboard behavior.
- Profile layout/paint and input responsiveness on target devices before claiming performance.

No runtime, integration, accessibility, visual or performance tests were performed. Documentary approval is not deployment permission or Gemini approval. Source pages are mutable unless commit-pinned; unknown dates remain null. Only original summaries and an unexecuted acceptance plan are published.

