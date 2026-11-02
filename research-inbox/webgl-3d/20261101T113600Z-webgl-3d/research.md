# Prevent stale GLB loads from attaching after a route changes

Run: 20261101T113600Z-webgl-3d
Scheduled: 2026-11-01 06:36 America/New_York (11:36 UTC)
Question: How should a React/Three.js scene handle an asset load completing after its owner has unmounted or selected another asset?
Status: Documentary candidate, not runtime certified.

## Why this is new
The initial 3D package covered compression, renderer selection, caching and disposal broadly. A concurrent [runtime resilience update](../20261101T064013-webgl-resilience-02/research.md) already recommends ignoring stale asynchronous generations during GPU recovery. This narrow addendum does not originate that guard: it elaborates conditional LoadingManager cancellation and the distinct ownership of stale exclusive results versus shared cached assets. It does not replace either package or certify their version claims.

## Verified facts
- Three.js LoadingManager has abort(), but its documentation conditions cancellation on loader implementation and browser support for AbortSignal.any(). Do not interpret a manager abort call as a guarantee that every request or decoding task stopped. [S1](https://threejs.org/docs/pages/LoadingManager.html)
- GLTFLoader accepts a LoadingManager, delivers the loaded asset through onLoad, and provides asynchronous parsing. Its documentation warns that image bitmaps need special disposal handling. The page does not establish that every decoder is cancellable. [S2](https://threejs.org/docs/pages/GLTFLoader.html)
- R3F useLoader caches assets by URL and warns against careless mutation or disposal of reusable assets. A component's end of use is therefore not automatically the end of the shared resource's lifetime. [S3](https://r3f.docs.pmnd.rs/api/hooks)
- React documents cleanup on dependency changes and unmount, and an ignore flag to prevent late responses from updating state. Development Strict Mode adds a setup/cleanup cycle; cleanup must mirror setup. [S4](https://react.dev/reference/react/useEffect)

## Proposed ownership protocol (original synthesis, UNTESTED)
Use three separate decisions:
1. Is this request still allowed to attach its result?
2. Can pending work be cancelled?
3. Who owns disposal of the resulting resources?

For an imperative, exclusively owned loader, create a request generation or closed flag inside each effect. Cleanup first invalidates that generation, then requests cancellation where supported. Every success callback checks the generation before attaching to the scene. A stale result is never attached; hand it to the exclusive-owner release routine. A current result is registered with that same owner so later cleanup releases it once.

Cancellation is an efficiency measure, not the correctness guard. Keep the generation check even when abort is supported. Treat a late error from an obsolete request as a diagnostic, not a reason to replace the new route's current UI with an error screen.

For cached R3F assets, do not transplant the exclusive-owner disposal routine into each consumer. Let consumers detach their own instances, while a cache/resource owner decides when no remaining consumer needs shared geometry, materials, textures or image data. Define cache eviction separately. This is an architectural recommendation, not a claim that a particular cache already provides reference counting.

## Two implementation recipes
**Exclusive preview viewer:** one request-scoped manager; one generation flag; one attachment registry; one idempotent release routine. Test load A, navigate to B, then resolve A last. Only B may become visible. Include parse/decode completion after cancellation, not only a cancelled network fetch.

**Reusable product carousel:** load shared source assets through a stable cache; create consumer-specific scene instances under an explicit ownership policy. Test two consumers, remove one, and confirm the other remains valid. Release shared resources only under the cache owner's eviction policy. Do not assume that cloning an object graph duplicates every GPU resource.

## Required implementation tests, not performed here
- A then B resolves in reverse order; unmount before load resolves.
- Strict Mode setup/cleanup/setup does not leak or attach stale objects.
- Abort unsupported or decoder still completes; generation guard still works.
- Repeated cleanup is harmless; shared assets survive one consumer leaving.
- ImageBitmap/resource cleanup follows actual ownership.
- Loading/error UI preserves semantic DOM content and keyboard access.

## Versions, risks and limits
These are moving documentation pages observed 2026-11-01, not a tested package lockfile. Exact Three.js/R3F installed versions are unknown; React documentation displayed v19.3, which is a documentation label, not the project's runtime version. Verify APIs against the target lockfile before implementation. No asset, code, browser, GPU, performance, accessibility or abort behavior was executed. No third-party code or assets were copied. Two guessed legacy Three.js URLs returned inaccessible/404 and were excluded from factual support. Licence evaluation of software reuse was not required for this original documentary note; do not infer any asset reuse rights.

