# A horizontal-looking scene is not a horizontal scroller

Run: 20261102T151200Z-motion-interaction  
Scheduled: 2026-11-02T15:12:00Z  
Evaluated: 2026-11-02  
Status: documentary research; recommendations and acceptance fixtures are untested.

## Question and gap

When vertical scrolling translates a gallery horizontally, how should nested ScrollTriggers identify the correct coordinate owner without placing unsupported pinning or snapping on child triggers?

The live catalog and twelve existing motion reports were read first at branch head `2d68f1ac30099267efb205225105e85c8885f58e`. They cover engine choice, refresh, playhead synchronization and ownership but not the `containerAnimation` coordinate contract. No same-run artifact or unpublished topic directory was found. This is an implementation addendum, not another library shortlist.

## Source findings

- Horizontal transforms driven by vertical scrolling are not native horizontal scrolling. ScrollTrigger's `containerAnimation` connects a child trigger to the parent animation. Its parent transport must have linear easing; containerAnimation-based triggers do not support pinning or snapping. Moving the trigger itself horizontally requires start/end compensation. A separate `horizontal: true` option describes a horizontal scroll setup. [S1](https://gsap.com/docs/v3/Plugins/ScrollTrigger/)
- The official 3.8 release article, dated September 27, 2021, introduces this feature with a pinned, vertically scrubbed parent and a child triggered by horizontal position. The parent can be pinned even though a child using containerAnimation cannot. Its embedded example is a reference, not a tested project integration. [S2](https://gsap.com/blog/3-8/)

## Proposed coordinate contract — original synthesis, untested

Create a short ownership record before constructing the scene:

| Layer | Owns | Must not be mistaken for |
|---|---|---|
| Actual page scroller | Vertical input and scroll position | The translated rail's x position |
| Parent transport animation | Horizontal translation of the rail | Independent native scrollLeft |
| Child measurement wrapper | Stable reference position within the rail | The independently animated inner artwork |
| Inner artwork animation | Decorative opacity, scale or vertical motion | Transport geometry |
| Application navigation | Focus, reading order and explicit destinations | Animation progress |

Use one parent transport reference for every child in the scene. Keep transport linear; place decorative easing on inner artwork instead. A design request for eased travel is a reason to redesign or separately validate the mapping, not quietly remove the documented linear requirement.

Do not add child pin/snap options and assume they will create another independent scroll stage. If a story requires page-level pinning or settling, design it at the real-scroll owner and verify it there. Do not interpret that recommendation as a promise that every combined parent snap, scrub and child-trigger arrangement works.

Prefer a wrapper that moves only with the rail as each child's trigger. Animate a descendant for independent horizontal flourish. This removes one source of measurement compensation; it does not remove resize, font or content-change obligations.

On teardown, retire child triggers before the shared transport and remove the complete scene as one owned unit. On reconstruction, obtain the new parent reference rather than reusing an old reference held by children. This is proposed lifecycle ordering, not a documented atomic operation.

## Decision and alternatives

Choose this arrangement only for an authored story that benefits from vertical-to-horizontal travel. For a gallery the reader should browse freely, prefer ordinary document flow or a real horizontal overflow region; then reason about actual scroll coordinates rather than imitating them. A static stacked layout is the default escape route for narrow screens, reduced motion, failed initialization or an unverified keyboard experience.

The benefit is precise synchronization of child moments with a cinematic transport. Costs include additional geometry, lifecycle and navigation testing. No performance savings are claimed. A future original use is a process timeline whose rail passes three fixed milestones, each revealing a detail on an inner element. Keep the milestones informational, not the only route to essential content.

## Tool fit, maintenance, licence and operational requirements

GSAP is a JavaScript animation toolkit; ScrollTrigger supplies its scroll relationship. The pinned package manifest reports **3.15.0**, JavaScript module/distribution entry points and TypeScript declarations, with no dependencies or peerDependencies fields. This is a repository observation, not an npm-latest or installed-version assertion. [S3](https://github.com/greensock/GSAP/blob/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2/package.json)

The observed repository head is commit `13e2b790546426a1a2e0e9b409f3f8dc6d6611f2`, dated April 13, 2026, whose message identifies 3.15.0 and fixes. A commit date does not establish npm publication time or ongoing support guarantees. [S4](https://github.com/greensock/GSAP/commit/13e2b790546426a1a2e0e9b409f3f8dc6d6611f2)

The actual Standard No Charge licence permits commercial websites under its terms but restricts competing visual animation builders and removal of proprietary notices. GSAP is not MIT-licensed simply because its repository is public. The licence page gives an April 30, 2025 effective date and May 30, 2025 modification date. Check the full terms for the intended product; this is not legal clearance. [S5](https://gsap.com/community/standard-license/)

Installation can use npm or script files. Import the core and ScrollTrigger and register the plugin explicitly, particularly with bundlers that tree-shake. The installation guide says plugins are available through public npm for 3.13 onward. [S6](https://gsap.com/docs/v3/Installation/)

For this proposed client-side scene, no hosted API, MCP service, account or server daemon is required. Existing static hosting and asset transfer still have project-specific costs; measured CPU/GPU, bundle, bandwidth and engineering costs are unknown. No install or deployment was performed. Framework lifecycle integration and the application lockfile must be checked before adoption. No browser-version compatibility matrix was executed.

## Acceptance fixture — all NOT RUN

Build a small fixture with three unequal-width panels, a vertically scrolled pinned transport and one child wrapper per panel. Record actual page scroll, parent animation time, child start/end and the wrapper's visible position. Test:

1. Scroll forward and backward slowly, then jump past the section. Compare child transitions with visible wrapper crossings.
2. Deliberately apply non-linear transport easing as a negative case; reject the configuration instead of accepting visually plausible timing.
3. Add independent x movement first to the trigger and then to its descendant. Confirm the accepted version keeps the measurement wrapper stable within the rail.
4. Resize, change text length and load late media; verify reconstructed measurements and no stale parent references.
5. Request child pin/snap in a negative configuration; the application's own validation should reject that unsupported requirement.
6. Navigate by keyboard and activate links in offscreen panels. Verify focus remains visible and essential content reachable; otherwise choose the static or native-scroll alternative.
7. Unmount/remount and change reduced-motion preference. Check for residual transforms, duplicate callbacks, orphan triggers and lost content.
8. Only after these checks, profile target devices and test any parent snapping separately.

No runtime, integration, performance, visual, accessibility or usability certification is implied. Source text was treated as untrusted evidence; no full repository, demo asset or substantial copyrighted passage was copied.

