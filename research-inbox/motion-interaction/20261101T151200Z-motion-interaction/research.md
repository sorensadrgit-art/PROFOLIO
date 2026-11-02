# Drag is a preview, not a command: cancel-safe interaction with a click alternative

Run: `20261101T151200Z-motion-interaction` · Scheduled: 2026-11-01 10:12 EST · Evaluation: 2026-11-01.

## Question and gap

How can a horizontal drag interaction preserve ordinary page scrolling and avoid committing an action after cancellation, while remaining usable without dragging?

The live catalog and four previous motion packages were inspected. They cover engine ownership, route transitions, dialog exits and scroll smoothing, not the drag-to-command boundary. This reference is about a bounded interaction contract, not another carousel-library shortlist.

## Verified source findings

- **Motion mechanics:** `drag="x"` constrains the animated axis. Drag release has inertia by default; `dragMomentum={false}` disables it. Constraints and elasticity govern visual movement, not application authorization or business-state commits. The documented callbacks include `onDragStart`, `onDrag` and `onDragEnd`. [Motion drag guide](https://motion.dev/docs/react-drag)
- **Gesture recognition:** Motion documents a three-pixel pan threshold and cancellation of a child tap after movement within a draggable parent. Its keyboard tap handling does not supply a complete drag alternative. Touch pan handling requires an appropriate `touch-action` policy. [Motion gestures](https://motion.dev/docs/react-gestures)
- **Browser ownership:** Pointer Events defines `pointercancel` when the browser suppresses a pointer stream. `touch-action` participates in deciding native panning/zooming, including ancestor restrictions; changing it during the active gesture does not retroactively change ownership. The specification gives `pan-y` as a horizontal-carousel/vertical-page arrangement. Pointer capture redirects event targeting; it is not permission to defeat native gesture handling. [Pointer Events, sections 4.2.7, 8 and 9](https://www.w3.org/TR/pointerevents3/)
- **Alternative operation:** WCAG 2.2 SC 2.5.7 is Level AA and requires a single-pointer, non-drag alternative except specified essential/user-agent cases. Keyboard equivalence alone is insufficient. The Understanding document distinguishes author-implemented dragging from native browser scrolling. [W3C explanation](https://www.w3.org/WAI/WCAG22/Understanding/dragging-movements.html)

## Proposed contract — original synthesis, not executed

Use separate **preview** and **committed** state. Pointer movement may update a temporary offset; only an explicit accepted completion changes the selected item or ordering. Cancellation returns to the committed state. Animation completion must not itself submit a mutation.

For a project rail, keep visible native Previous/Next buttons. Both buttons and a completed drag call the same bounded `selectItem` command. Do not replace the buttons with instructions to swipe. Preserve focus on the activated control unless a documented interaction requires otherwise.

Choose one gesture owner. If Motion owns dragging, use its supported lifecycle and verify cancellation behavior in the pinned version. Do not add a competing raw-pointer recognizer on the same surface. The sources do not establish that every browser cancellation maps to a particular Motion callback; that mapping remains a required implementation test.

For a custom pointer implementation, an illustrative state machine is:

```text
IDLE -> PREVIEW(pointerId, generation, initialSelection)
move for matching pointer -> update temporary offset
accepted pointer-up -> select bounded target once -> IDLE
cancel / unexpected capture loss / unmount -> discard preview -> IDLE
late event or obsolete generation -> ignore
```

Clear the active operation before releasing capture so the resulting capture-loss event cannot finalize it twice. Handle pointer cancellation even when the pointer never leaves the element. An unexpected capture loss aborts an active preview; normal loss after completed release does not undo the accepted selection. This is a proposed algorithm, not a tested adapter.

## Debugging and tradeoffs

| Symptom | Investigation / proposed correction |
|---|---|
| Page cannot scroll over the rail | Inspect gesture policy and ancestors; avoid a blanket page-wide `touch-action: none`. Verify zoom separately rather than assuming `pan-y` preserves it. |
| Release drifts past intended target | Decide whether inertia is appropriate; test disabled momentum for discrete selection. |
| A canceled drag still selects | Audit every finalization path; distinguish accepted completion from cancellation. |
| Drag triggers a nested link | Test actual click propagation and pointer movement; do not assume a library's tap threshold proves all native click behavior. |
| Route change causes a late update | Invalidate the operation on teardown; reject stale pointer/generation callbacks. |

## Acceptance matrix — NOT RUN

Test mouse, touch and pen; vertical scrolling over the rail; horizontal gesture; diagonal intent; release outside; browser cancellation; lost capture; unmount/remount; rapid repeated gestures; nested links; button-only and keyboard operation; zoom; reduced-motion alternatives. Confirm exactly one command per accepted action and none after cancellation. Test real accessibility semantics and target devices before adoption.

## Compatibility and limitations

Four primary documentation sources, no repository shortlist or copied implementation. Motion pages are live and unversioned; installed dependency versions and source release dates are unknown. Pointer Events page identifies Level 3 Recommendation dated 2026-06-30; browser behavior still needs testing. No dependencies, servers, MCP endpoints or paid services were added. Licensing of any eventual dependency must be checked against its actual package; documentation access is not a software reuse licence. Performance, accessibility conformance and runtime integration are **not tested**. This reference's state machine and tradeoffs are original proposals, not vendor guarantees.
