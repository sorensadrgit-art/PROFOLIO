# A portfolio command menu: cmdk or kbar?

Run: `20261101T171800Z-open-source-creative`  
Scheduled: 2026-11-01 12:18 America/New_York (17:18 UTC). Evaluated: 2026-11-01.

## Decision — original synthesis, not an integration result

Evaluate **cmdk 1.1.1** for a small, custom-styled project navigator. Evaluate **kbar 1.0.0** when nested actions, registered shortcuts and an action registry justify more structure. Keep visible ordinary navigation as the baseline: a hidden keyboard shortcut is not a replacement for discoverable links or touch access.

The live catalog contained 24 packages and only two creative shortlists: carousels and image lightboxes. This adds command-menu selection and action ownership, not another gallery or dialog animation report.

## Verified source findings

### cmdk

The repository now resolves to `dip/cmdk`; the older `pacocoursey/cmdk` URL redirects. The README describes composable, unstyled React command parts, custom filtering and optional Radix Dialog composition. It does not register the global shortcut automatically. Provide unique item values and keys. Built-in virtualization is absent; externally filtered results can disable internal filtering with `shouldFilter={false}`. The README says to render a closed dialog on the server and identifies this as a client component. These are maintainer statements, not our runtime verification. [C1]

The tagged manifest declares React/React DOM 18 or 19 peers and Radix compose-refs, dialog, id and primitive dependencies. Peer declarations do not certify a particular Next.js application. [C2]

GitHub reports latest stable v1.1.1 published 2025-03-14, including an IME keybinding fix. The latest observed default-branch commit is `dd2250ed608443e8f32bafc5fa2d1d07a3746aa3`, dated 2025-10-29. Release age merits compatibility investigation, not a claim of abandonment. [C4,C5]

### kbar

The README organizes commands as action objects beneath `KBarProvider`, with identifiers, labels, keywords and execution callbacks. It documents nested actions, shortcuts, search components, animation and virtualized result rendering. Accessibility and performance descriptions are maintainer claims; no screen-reader or large-list benchmark was reproduced here. [K1]

The v1.0.0 release notes describe React 18/19 support, Fuse7, TanStack virtualization replacing react-virtual, and multi-word/diacritic matching improvements. Its tagged manifest declares React/React DOM 17–19 peers and runtime dependencies including Radix Portal, TanStack React Virtual, Fuse, fast-equals and tiny-invariant. Do not copy an old beta tutorial without comparing these changes. [K2,K4]

The latest stable release is v1.0.0, published 2026-08-10. The latest observed default-branch commit is `26ec0f49f92ab34fa6ab59392782d56020f28098`, dated 2026-08-10. This documents observed activity, not a maintenance guarantee. [K4,K5]

### Reuse, integration and cost

Both tagged license files contain MIT terms; retain copyright and permission notices with redistributed copies or substantial portions. The code license does not establish rights to unrelated demo branding, imagery or fonts. No full repository or media was copied. [C3,K3]

These are React frontend libraries with TypeScript declarations, not hosted search services. Future evaluation would add the pinned package to a compatible React application and supply original styles and navigation callbacks. No MCP/API server is required for this client-side use. No paid service is required by the reviewed setup; actual hosting, bandwidth and engineering costs are unmeasured. Neither package was installed. Repository examples are available through the source links; demos were not operated.

## Original implementation proposal — untested

1. Start with a visible “Find a project” button and conventional linked navigation. Offer the shortcut as an enhancement, with context-aware handling around editable fields and input composition.
2. Maintain a small curated registry: stable ID, public label, search aliases, destination and category. Keep navigation commands separate from state-changing actions; never turn arbitrary search text into executable code or a trusted URL.
3. For cmdk, let the application own shortcut binding, state and ranking. For kbar, deliberately scope the provider/action registration so obsolete route actions cannot linger. Test this ownership rather than assuming cleanup.
4. Use original editorial typography and restrained category labels. Keep focus/selection visually distinct and do not animate the same properties from two owners. Respect reduced motion in the application styling.
5. Use one ranking pipeline. If the application prefilters results, verify that a component's internal filtering does not remove expected matches again. Preserve stable IDs across translation or title changes.
6. Close the menu only as part of a deliberate navigation outcome; define focus placement after navigation and after dismissal. Keep a usable error state when a destination fails.

## Troubleshooting and proposed acceptance

| Symptom | Investigation |
|---|---|
| Shortcut fires while composing text | Compare pinned version, application key listener and IME handling; the release fix does not test your custom handler. |
| Commands disappear unexpectedly | Inspect duplicate IDs, implicit text-derived values and double filtering. |
| Hydration warning | Compare server/client initial open state and client boundary; cmdk documents closed server state. |
| Old route action still appears | Inspect registration and teardown ownership across route remounts. |
| Search feels slow | Measure real list size and ranking/render cost; do not repeat a maintainer benchmark as your own. |

A later authorized prototype should test keyboard-only and touch entry, Escape/focus return, IME, editable-field shortcuts, screen-reader naming and active-result announcements, reduced motion, empty/long queries, aliases/diacritics, repeated navigation, rejected destinations and server hydration. Compare ordinary links, cmdk and kbar with identical synthetic project data. None of these tests ran.

## Limitations and sources

Documentary only: no installation, runtime, visual, accessibility, performance, dependency-security or integration tests. Live README guidance may differ from a release; tagged manifests/licenses constrain version claims. Independent review and local structural/privacy checks are in `qa.md`.

- [C1] [cmdk README](https://github.com/dip/cmdk/blob/dd2250ed608443e8f32bafc5fa2d1d07a3746aa3/README.md)
- [C2] [cmdk tagged manifest](https://github.com/dip/cmdk/blob/v1.1.1/cmdk/package.json)
- [C3] [cmdk tagged license](https://github.com/dip/cmdk/blob/v1.1.1/LICENSE.md)
- [C4] [cmdk latest release](https://api.github.com/repos/dip/cmdk/releases/latest)
- [C5] [cmdk observed commit](https://github.com/dip/cmdk/commit/dd2250ed608443e8f32bafc5fa2d1d07a3746aa3)
- [K1] [kbar README](https://github.com/timc1/kbar/blob/26ec0f49f92ab34fa6ab59392782d56020f28098/README.md)
- [K2] [kbar tagged manifest](https://github.com/timc1/kbar/blob/v1.0.0/package.json)
- [K3] [kbar tagged license](https://github.com/timc1/kbar/blob/v1.0.0/LICENSE)
- [K4] [kbar latest release](https://api.github.com/repos/timc1/kbar/releases/latest)
- [K5] [kbar observed commit](https://github.com/timc1/kbar/commit/26ec0f49f92ab34fa6ab59392782d56020f28098)
