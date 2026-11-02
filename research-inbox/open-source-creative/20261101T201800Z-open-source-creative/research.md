# Split the heading, not its meaning: SplitType or Splitting.js?

Run: `20261101T201800Z-open-source-creative`  
Scheduled: 2026-11-01T20:18:00Z. Source evaluation began 2026-11-01T20:17:00Z, before the scheduled slot; the scheduled identifier is retained. Documentary comparison only.

## Decision — original synthesis, not an adoption approval

For a short, original heading with CSS-indexed word/character effects, **evaluate Splitting.js first**, subject to exact distributed-version checks. For measured line wrappers and an explicit restore API, **SplitType is the stronger functional candidate**, but defer reuse until its distributed licence terms are verified. Keep an unsplit heading plus a whole-element reveal as the lowest-complexity alternative.

The live catalog and all three existing creative packages were read first: carousel rails, lightboxes and command menus. Neither the current run nor an unfinished creative package was present in the topic directory. This adds typography DOM ownership and reuse diligence rather than duplicating navigation, general motion orchestration or headline wrapping advice.

## Verified source findings

### SplitType: measured lines need a lifecycle

The inspected README describes a JavaScript DOM splitter for lines, words and characters, usable with separate animation libraries. It exposes split-node arrays, `split()` and `revert()`; reverting restores original HTML and clears its internal target data. Relative words/characters can reflow naturally, while line splitting or absolute positioning requires re-splitting after container resize. The documentation recommends checking width changes rather than blindly re-splitting for every resize observation. [T1]

The README warns that nested text crossing multiple lines is incompatible with its line splitting. It also recommends disabling font kerning on targets and defining width for targets inside flex containers. These are trade-offs for typography and layout, not proof of pixel fidelity. Its Unicode support statement is a maintainer claim, not a test across writing systems. [T1]

The pinned source manifest reports version **0.3.4**, JavaScript module/UMD entry points and TypeScript declarations, with an empty runtime dependencies object. The observed latest GitHub release is **v0.3.3**, published **2022-10-29**; the observed default-branch commit is **2023-10-22**. Do not call the branch version the latest npm release. [T2,T3,T4]

**Licence limitation:** the manifest declares ISC, but the inspected recursive commit tree contains no licence-named file, and the attempted root LICENSE read returned 404. The actual distributed licence text was not verified. This is a reason to defer adoption and inspect the package/legal terms, not a claim that the project has no licence or is forbidden. Public source visibility is not unrestricted reuse. [T2,T5]

### Splitting.js: indices are not animation or line wrappers

The official guide documents span-based words/characters with CSS index/total variables; animation is supplied separately. Its chars plugin first runs words. The lines result groups word elements into arrays rather than promising a line-wrapper element. The guide recommends bundling the npm package for production; included CSS aids text effects, while cell effects have additional styles. Its API also supports indexing existing items and grid positions. [S1]

The pinned source manifest reports **1.1.0**, a JavaScript distribution entry and an empty runtime dependencies object. Its latest GitHub release endpoint instead returns **1.0.5**, published **2018-08-27**; the observed default-branch commit is **2024-05-31**. Neither release age nor recent development-tool changes establishes current browser compatibility or abandonment. [S2,S4,S5]

The actual pinned LICENSE.md contains MIT terms: retain the copyright and permission notice in redistributed copies or substantial portions. This code licence does not establish rights to demo imagery, fonts or branding. [S3]

## Integration, costs and alternatives

These are browser DOM utilities, not React components or hosted services. No reviewed manifest declares a React peer compatibility contract. A future React/Next integration should isolate an imperative enhancement beneath a client-side element reference, initialize only after the target exists, and define who owns restoration before framework updates. This is a recommendation to test, not verified framework support.

A later authorized prototype would install a verified pinned `split-type` or `splitting` package in the existing frontend build, import its entry point and supply original CSS or an animation library. Nothing was installed here. No backend/API credentials, MCP server or new tool connection is required for this frontend use. The reviewed source packages declare no runtime dependencies; development/build dependencies are separate and were not security-audited. Hosting/bandwidth, DOM work and engineering time remain real costs; no paid service is required by the reviewed setup, and no cost or performance benchmark was measured.

The repositories contain documentation/examples and Splitting's README links a maintainer demo collection. Demos were not operated and their assets were not copied. Prefer whole-heading CSS motion if per-word control has no information value. A bespoke author-controlled span structure is another alternative for very short fixed text, but localisation, wrapping and cleanup remain application responsibilities.

## Original adaptation proposal — untested

1. Keep the original heading visible and meaningful before JavaScript. Enhance only a short display heading, not body prose or interactive links.
2. Prefer words over characters until the design truly needs letters. Build an original stagger from indices instead of copying demo typography or media.
3. Wait for the intended font/layout before measuring lines. Treat width changes, content changes, font substitution and locale changes as invalidation events; do not reuse stale line geometry.
4. Cancel animations targeting old nodes before restoring/re-splitting. Reacquire node references afterward. For Splitting, establish and test restoration ownership rather than assuming a SplitType-style revert API.
5. Preserve an unsplit reduced-motion/failure path. Do not hide all text while waiting for an animation dependency.
6. Test the rendered semantic heading with assistive technology. Do not indiscriminately add aria-label/aria-hidden to containers containing links, or assume visual text is announced correctly because the source string is unchanged.

Original future application: a project-title reveal that uses word indices on wide screens, falls back to a whole-heading fade on constrained layouts, and restores ordinary text after completion. A second application is editorial grid sequencing through existing item indices without fragmenting text at all. Neither is implemented.

## Proposed acceptance tests and limitations

Compare unsplit text with one candidate at a time using the same licensed font and synthetic headings. Test delayed/failed fonts, narrow/wide container changes, 200% zoom, text-spacing overrides, long translated headings, Arabic/Indic scripts, combining marks/emoji, copy/paste, keyboard/screen-reader reading, reduced motion, route remounts and rapid content replacement. Check for stale node references and excess spans after repeated setup/cleanup. Measure actual layout and animation cost on target devices.

**None of those tests ran.** No installation, runtime, accessibility, performance, browser, dependency-security or production-integration test was performed. The npm website returned 403 and registry metadata was inaccessible through the web tool; published npm versions and package tarball licences remain unverified. Source snapshots, GitHub releases and live guide are different evidence layers. Independent documentary review and structural/privacy checks are recorded in qa.md.

## Sources
- [T1] [SplitType README](https://github.com/lukePeavey/SplitType/blob/36af092f7061ef9b6ea33261b688a3fe04b890fb/README.md)
- [T2] [SplitType source package manifest](https://github.com/lukePeavey/SplitType/blob/36af092f7061ef9b6ea33261b688a3fe04b890fb/package.json)
- [T3] [SplitType latest GitHub release](https://api.github.com/repos/lukePeavey/SplitType/releases/latest)
- [T4] [SplitType observed commit](https://github.com/lukePeavey/SplitType/commit/36af092f7061ef9b6ea33261b688a3fe04b890fb)
- [T5] [SplitType recursive source tree](https://api.github.com/repos/lukePeavey/SplitType/git/trees/36af092f7061ef9b6ea33261b688a3fe04b890fb?recursive=1)
- [S1] [Splitting.js official guide](https://splitting.js.org/guide.html)
- [S2] [Splitting source package manifest](https://github.com/shshaw/Splitting/blob/d5a8fd0137e90770ae10d2e0e874a4a101161544/package.json)
- [S3] [Splitting MIT license](https://github.com/shshaw/Splitting/blob/d5a8fd0137e90770ae10d2e0e874a4a101161544/LICENSE.md)
- [S4] [Splitting latest GitHub release](https://api.github.com/repos/shshaw/Splitting/releases/latest)
- [S5] [Splitting observed commit](https://github.com/shshaw/Splitting/commit/d5a8fd0137e90770ae10d2e0e874a4a101161544)
- [S6] [Splitting README and demo index](https://github.com/shshaw/Splitting/blob/d5a8fd0137e90770ae10d2e0e874a4a101161544/README.md)

