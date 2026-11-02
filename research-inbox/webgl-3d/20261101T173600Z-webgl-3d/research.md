# Pick the project, not the instance slot

Run: `20261101T173600Z-webgl-3d` · Scheduled: 2026-11-01 17:36 UTC (12:36 America/New_York, EST) · Evaluated: 2026-11-01.

## Question and incremental value

How should an interactive instanced project gallery keep selection correct when instances move, reorder or disappear? Supporting questions: how should geometry updates reach picking, and when must hover be refreshed without pointer motion?

The five existing WebGL packages cover delivery, renderer recovery, stale asset loads, shader portability and independent animated characters. The delivery guide mentions instancing and bounds; it does not define selection identity across changing instance slots. This is a narrow interaction addendum, not another general performance guide.

## Verified source findings

1. **An instance hit returns an index.** Raycaster intersection data provides `instanceId` for InstancedMesh and a world-space hit point. Results are distance-sorted. Mesh back faces are not hit by default; choosing DoubleSide changes that behavior. [Raycaster](https://threejs.org/docs/pages/Raycaster.html)
2. **Rendering updates and bounds are separate responsibilities.** Instance matrices are local transforms. After `setMatrixAt`, flag `instanceMatrix.needsUpdate`; after color changes, flag `instanceColor.needsUpdate`. Bounding boxes are application-computed. Spheres are computed when needed, but transforming an instance can require recomputation. [InstancedMesh](https://threejs.org/docs/pages/InstancedMesh.html)
3. **Scene transforms have local and world forms.** Object3D distinguishes `matrix` from `matrixWorld`. Matrix-update APIs expose ancestor/descendant control; disabling automatic updates transfers responsibility to the application. [Object3D](https://threejs.org/docs/pages/Object3D.html)
4. **R3F events are not DOM occlusion rules.** Events can reach farther hit objects; `stopPropagation` also blocks those deliveries and can immediately cause pointerout. Some pointerout data comes from a previous intersection. A moving camera does not itself trigger hover; `events.update()` can rerun picking using the last pointer event. [R3F Events](https://r3f.docs.pmnd.rs/api/events)

## Proposed ownership contract — original synthesis, not executed

Treat a GPU slot as transient placement, not the identity of a portfolio project. Maintain a mapping from slot to stable application ID alongside the transforms it describes. Store selection by that application ID. When filtering or sorting changes slot order, replace mapping and transforms as one application-level revision before accepting another pick. If asynchronous code retains a hit, retain its revision too and reject it after remapping.

For example, slot 0 can represent project A before filtering and project B afterward. A delayed action based only on the number 0 has lost its meaning. This is an application consistency risk inferred from the index API, not a reported Three.js defect.

Suggested update sequence:
1. Build the next ordered project-ID list and transforms together.
2. Temporarily prevent selection handling during the synchronous replacement, or use an immutable revision snapshot in the handler.
3. Write transforms and update the active count; mark changed buffers and refresh affected bounds.
4. Ensure relevant world transforms are current before a custom raycast. Do not disable automatic matrix updates without an explicit update owner.
5. Publish the new mapping revision. Reconcile selected IDs that no longer exist; clear their selection rather than silently selecting a replacement slot.
6. Refresh hover after meaningful camera/scene changes when the interaction design requires it. Avoid unconditional extra raycasts as a default optimization.

This sequence is a design proposal. It does not assert atomic GPU upload or synchronization across arbitrary asynchronous callbacks. CPU event handling must use the same committed mapping as the submitted transforms.

## Small original handler sketch — untested

```ts
function resolveProject(hit: { instanceId?: number }, snapshot: {
  ids: readonly string[]
  revision: number
}) {
  const slot = hit.instanceId
  if (slot === undefined || !Number.isInteger(slot) || slot < 0) return null
  const id = snapshot.ids[slot]
  return id === undefined ? null : { id, revision: snapshot.revision }
}
```

Check `undefined`, not truthiness: slot zero is valid. The caller must also verify the hit belongs to the intended mesh, enforce current application permissions for any consequential action, and reject stale revisions before using delayed results. The sketch does not implement those surrounding controls or promise a security boundary.

## Troubleshooting and acceptance plan

| Symptom or scenario | Proposed check |
|---|---|
| Clicking first item does nothing | Exercise slot zero; remove truthiness-based rejection. |
| Filtering opens the wrong project | Verify slot mapping and transforms share one revision. |
| Moved instances disappear or miss picking | Inspect buffer update flags, bounds and parent world transforms. |
| Camera moves but hover stays old | Evaluate explicit event refresh after the relevant change. |
| Foreground and background both respond | Define intentional propagation/occlusion policy. |
| Removed item remains selected | Reconcile stable IDs, including delayed callbacks. |
| Pointerout changes the wrong highlight | Do not interpret prior intersection data as a new current hit. |

Run these in a minimal three-item fixture: reorder while selected, remove the selected item, move one instance beyond previous bounds, move the parent/camera, and defer a click callback across a mapping revision. Record exact IDs and visible results. **These tests were not run.** Keep a keyboard-operable DOM project list as a proposed companion; accessibility needs its own verification.

## Alternatives, costs and limitations

For a small gallery, separate meshes can simplify object identity and handlers; instancing is a trade-off, not a prerequisite. Large scenes may warrant a dedicated picking strategy after measurement. No draw-call, raycast or GPU benchmark establishes a threshold here. Buffer/bounds updates and extra picking have costs; profile the actual device and scene before increasing their frequency.

The four official pages were read on 2026-11-01. They are moving documentation; package versions, release dates and application lockfile compatibility were not established. Verify APIs against the pinned Three.js/R3F versions before implementation. This report is original analysis, not a software shortlist, licence audit or copied asset. No third-party implementation was installed or imported. No browser, GPU, runtime, accessibility or integration tests were performed, and no application code was changed.
