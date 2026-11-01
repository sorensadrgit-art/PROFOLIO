# Route transitions as a motion system: React ViewTransition, browser snapshots, Motion layout, and scroll/focus continuity

**Run ID:** 20261101T070845-view-transition-navigation-01  
**Topic:** Motion and Interaction  
**Research date:** 2026-11-01  
**Research status:** Documentary research; no application code changed

## Executive summary

The previous Motion & Interaction package already covered engine selection, one-owner-per-property, ScrollTrigger geometry, React cleanup, reduced motion, typography splitting, and a scroll-to-R3F progress bridge. This run deliberately avoids repeating those topics.

The new question is: **How should PROFOLIO coordinate route-level cinematic transitions with React rendering, browser snapshot animation, scroll restoration, focus management, and rapid navigation?**

The strongest conclusion is to treat route transitions as a separate navigation layer, not as another general-purpose animation engine:

1. Use React's built-in ViewTransition component for React-owned route or tree transitions when the installed React/React DOM version and router integration support it.
2. Keep the transition boundary persistent around the route outlet. Do not key/remount the whole route tree just to trigger animation.
3. Keep navigation chrome outside the route-content boundary when it should remain stable.
4. Use Motion layout animation for interactive, interruptible component-level layout changes; use browser View Transitions for snapshot-based route continuity and broad crossfades. Do not run both as competing orchestrators for the same navigation.
5. Treat scroll restoration and focus routing as explicit navigation responsibilities. A visual snapshot does not move keyboard focus or decide the reader's next position.
6. Keep transitions progressive: unsupported browsers and reduced-motion users must receive the same content and navigation without a dependency on the animation.

This is an architecture recommendation synthesized from current official documentation, not a benchmark against PROFOLIO source code.

## 1. What changed in the platform and framework guidance

### React ViewTransition is now a first-class React DOM API

The current React reference identifies React 19.3. React's ViewTransition component participates in React Transitions and Suspense, and exposes enter, exit, update, and shared-element behavior. It is DOM-only at the documented state. [S01]

Important activation detail: an ordinary immediate setState does not activate a ViewTransition by itself. The update must be part of a React Transition, Suspense reveal, or useDeferredValue flow. React's reference also says React itself calls document.startViewTransition; application code should not independently start another browser transition around the same React update. [S01]

React coordinates an internal sequence: DOM mutation, font readiness, layout effects and refs, pending navigation completion, layout measurement, then animation callbacks. A flushSync in the middle can cause React to skip the transition. React also batches later updates while a transition is active, so rapid state changes can coalesce rather than produce every intermediate visual state. [S01]

### React Router's current guidance has moved on from its older transition props

The current React Router documentation is version 8.5.1. It recommends React's ViewTransition component for client-side navigation and marks its older viewTransition props/options, useViewTransitionState, and NavLink transition state APIs as deprecated in favor of the React component. It states the new component requires React and React DOM 19.3 or later. [S02]

For route navigation, React Router recommends a persistent ViewTransition boundary around the shared layout's Outlet. The boundary should remain mounted across route changes; it should not be keyed by location or force a remount of the route tree. React Router already wraps router state updates in startTransition by default. Disabling router transitions or using flushSync can prevent the animation. [S02]

A particularly important caveat: React skips ViewTransitions for synchronous popstate updates. Therefore browser Back/Forward navigation may not animate through this React integration. This is a documented behavior, not a defect to hide with a second competing transition. [S02]

### Browser support is usable, but feature-level differences remain

MDN marks the ViewTransition interface Baseline 2025, newly available across current major browser versions since October 2025, while warning that individual parts can have different support. The safe implementation is feature detection and a no-animation navigation path, not a browser-name assumption. [S03, S04]

The same-document View Transition model captures the old state, applies the DOM update, then captures the new state and animates browser-generated pseudo-elements. This is fundamentally a snapshot transition, not a live DOM FLIP of every child. [S08, S09]

## 2. Choose the correct transition engine

| Use case | Recommended mechanism | Reason | Main guardrail |
|---|---|---|---|
| Whole route content crossfade | React ViewTransition around the persistent route outlet | React coordinates route state with the browser transition | Requires React/React DOM 19.3+ per current Router docs; test Back/Forward |
| Thumbnail-to-detail hero continuity | Named React ViewTransition shared element | Same named boundary can connect outgoing and incoming trees | Names must be globally unique; both sides must participate in the same React Transition |
| Reordering cards, accordion, filters, responsive layout | Motion layout/layoutId | Live elements animate using transform-based layout projection | Keep animation interruptible and watch scale distortion |
| Fast hover, focus, press and local feedback | CSS or Motion | Component-local state, low ceremony | Preserve focus-visible and reduced-motion behavior |
| Elaborate pinned case-study chapter | GSAP + ScrollTrigger | Timeline sequencing and scroll geometry | Do not nest a page snapshot transition around an actively scrubbed scene without testing |
| Route snapshot with imperative wrapper and queuing | Motion animateView | Wraps native View Transitions and offers queued interruption behavior | Do not double-wrap a React ViewTransition navigation |

Motion's documentation explains that layout animation operates on live elements using transform-based projection, while View Transitions use old/new snapshots. The native API can be useful for page wipes and cross-route continuity, but its snapshot model is less suited to dense, independently moving child layouts. [S05]

Motion's animateView is a higher-level wrapper around the browser API. Its documentation describes queued interruption handling, but also documents a scroll-offset caveat: a scroll change during the transition is represented in the captured current view and can make named elements animate by that scroll delta. [S06]

**Synthesis:** PROFOLIO should not standardize on one transition technology for every interaction. Use React ViewTransition for route-level continuity, Motion for component-level live layout, and GSAP for deliberate scroll choreography. Keep the boundary between these systems explicit.

## 3. Persistent route boundary and shared-element strategy

Recommended React Router shape, assuming a compatible React stack:

- Keep global navigation, logo and persistent controls outside the route-content transition boundary if they should not animate with every route.
- Wrap the shared route Outlet in one persistent React ViewTransition boundary.
- Do not add a location key to the boundary just to trigger a transition.
- Use a named ViewTransition only for a deliberate shared element, such as a project thumbnail becoming the project hero.
- Namespace names by feature and object identity. Avoid one generic name reused by multiple simultaneously mounted cards.
- Do not combine React's built-in transition with React Router's deprecated viewTransition prop on the same navigation; the Router migration guide warns that enabling both can interrupt the transition. [S01, S02]

For PROFOLIO, the highest-value shared elements are likely:
1. A selected project thumbnail becoming its case-study hero.
2. A project title moving from a card/list context to the case-study heading.
3. A small brand mark or navigation indicator only if its continuity adds meaning.

Do not assign unique snapshots to every decorative element. A small number of meaningful pairs gives a clearer hierarchy and avoids making the transition visually noisy. This is a design recommendation, not a browser limit.

## 4. Scroll position and navigation are separate state

React Router's ScrollRestoration component emulates browser scroll restoration on location changes. Its default storage key is based on location.key; the getKey option can instead use pathname or a custom key. It renders an inline script to prevent scroll flashing. [S07]

React's ViewTransition implementation waits for pending Navigation work so scroll restoration can occur within the transition. However, the documented legacy popstate constraint creates an important edge case: browser Back/Forward must complete synchronously for scroll/form restoration, which conflicts with the ViewTransition scheduling model, so React skips those animations. React's docs suggest router integrations using the Navigation API can address this class of limitation. [S01, S02]

Recommended navigation policy:

- New forward route: start the route transition; after route update, apply the route's intended top/anchor position.
- Back/Forward: prioritize correct browser history and scroll restoration over animation; accept a static route change where the framework skips the transition.
- In-page anchor: do not accidentally animate the whole page snapshot when the user's intent is simply to move to a section.
- Same-route filter/tab change: use Motion layout or a small local transition, not a full route snapshot.
- Interrupted or rapid navigation: prioritize the latest valid route and avoid queuing stale destinations in custom code.

Do not let GSAP ScrollTrigger's own scroll-to or pin refresh logic fight the router's scroll restoration. Route transition, scroll restoration, and scroll-story choreography should have separate ownership.

## 5. Accessibility: visual continuity does not move keyboard focus

The View Transition API animates visual snapshots. It does not automatically choose a new programmatic focus target. Chrome's current Modern Web Guidance explicitly calls for moving focus to the newly revealed heading or view container when the old focused element disappears. It also recommends removing temporary view-transition-name values after the transition and respecting reduced motion. [S08]

Recommended behavior:

1. On route change, ensure the new route has a meaningful heading or main landmark.
2. After the DOM update (or after the transition's finished promise when appropriate), move focus to the new route heading/container with tabindex="-1" if the router does not already manage this.
3. Do not leave focus on a link or button that was removed with the outgoing route.
4. Verify screen-reader announcements and browser history behavior independently of the visual animation.
5. Under prefers-reduced-motion: reduce, disable or sharply simplify the old/new snapshot animations; do not suppress route content or navigation.
6. If View Transitions are unavailable, perform the same route update and focus behavior without the animation.

Avoid blindly moving focus on every state change. A filter update or an in-page tab change may have a different expected focus policy from a full route navigation. This distinction is a synthesis of the browser guidance and standard interaction design practice.

## 6. Motion-specific limitations and failure modes

### Snapshot transition limitations

- Only one browser View Transition can be active at a time in the documented native model; a newly started transition can cause the previous one to skip to completion. [S09]
- The transition overlay can temporarily block interaction with the underlying page. Motion documents this as a practical drawback compared with its live layout animations. [S05]
- Scroll changes during a snapshot transition can visually shift named elements; Motion documents this caveat for animateView. [S06]
- Duplicate view-transition names can cause the transition to be skipped. [S08, S09]
- A route can be functionally correct even if the visual transition fails; treat animation as enhancement, never as the navigation transaction itself. [S08, S09]

### React integration limitations

- Immediate state updates do not automatically activate ViewTransition; use a Transition/Suspense path where appropriate. [S01]
- flushSync can cause React to skip the animation. [S01, S02]
- React waits for fonts and some layout dependencies before measuring, so font loading and route data can affect the start time. [S01]
- Shared-element names must be unique across simultaneously mounted boundaries. [S01]
- React ViewTransition currently targets the DOM, not React Native. [S01]

## 7. PROFOLIO implementation decision record (recommendation only)

| Decision | Recommendation |
|---|---|
| Route-level animation | React ViewTransition if React/React DOM >=19.3 and router support is confirmed |
| Router structure | Persistent boundary around Outlet in shared layout |
| Navigation controls | Keep outside route boundary when they should remain stable |
| Shared project hero | Use a named shared element for only the selected card and destination hero |
| Component layout | Motion layout/layoutId |
| Cinematic scroll chapter | GSAP/ScrollTrigger, separate from route snapshot lifecycle |
| Scroll restoration | Router-owned ScrollRestoration; test forward, back, forward again |
| Focus management | Explicit route focus policy independent of animation |
| Reduced motion | CSS media query for all view-transition pseudo-elements and other motion layers |
| Unsupported browser | Normal navigation with no animation |
| Rapid navigation | Prefer router/React coordination; avoid a second imperative startViewTransition call |

Before adopting this, inspect the actual PROFOLIO package lockfile and router mode. The repository's research branch contains research artifacts only; this run does not establish which React, React DOM, or router versions the portfolio application uses.

## 8. Validation matrix for a future implementation

| Test | Expected outcome |
|---|---|
| Forward navigation | New route appears; transition is decorative |
| Browser without View Transitions | Route still navigates normally |
| Browser Back/Forward | Correct URL, route and scroll; animation may be skipped by React integration |
| Rapid double navigation | No stale route or duplicate transition wrapper |
| Project card to detail | Only selected image/title continuity animates |
| Duplicate shared name | Prevented by test/lint convention |
| Font loads after route request | No title jump; transition measurement waits as documented |
| Reduced motion | No spatial route animation; content remains immediate |
| Keyboard navigation | Focus moves to a valid new route target |
| Screen reader | New heading/context announced correctly |
| Anchor navigation | Correct target and scroll position, no unwanted full-page wipe |
| ScrollTrigger page | Pins and trigger geometry remain valid after route change/unmount |
| Suspense loading | Fallback and final content do not create confusing duplicate transitions |
| Unsupported React version | Use non-animated route behavior; do not import unavailable React API |

## 9. Evidence quality and limitations

- React, React Router, Motion, MDN and Chrome documentation are primary or official project sources.
- React's current API reference identifies version 19.3; React Router's current docs identify 8.5.1.
- MDN marks the ViewTransition interface Baseline 2025, while warning that support varies among individual features.
- Chrome Modern Web Guidance was last updated 2026-07-18 and documents explicit focus management and progressive enhancement.
- The older Chrome same-document guide was last updated 2024-09-25 and is used for mechanism explanation, not current browser support claims.
- No PROFOLIO app lockfile was inspected and no runtime/browser benchmark was performed.
- No application code, deployment configuration, or website implementation was changed.

## References

- [S01] React ViewTransition API
- [S02] React Router View Transitions (current docs: 8.5.1)
- [S03] MDN View Transition API
- [S04] MDN ViewTransition interface
- [S05] Motion for React layout animations
- [S06] Motion animateView
- [S07] React Router ScrollRestoration
- [S08] Chrome Modern Web Guidance: Same Document Transitions (updated 2026-07-18)
- [S09] Chrome for Developers: Same-document view transitions for SPAs (updated 2024-09-25)
- [S10] React startTransition API
