# Animated dialog exits: separate visual presence from interaction ownership

Run: `20261101T121200Z-motion-interaction`  
Scheduled: 2026-11-01 07:12 America/New_York (12:12 UTC); evaluated 2026-11-01.  
Scope: documentary research and original design recommendation, not implemented or accessibility-certified.

## One question

How should a React portfolio close an animated project-preview dialog without leaving invisible controls focusable or releasing modal isolation at the wrong time?

This is a narrow addition to the existing motion architecture and route-transition packages, not a replacement. Those cover cleanup and route focus; this note specifies **dialog interaction lifetime versus exit-animation lifetime**. Both existing motion packages and the live catalog were inspected before selection.

## Source findings

- **Motion:** `AnimatePresence` retains exiting children in the DOM until their exit animations complete. Direct children need unique keys. `useIsPresent` exposes presence state, and `onExitComplete` signals that exiting nodes have finished. Therefore removal from the React render condition is not proof that the retained DOM is gone. [S1](https://motion.dev/docs/react-animate-presence)
- **WAI dialog pattern:** a modal contains the keyboard tab sequence, places focus inside on opening, and supports Escape to close. Closing normally returns focus to the invoker; when that element no longer exists, a logical workflow target is needed. A visible close control is recommended. [S2](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/)
- **Native dialog:** `showModal()` and the `open` attribute are not equivalent: the latter alone opens a non-modal dialog. Native modal dialogs make outside content inert. MDN documents exit transitions involving discrete `display` and `overlay` behavior; those CSS capabilities require their own compatibility checks. [S3](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog)
- **Inertness:** `inert` prevents focus/click interaction and removes a subtree from the accessibility tree; opacity does not provide those semantics. A modal dialog opened with `showModal()` escapes ancestor inertness, but can itself be explicitly inert. [S4](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Global_attributes/inert)

## Proposed interaction contract — original synthesis, untested

Prefer an already accessible dialog primitive or native modal dialog over constructing a focus trap around a decorative Motion container. Do not assume an animation library supplies modal behavior.

For the initial portfolio implementation, choose **semantic close first, decorative exit second**, or omit the exit entirely. A native close and an animation completion are different events: call the dialog's actual close mechanism, restore a valid focus target, and leave no actionable outgoing controls. If a visual ghost is retained, it must be a separate inert, non-semantic visual layer with no active form controls, duplicated IDs, or confidential content. It must not intercept the newly restored interaction. Prefer a simple noninteractive shell over cloning a complex DOM subtree. This is a design recommendation requiring browser and assistive-technology tests, not a claim that native dialog plus Motion automatically implements it.

An alternative is to keep the dialog semantically open throughout a very short exit, then close it. That requires retaining modal isolation and usable focus while visible, handling Escape/repeated close requests idempotently, and a completion path even if animation is canceled. Do not mark the focused dialog subtree inert while background content remains inert: that can leave no useful focus target. This alternative is more stateful and is not the recommended first implementation.

Use one owner for finalization, not independent router, animation, and dialog callbacks each restoring focus. Model `open → closing → closed` explicitly; a new open request invalidates old completion callbacks with an operation token. Reopening must not allow a stale `onExitComplete` to close the new dialog. Treat animation callbacks as visual cleanup signals, never authorization to submit a form or navigate. On reduced motion or animation failure, finalize without waiting for decorative motion.

### Decision table

| Situation | Recommended behavior to implement and test |
|---|---|
| No established dialog primitive | Start with native `showModal()` and no exit animation |
| Exit with retained Motion DOM | Explicitly separate the interactive dialog from decorative retained content |
| Reopen before exit completes | Invalidate the obsolete close operation; preserve the current dialog |
| Invoker removed by route/filter update | Choose a connected, logical focus destination |
| Nested modal | Delegate focus/isolation ownership to one tested dialog manager; do not globally clear inertness |
| Reduced motion or interrupted animation | Complete the semantic state change without visual dependency |

## Future acceptance matrix — NOT RUN

1. Open by keyboard; verify initial focus, label, Tab and Shift+Tab containment.
2. Close via button and Escape; verify exactly one finalization and valid focus restoration.
3. During exit, repeatedly Tab, click, and submit: outgoing controls must not remain actionable after semantic close.
4. Close then immediately reopen; obsolete completion must not close the new instance.
5. Remove the invoker or navigate during close; no focus restoration to detached DOM.
6. Test reduced motion, canceled animations, background tab, and no animation support.
7. Test nested dialogs and portals; unrelated modal isolation must remain intact.
8. Inspect accessibility tree and test with target screen readers; perform browser-specific keyboard checks.

## Limits and adoption

No app lockfile or installed package version was inspected. Source pages are live documentation, not pinned library releases; release/commit dates are unknown here. No library is installed, licensed for reuse, or recommended for commercial redistribution by this note. No hosting/API/MCP integration is needed for the proposed client-side design; runtime costs and animation performance are unmeasured. This is not a repository/tool shortlist, so repository license and maintenance evaluation are not applicable. Reuse ideas in original code only after verifying the chosen dependency's actual license and lockfile compatibility.

Four official documentation sources were reviewed. Browser execution, accessibility testing, security testing, and performance profiling were not performed. Review status is documented separately in `qa.md`.

## Prior work

- [Motion architecture](../20261101T061350-motion-orchestration-architecture-01/research.md)
- [Route-transition navigation](../20261101T070845-view-transition-navigation-01/research.md)
