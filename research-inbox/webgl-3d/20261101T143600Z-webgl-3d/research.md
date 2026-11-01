# One cached GLB, two independent animated characters

Run: `20261101T143600Z-webgl-3d` · Scheduled: 2026-11-01 14:36 UTC (09:36 America/New_York, EST) · Evaluated: 2026-11-01.

## Question and incremental value

How can two visible copies of one skinned GLB play different animations without sharing pose state or destroying each other's resources? Supporting questions: which resources remain shared after cloning, and who owns mixer advancement and cleanup?

The four existing WebGL references cover asset delivery, renderer recovery, stale asynchronous loads and shader/material portability. The stale-load note already recommends consumer-specific instances, but does not resolve bone association or per-copy animation ownership. This addendum fills that narrower gap rather than repeating caching or renderer migration advice.

## Verified source findings

1. **Clone the skeleton-bearing hierarchy, not just the visible mesh.** `SkeletonUtils.clone` reconnects cloned skinned meshes to cloned bones; the bones must descend from the supplied root. Geometry and materials remain shared references. This is not a deep copy of all resources. [SkeletonUtils](https://threejs.org/docs/pages/module-SkeletonUtils.html)
2. **Playback state belongs to a root/mixer relationship.** Three.js permits a mixer per independently animated object. Repeated `clipAction` calls with the same clip and root return the same action. Mixer updates take seconds. Before uncaching a root, stop its actions; a single-root mixer may use `stopAllAction`. [AnimationMixer](https://threejs.org/docs/pages/AnimationMixer.html)
3. **Disabled is not reset.** Re-enabling an action resumes its current time. `reset` resets playback; `clampWhenFinished` affects a genuinely completed final loop, not an interrupted action. Therefore a replay policy must distinguish resume, restart and finish-pose retention. [AnimationAction](https://threejs.org/docs/pages/AnimationAction.html)
4. **R3F has shared asset and frame lifecycles.** `useLoader` caches by URL and warns against casually mutating or disposing reused assets. `useFrame` supplies seconds and unsubscribes on unmount. In demand mode `invalidate` requests a render: a frame callback is not itself proof that frames are continuously scheduled. [R3F Hooks](https://r3f.docs.pmnd.rs/api/hooks)
5. **Drei offers a binding abstraction, not a cloning contract.** `useAnimations` wraps AnimationMixer and accepts a pre-existing Object3D or ref root. Its documentation does not say that this creates an independent clone for every consumer. [Drei useAnimations](https://drei.docs.pmnd.rs/abstractions/use-animations)

## Recommended ownership model — original synthesis, not executed

| Layer | Proposed owner | Mutation/disposal rule |
|---|---|---|
| Loaded source scene and clips | Asset cache | Treat as immutable templates; eviction is a separate policy. |
| Cloned hierarchy and bones | One visible character | Clone once per source identity, not each render. Include every referenced bone below the cloned root. |
| Mixer/actions | Same character instance | Bind to its clone, not the cached source; advance from one owner only. |
| Shared geometry/material/texture | Asset cache or explicit shared owner | Removing one character must not dispose shared resources. |
| Intentionally customized material | Character or explicit variant owner | Clone the material before changing per-character properties; separately document texture ownership. |

A source cache hit answers “do we have the asset?” It does not answer “which pose belongs to this character?” Use a full skeleton-bearing scene root as the initial cloning boundary. Optimize that boundary only after inspecting the actual hierarchy. The recommendations apply to independently animated copies, not a promise of efficient crowd rendering or GPU instancing.

### Two implementation alternatives

**Explicit Three.js owner:** clone the source hierarchy; construct a mixer rooted at that clone; resolve the requested clip; create/play its action; advance the mixer from exactly one frame subscription. On final teardown, stop actions and uncache that clone. This cleanup concerns animation bindings; it is not permission to dispose cache-owned GPU resources.

**R3F + Drei adapter:** create a stable clone and pass it, rather than the cached scene, as the root to `useAnimations`. Let the adapter own mixer advancement; do not also add a second manual mixer update. Verify the installed hook's cleanup implementation before adding another cleanup layer. The short API page alone does not establish all disposal details.

Use continuous rendering while animation is active, or implement and test a demand-loop invalidation policy. Do not ship a frozen character on the assumption that calling `play()` guarantees render scheduling. Keep semantic controls outside the canvas and define a reduced-motion pose policy; neither accessibility nor motion comfort was assessed here.

## Troubleshooting and proposed acceptance tests

| Symptom or test | Investigation / expected evidence |
|---|---|
| Changing character A changes B's pose | Inspect whether both actions bind to the same root; verify distinct bones and mixers. |
| Distorted or missing skin after cloning | Confirm the clone input contains referenced bones; do not assume a detached mesh is sufficient. |
| Tinting A also tints B | Inspect shared material identity before mutation. |
| Removing A breaks B | Audit disposal against ownership, not just traversal membership. |
| Animation runs twice as fast | Count update owners; remove duplicate manual plus adapter updates. |
| Replay resumes unexpectedly | Test explicit restart versus resume and interruption behavior. |
| Two-copy test | A plays one clip, B another; advance, remove A, confirm B remains valid. |
| Lifecycle stress | Mount/unmount and development effect replays; confirm no stale mixer bindings, leaks or double updates. |
| Demand rendering | Start, pause, finish and resume; record frame scheduling rather than assuming it. |

All table entries are proposed tests, **not passed tests**. A practical next evaluation is one licensed, minimal two-character fixture with known bones and two clips. No asset was downloaded or execution environment installed for this report.

## Version, licensing and evidence limits

The five primary pages were read on 2026-11-01. Three.js and R3F pages are moving documentation, not a verified application lockfile. The Drei page displayed `v10.7.9-5-gbf6f4ad master`; that is a documentation build label, not a release or installed-version assertion. Release dates, exact installed versions and compatibility remain unknown. Pin compatible versions and inspect the actual hook implementation before applying this design.

This is original documentary analysis, not a software shortlist or copied implementation. No third-party asset licence was evaluated; acquire explicit rights for any future model. No GLB, Blender, GPU, browser, performance, accessibility, installation or integration test was run. No existing application or configuration was changed.
