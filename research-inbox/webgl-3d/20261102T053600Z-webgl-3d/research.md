# Color output needs one owner per rendering path

Run: `20261102T053600Z-webgl-3d` · Scheduled: 2026-11-02 05:36 UTC · Evaluation began: 2026-11-02 05:40 UTC.

## Question and incremental value

How should a Three.js post-processing chain distinguish texture interpretation, tone mapping and display conversion without applying an output transform twice?

The live catalog and all nine existing WebGL packages were read first. Earlier material guidance mentions color-space tags; none defines an output-stage ownership contract or the documented exception to placing OutputPass last. The live topic directory contains nine published packages and no same-run or additional unfinished directory.

## Source findings

- OutputPass supplies tone mapping and color-space conversion, drawing its settings from the renderer. It normally belongs near the chain's end, but a pass requiring sRGB input, such as FXAA, goes after it. “Always put it last” would therefore be incorrect. [S1](https://threejs.org/docs/pages/OutputPass.html)
- Texture.colorSpace describes color data and defaults to NoColorSpace; the documentation specifies SRGBColorSpace or LinearSRGBColorSpace for color textures. This is a different API boundary from the renderer's output settings. [S2](https://threejs.org/docs/pages/Texture.html)
- WebGLRenderer documents outputColorSpace separately from toneMapping and toneMappingExposure. Their documented defaults are SRGBColorSpace, NoToneMapping and 1 respectively. An sRGB output setting does not itself mean that a tone-mapping operator was selected. [S3](https://threejs.org/docs/pages/WebGLRenderer.html)
- The pinned OutputPass copies renderer exposure each render and rebuilds its shader defines when renderer outputColorSpace or toneMapping changes. It can draw either to the screen or to a write buffer; an intermediate destination does not automatically disable its configured output transform. [S4]
- The pinned OutputShader samples its input, applies the selected tone-mapping branch, then conditionally applies the sRGB transfer function. It does not inspect the input image to discover whether an earlier pass already performed those operations. [S5]

## Proposed color-stage contract — original synthesis, untested

For each presentation path, maintain a small pass inventory: input interpretation, operation, output interpretation and owner. Do this separately for direct rendering, the composer path and any screenshot/export path. Names such as “final” or “copy” do not establish a color contract.

1. Record source texture intent independently of the desired display appearance. Do not change asset interpretation merely to compensate for a later pass error.
2. Select a documented output policy, including tone mapper, exposure and output color space. Record the actual renderer and addon version.
3. Assign one deliberate output-stage owner for the intended chain. Inspect custom shaders and wrappers for their own tone mapping or transfer functions before inserting OutputPass.
4. Keep passes on the side of that boundary required by their documented input. The FXAA exception is a concrete reason to track input encoding rather than sort by an intuitive “final pass” label.
5. When bypassing effects or switching views, verify that the resulting path still has the intended transformation exactly where designed. A disabled pass can change the contract even when every other shader compiles.
6. Treat a target written by OutputPass according to the transformation actually performed. Do not assume that all offscreen targets contain the same kind of values.

“Exactly one” here is a recommended architecture for an ordinary final-output path, not a universal law forbidding intentional decode/encode stages. Complex pipelines must document those stages explicitly. Duplicate tone mapping or transfer application is a diagnostic hypothesis to test, not an established explanation for every washed-out or dark scene. Lighting, source interpretation, exposure and other effects can also affect appearance.

Avoid fixing a mismatch by adding arbitrary gamma powers until the image looks plausible on one monitor. First compare the pass inventory and renderer settings. A software contract still does not certify display calibration, wide-gamut behavior or perceptual equivalence across devices.

## Acceptance fixture — proposed, not executed

Use original color patches and a controlled scene, not protected demo media. Compare a direct renderer path with an otherwise minimal composer path under the same explicit policy. Then test:
- adding/removing the output stage, including an intentional duplicate;
- a custom pass that already transforms output;
- FXAA placed according to its documented input;
- switching between direct and composer rendering;
- changing exposure or tone mapper;
- capturing an offscreen result that has already passed through OutputPass.

Collect settings, pass order, target interpretation and observed output. Do not call the fixture passed from source review alone. A future original application is a developer-only pass inspector that labels color boundaries and warns about undeclared output transforms; such warnings would require verified adapters, not guesses from pass names.

## Compatibility, operation, licence and alternatives

This is a JavaScript Three.js WebGLRenderer/addon integration note, not a tool shortlist. OutputPass is imported explicitly from three/addons/postprocessing/OutputPass.js. Use the addon from the same pinned distribution; WebGPU/TSL and R3F wrapper behavior are not certified here.

The inspected development commit is 8086b3fda07a70bffad7ee39265c9617994ec482, dated 2026-11-01T22:25:58Z. Its manifest declares 0.187.0, ESM/addon exports, development tooling and no dependencies field. This is not verification of the npm artifact or installed application. [S6] Actual licence text is MIT: retain copyright and permission notices with copies or substantial portions; no warranty. That licence does not grant rights to unrelated models, textures or demo assets. [S7]

No service, credential, API or MCP connection is needed by this design. Extra full-screen passes consume GPU work and render-target bandwidth; no cost or speed benchmark was run. Alternatives are direct rendering without post-processing, a simpler pass chain, or a static poster. No library was installed, demo executed, application edited or deployment made. Browser, GPU, runtime, performance, accessibility and integration tests remain unperformed. The attempted manual color-management URL returned 404 and was not evidence.

## Pinned references

- [S4: OutputPass implementation](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/examples/jsm/postprocessing/OutputPass.js)
- [S5: OutputShader implementation](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/examples/jsm/shaders/OutputShader.js)
- [S6: Package manifest](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/package.json)
- [S7: MIT licence](https://github.com/mrdoob/three.js/blob/8086b3fda07a70bffad7ee39265c9617994ec482/LICENSE)

