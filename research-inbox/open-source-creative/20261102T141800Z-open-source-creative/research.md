# Atropos: stopping tilt is not the same as retiring the effect

## Bounded question and decision
**Can a decorative parallax card switch safely to a static mode when preferences or routes change?** Shortlist one project, **nolimits4web/atropos**, for a small optional layered illustration. Prefer a static original image unless pointer-reactive depth improves the story. This is documentary selection, not adoption approval.

The live catalog and nine prior creative reports were read first. They cover rails, lightboxes, command menus, text splitting, flowmaps, annotations, freehand outlines, shape morphs and a globe. No same-run or extra unindexed topic directory was observed. This package adds a concrete React parameter-update and teardown boundary. The existing COBE package was present in the global catalog and repository but absent from the topic's nested index; that pre-existing discrepancy is preserved, not silently repaired.

## Findings from primary sources
- Atropos produces layered parallax with DOM elements and CSS 3D transforms, not a WebGL scene. The core uses scale/rotation wrappers, optional highlight/shadow, pointer handlers and data attributes. Its defaults include rotate and rotateTouch enabled. [S1,S3]
- The core starts a requestAnimationFrame queue-draining loop during construction and schedules another callback on every drain, including when the queue is empty. isActive gates movement, not that scheduling loop. destroy cancels the recorded frame, removes listeners and deletes the element's instance marker. It does not remove generated shadow/highlight elements, clear inline transforms or remove effect classes in the inspected function. These are static-source facts, not measured CPU usage or an observed leak. [S1]
- The React wrapper initializes in an empty-dependency mount effect and destroys on cleanup. A second effect updates only onEnter, onLeave and onRotate callbacks. Changes to rotate, rotateTouch or other non-callback props are not copied into the existing core params by that wrapper. JSX may rerender some decoration nodes, but that does not make all core options reactive. [S2]
- Styles set touch-action:none for the rotation-touch class, with pan-y/pan-x rules for directional modes. The core accepts scroll-y and scroll-x modes and classifies initial touch movement. The official guide documents these as ways to preserve scrolling on the corresponding axis. This does not certify real-device scrolling, cancellation or tap activation. [S1,S3,S9]
- The distribution manifest says version 2.0.2, ESM, TypeScript declarations, and core, React and custom-element exports. It declares no runtime dependency or peerDependency fields. That omission does not mean the React entry works without React: its source imports React hooks. Exact React/Next.js compatibility remains untested. [S2,S4]
- GitHub latest release is v2.0.2, published 2023-07-04T06:52:07Z. The observed default-branch head eb4b4c5682117a53f60fccd459f64d06a99e9445 is dated 2026-10-06T17:16:44Z and adds sponsors. Neither its recency nor release age proves active runtime support or abandonment. npm metadata retrieval failed; registry latest, integrity and artifact/source equivalence remain unknown. [S7,S8]
- The actual tagged LICENSE is MIT, copyright 2021 Vladimir Kharlampidi. Retain copyright and permission notices with copies or substantial portions; warranty is disclaimed. This does not clear demo artwork, logos, fonts or other third-party assets. No assets or substantial source text are copied here. [S5]

## Original synthesis: use a mode boundary, not one changed prop
A React rerender with rotate=false is not sufficient evidence that the inspected wrapper changed the live core configuration. Even changing the core flag would only address movement, not the perpetual queue scheduling or remaining decoration. Therefore define static mode as a different, clean presentation owned by the application, rather than equating it with a single tilt option.

Destroying an imperative instance while retaining its DOM is also different from removing the whole React subtree. A retained node can keep styles/classes and decorative children after listener cleanup. Test both transitions separately. These are deductions from S1/S2, not reproduced browser failures.

## Untested integration and original application
Proposed application: one layered cover illustration for an architecture case study, while title, summary and project link stay ordinary content. The layer artwork should be original or separately licensed.
1. Build a readable static card first. Keep navigation and meaningful text available without hover, touch motion or JavaScript.
2. In a separately authorized prototype, verify the registry artifact, pin atropos 2.0.2 if appropriate, import its CSS and choose either the documented DOM structure or React export. Do not confuse repository build tooling with an installed production artifact. README distinguishes production package output from development build output. [S4,S6,S9]
3. Determine enhancement eligibility before mounting. For reduced motion or a deliberately static view, render the clean baseline instead of initializing the effect. On live preference changes, deliberately unmount the effect-owned subtree and render that baseline. Preserve focus on a stable external link/control; do not remount a focused interactive child blindly.
4. Give the library exclusive ownership of its wrapper transforms and layer offsets. Apply unrelated transitions to a separate ancestor or sibling, not the same transform property.
5. If using the core imperatively on retained DOM, define application-owned restoration of the original classes/styles and generated nodes after destroy, without removing author-owned content. Do not assume destroy restores a pristine card.
6. Choose a touch policy explicitly and compare directional modes with no effect on actual devices. A decorative card must not become a scroll trap.
7. Limit simultaneous instances until measured. Idle callback scheduling is a reason to profile, not a quantified performance verdict.

## Alternatives, operations and costs
For fixed artwork, use a static image or original SVG. A restrained CSS hover transform may suffice for a single-layer decorative effect; it still needs motion preferences and keyboard-safe semantics. A full Three.js scene is a different complexity class, not a necessary replacement for this card.

This is a browser-side library, not a hosted API or MCP server. No account, credential or new paid service is required by the reviewed integration. Normal hosting/bandwidth, asset preparation, browser rendering, lifecycle engineering and dependency maintenance remain costs; no dollar, frame-time or memory measurement was made. JavaScript, React JSX, CSS/SCSS and TypeScript declarations are observed technologies. The official homepage and repository playground are demo entry points, not executed evidence.

## Proposed acceptance fixture, not executed
- Toggle reduced motion before mount and while a card is active; confirm a genuinely static readable baseline and retained focus.
- Change rotate/rotateTouch props on a mounted wrapper, then compare deliberate remount; inspect actual core parameters and touch behavior.
- Destroy while hovered with retained DOM; inspect frame callbacks, listeners, styles, classes and generated decorations separately.
- Repeat route mount/unmount, pointer entry/exit, touch cancellation, scroll-y gestures, keyboard navigation and slow image loading.
- Profile one versus a gallery of instances in foreground and hidden states; do not infer measured savings from source inspection.

No installation, runtime, integration, browser, visual, performance, accessibility or dependency-security tests were performed. Documentary approval is not deployment permission. Exact framework compatibility, npm artifact equivalence and non-code asset rights remain adoption gates.

## Provenance
Scheduled 2026-11-02T14:18:00Z; retrieved/evaluated 2026-11-02 around 15:50 UTC. S1–S6 are tagged primary files, S7/S8 provider metadata and S9 official documentation. Sources, original deductions and untested recommendations are separated. See sources.json, repos.json and qa.md.

- [S1] [Atropos core source](https://github.com/nolimits4web/atropos/blob/v2.0.2/src/atropos.js)
- [S2] [Atropos React wrapper](https://github.com/nolimits4web/atropos/blob/v2.0.2/src/react/atropos-react.js)
- [S3] [Atropos styles](https://github.com/nolimits4web/atropos/blob/v2.0.2/src/atropos.scss)
- [S4] [Distribution package manifest](https://github.com/nolimits4web/atropos/blob/v2.0.2/package/package.json)
- [S5] [Actual MIT licence](https://github.com/nolimits4web/atropos/blob/v2.0.2/LICENSE)
- [S6] [Repository README](https://github.com/nolimits4web/atropos/blob/v2.0.2/README.md)
- [S7] [Latest release metadata](https://api.github.com/repos/nolimits4web/atropos/releases/latest)
- [S8] [Latest default-branch commit](https://api.github.com/repos/nolimits4web/atropos/commits?per_page=1)
- [S9] [Official Atropos documentation](https://atroposjs.com/docs)
