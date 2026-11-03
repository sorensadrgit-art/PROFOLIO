# Flip layout transitions: commit the new order before animating its appearance

## Question and novelty
How should a sortable portfolio grid use GSAP Flip without confusing the visual transition with the underlying reorder?

The live catalog and 15 existing motion reports were read before selection. They cover route snapshots, motion ownership, drag cancellation and GSAP timing, but not Flip's capture/mutate/animate transaction. This is a focused engineering note, not a tool shortlist. Scheduled slot: 2026-11-03T00:12:00Z; evidence evaluated 2026-11-03. No application was changed.

## Primary-source findings
- **Capture has an interruption side effect.** `Flip.getState()` records geometry, transforms and opacity; extra CSS properties require `props`. By default, an affected running Flip is completed and killed to keep its inline styles out of the next state. Therefore it is not an inert measurement probe during an active transition. `kill:false` transfers that coordination responsibility to the caller. [S02]
- **The destination must already exist.** The documented sequence is capture, change DOM/styles, then `Flip.from()`. That method offsets targets toward the saved appearance and animates those offsets away; it returns a timeline. Default resizing uses width/height; `scale:true` selects transform scaling. [S03]
- **Options change geometry, not application meaning.** `absolute` can target a subset; making everything absolute can collapse document flow. `nested:true` prevents parent/child movement compounding. These are distinct problems, not universal optimization switches. [S03]
- **Coordinate multiple owners.** `Flip.batch()` sequences all captures before state changes, then creates animations, avoiding one component's mutation contaminating another component's measurement. [S04]
- **Playback is separate.** Timeline `reverse()` reverses animation playback. [S05] Inference from S03/S05: it does not by itself undo an external array reorder or DOM mutation performed before timeline creation. Custom callbacks can of course implement application changes; do not confuse such code with an automatic Flip guarantee.
- **React timing matters.** `useLayoutEffect` runs after DOM commitment and before repaint; effects do not run during server rendering. Its work blocks paint, and development Strict Mode adds a setup/cleanup cycle. [S06]

## Proposed grid protocol — original synthesis, NOT implemented
Use one transaction owner for a small grid, with stable item identities and a monotonically increasing request identifier:

1. Accept a sort/filter intent. Capture the currently rendered eligible items before changing the application order.
2. Commit the new canonical data order through the framework. Keep semantics correct even when animation is disabled.
3. At the matching post-commit layout phase, consume that capture once and start Flip. Reject an obsolete capture if a newer request superseded it. Do not assume a state setter has synchronously committed the DOM.
4. For reduced motion, skip spatial interpolation but retain the same final data/DOM order. Focus restoration and any announcement are application responsibilities; visual movement is not their completion signal.
5. On another sort, capture afresh under the documented interruption policy. Never reuse the first snapshot indefinitely as an undo stack.
6. Undo is a new data transaction restoring the prior logical order, with an optional fresh transition. It is not merely `timeline.reverse()`.
7. Teardown invalidates pending captures and releases owned animation resources. Do not mutate React-owned children with ad hoc DOM reparenting alongside reconciliation.

Keep the outer grid in flow when experimenting with absolute children. Do not enable `simple:true` without validating its documented no-transformed-container assumption [S02]. Reserve batching for genuinely coupled components, not a single uncomplicated grid.

## Fit, licence and operating requirements
GSAP Flip is a JavaScript layout-transition plugin, useful for reparenting and changing layouts; its official overview includes interactive explanatory demos, not benchmark evidence [S01]. GSAP's repository package reports **3.15.0**, ESM/CommonJS entry points and TypeScript declarations, with no dependencies field. Observed master commit **13e2b790546426a1a2e0e9b409f3f8dc6d6611f2**, dated **2026-04-13T13:08:30Z**. The latest-release endpoint returned 404: release publication date is unknown, not inferred from commit date. This snapshot shows maintained source history, not a future support guarantee. [S07, S08]

Installation documentation supports npm or script loading; import/register the required plugin rather than assuming all plugins are loaded. [S09] Adoption requires browser DOM/layout and GSAP core; no hosted backend, API key or MCP server is required for this proposed local UI. Costs are bundle delivery, layout measurement and engineering/testing; no latency/FPS or hosting-cost figures were measured.

The linked **GSAP Standard No Charge licence**, not MIT/unrestricted reuse, permits commercial usage but restricts specified competing no-code visual animation builders and removing proprietary notices. Effective 2025-04-30, modified 2025-05-30. A public repository does not erase those restrictions; check product use against the actual terms. [S10]

Alternatives (not evaluated as new tool recommendations): immediate static reorder is the baseline; the earlier route/layout package discusses Motion layout for framework-owned projection. Keep Flip when explicit multi-element geometry control justifies its coordination burden. Future application: a case-study grid that changes between editorial and chronological order while preserving a meaningful static document.

## Acceptance exercise — all NOT RUN
- Sort twice during movement: final data, DOM order and appearance agree with the latest request.
- Undo after completion: logical order actually changes back, independently of playback direction.
- Toggle reduced motion: the same ordered content remains usable without travel.
- Nested card + parent and absolute-child variants: no doubled displacement or collapsed container.
- Keyboard focus, hidden/removed cards, resize/font changes, route teardown and Strict Mode: inspect semantics, stale captures and leftover styles.
- Profile layout/paint and input responsiveness on target devices before selecting width/height versus scale.

Documentary review only. No installation, runtime, integration, performance, accessibility or browser compatibility tests were performed. Sources are live unless commit-pinned; docs may drift independently of the repository version. See sources.json for provenance and qa.md for review.

