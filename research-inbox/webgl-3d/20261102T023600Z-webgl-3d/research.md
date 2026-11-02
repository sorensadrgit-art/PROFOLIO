# Renderer counters need a measurement contract

Run: `20261102T023600Z-webgl-3d` · Scheduled: 2026-11-02 02:36 UTC · Evaluation began: 2026-11-02 02:34 UTC (early delivery of the scheduled task).

## Question and incremental value

How can a Three.js diagnostic panel avoid misreporting a multipass frame or presenting renderer bookkeeping as exact GPU memory?

The live catalog and eight existing WebGL packages were read first. Earlier reports recommend profiling and discuss resource ownership, but do not define counter reset boundaries or distinguish the two info implementations. The topic directory contained eight published run directories, no same-run artifact and no additional unfinished directory.

## Source findings

- WebGLRenderer's documentation says its statistics reset by default on each render call. For multiple passes, it recommends disabling automatic reset and resetting after the application's frame. [S1](https://threejs.org/docs/pages/WebGLRenderer.html)
- The pinned WebGLInfo implementation exposes geometry and texture counts, plus draw calls and primitive counts. Its reset function zeroes calls/triangles/points/lines, not memory counts. Triangle accumulation includes instance count. These fields do not supply byte usage or frame duration. [S2]
- The separate common Info API has different field meanings: render.calls is cumulative, frameCalls is per frame, and drawCalls is per frame. It also exposes byte-labelled memory fields. A shared dashboard cannot safely reuse WebGLInfo field interpretations merely because both objects are named info. [S3](https://threejs.org/docs/pages/Info.html)
- The pinned common implementation calculates tracked sizes rather than reading a total physical GPU allocation. In particular, its compressed-texture size helper returns a placeholder of 1; program size uses source-code length as an approximation, and generated mipmaps use an approximate multiplier. Consequently, the presence of memory.total does not justify labelling it exact VRAM usage. These are findings about this commit, not all releases. [S4]

## Proposed measurement contract — original synthesis, untested

Define a sample by renderer family, actual installed version, scene revision, pass configuration and an explicit measurement interval. Do not combine values from different schemas into an apparently comparable chart without an adapter.

For an application-owned WebGLRenderer frame:
1. Assign a single owner for statistics reset. If a framework owns the loop, integrate with its verified before/after render hooks instead of starting another loop.
2. Disable automatic reset only when that owner can account for every intended render call.
3. Render the complete intended pipeline, including additional views and effects that belong in this sample.
4. Copy scalar values into a new sample before clearing counters. Retaining the mutable info object would allow a later reset to change the apparent historical record.
5. Reset once for the next interval. Mark interrupted/incomplete samples rather than comparing them as complete frames.

This ordering is a proposed instrumentation design, not a guarantee about arbitrary wrappers or hidden internal render operations. Do not reset statistics to attempt resource cleanup: resetting bookkeeping is not disposing assets. Do not force memory counts to zero to make a dashboard look healthy.

Label WebGL memory fields as resource counts. For common Info, label byte fields as renderer-tracked estimates with known coverage limits. Unsupported fields should be null/unavailable, not zero. A zero means a measured value only when the field and interval are actually supported. Keep browser heap, downloaded file size, tracked GPU-resource estimates and device-wide memory in separate categories.

Counts are useful for detecting changes in rendering work, but are not timings. A lower draw-call count does not alone prove a faster scene, and constant texture count does not establish constant texture size. Compare repeated observations under the same scene, resolution and pass configuration; use separately measured frame timing to evaluate responsiveness. Avoid an automatic quality downgrade based solely on an unvalidated total-memory field.

## Acceptance fixture — proposed, not run

Use an original small scene with a second view or post-processing pass. Capture:
- a single-render baseline;
- the same scene with all intended passes inside one sample;
- an intentional mid-frame reset, confirming that the diagnostic detects an invalid interval;
- a reset after capture, confirming that the stored sample does not mutate;
- two texture sizes with the same resource count;
- a compressed texture on the pinned common Info path, confirming the estimate limitation;
- a renderer-family switch, verifying schema selection and unavailable-field handling.

Record the expected scope and actual values; do not invent a universal draw-call or byte threshold. No case in this fixture has been executed. A useful future application is a development-only portfolio diagnostic overlay that displays counter scope and estimate quality beside the numbers, making unsupported comparisons visible.

## Compatibility, operation, licence and alternatives

Three.js is a JavaScript 3D library. The inspected development snapshot declares 0.187.0, exposes ordinary/WebGPU/TSL module entry points, and has development tooling but no dependencies field. Its commit is dated 2026-11-01T22:25:58Z. This is not a verified npm release or the application's installed version. [S5] Pin the application and inspect its actual renderer implementation before adopting the adapter; an R3F integration was not validated.

The actual included licence is MIT: preserve copyright and permission notices with copies or substantial portions; software is supplied without warranty. This does not license unrelated model, texture or demo assets. [S6] Only original analysis is published here, not source files.

No new service, API credential, MCP connection or paid hosting is required by this proposed local diagnostic panel. Sampling, allocations and logging have CPU/memory costs; use bounded retention and measure overhead. Alternatives are a minimal per-scene counter log, browser profiling for timings, or no always-on panel. No demo was run and no software installed. No GPU, browser, runtime, performance, accessibility or integration test was performed. npm artifact and lockfile compatibility remain unknown. The failed disposal-manual URL returned 404 and was not used as evidence.

## Pinned source links

- [S2: WebGLInfo implementation](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/src/renderers/webgl/WebGLInfo.js)
- [S4: Common Info implementation](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/src/renderers/common/Info.js)
- [S5: Package manifest](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/package.json)
- [S6: MIT licence](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/LICENSE)

