# Satori: a social card is a separate render contract, not a page screenshot

Run: 20261103T021800Z-open-source-creative  
Scheduled: 2026-11-03T02:18:00Z · Evaluated: 2026-11-03.

## Question and shortlist
Can an original portfolio reuse its visual language in generated social cards without assuming browser-identical rendering?

**Evaluate one candidate: Satori 0.63.2**, with a dedicated static card template and explicit font assets. This is an untested recommendation, not adoption approval. The live catalog had 101 packages; all 13 creative reports were read. None covered this generated-card contract. The topic tree contained no same-run or uncataloged unfinished directory. The unrelated existing COBE nested-index omission is preserved.

## Source findings
Satori converts a static element tree and styles to SVG. Its tagged README permits pure stateless components, not React state/effect APIs; it explicitly disclaims complete browser fidelity. A successful SVG return therefore does not establish equivalence with the live portfolio. [S1]

The reviewed version differs materially from older advice: it documents Taffy-backed Flexbox, Grid and block layout, and TTF, OTF, WOFF **and WOFF2** fonts. Do not apply older blanket “no Grid/no WOFF2” assumptions to this tag. Support still has boundaries: subgrid throws; interactive pseudo-classes do not represent interactive state. The stylesheet implementation includes hover/focus states among selectors that never match because elements are not interactive. [S1,S5]

Variable fonts are documented as rendering their default instance, not arbitrarily selected axes. The pinned font source renames the variation table before parsing so it is skipped. A supplied weight label is not evidence that a desired variable-font instance was generated. Use deliberately selected static font files when precise branding requires a particular weight, then verify the result. That last sentence is our recommendation. [S1,S6]

By default text is emitted as SVG paths; disabling embedFont emits text instead. This changes downstream font dependence, not the licence of the supplied font. Layout inputs still require fonts. Supplying a generic family name is not the same as loading an operating-system font: the README describes fallbacks among supplied fonts. [S1]

Invalid/unsupported inline declarations normally fail rendering. Providing onStyleError instead drops the declaration and reports it. Invalid stylesheet declarations are generally dropped rather than made fatal by default; the documented exception is values containing var(), which are checked when computed and can throw like inline styles. The source validates declarations that apply to matched rules; its feature-query checks are not reported as errors. **Inference:** an empty diagnostic list is not an exhaustive audit of every possible stylesheet state, and a returned SVG plus logged warnings is not automatic visual acceptance. [S1,S5]

## Version, dependencies and maintenance
GitHub latest release was 0.63.2, published 2026-10-20T09:08:32Z. The tag and observed default-branch head resolved to 074f0b38ed0f5d8fa768f709c0bb072b2d6c99e1, committed 2026-10-20T09:06:28Z. Release and commit timestamps are distinct. The tagged manifest uses the development placeholder version 0.0.0-development.2; do not call that the published release. npm lookup failed, so npm-latest and npm publication date remain unknown. [S2,S3,S7,S8]

This is a TypeScript/JavaScript library with WASM runtime components. The manifest declares Node >=16 and dependencies including HarfBuzz, an OpenType parser, compression, line breaking and CSS parsers. That engine range is metadata, not advice to run an unsupported Node release or proof of compatibility with a specific Next.js version. The README documents browser, Node and Web Worker usage; standalone builds require explicit layout WASM initialization. [S1,S3]

## Licence, integration, demos and costs
The actual tagged LICENSE is **MPL-2.0**, not MIT. Distribution of covered source, including modifications, must meet MPL terms; executable distribution also requires making corresponding covered source available and telling recipients how to obtain it. Notices must be preserved. Larger works can have other terms while complying for covered software. This is a documentary summary of sections 3.1–3.4, not legal advice. Font files, imagery, logos and template artwork need separate rights; public availability does not grant those rights. [S4]

A later authorized trial would pin satori@0.63.2, lock dependencies and build a small static render function with dimensions, approved text and licensed local assets. SVG is the immediate output; raster output adds a separately evaluated conversion stage. The README demonstrates Sharp, but this run does not audit or install it. A browser/worker deployment must assess WASM loading policy; a build-time render avoids a public rendering endpoint. No API account, MCP connection or paid hosted service is required by the basic library flow. Serverless-provider-specific limits and exact framework integration remain unverified. [S1,S3]

The maintainer links the OG Image Playground as a demo. It was not operated, and no demo assets were copied. Rendering compute, fonts/images, storage, bandwidth and maintenance still cost resources; no pricing, latency or bundle benchmark was measured. Avoid arbitrary user-supplied URLs or unbounded content in a future service; use approved assets and size/content limits. That security recommendation was not tested.

## Original application pattern — untested
Keep two representations: the accessible interactive webpage and a static share-card template using a deliberately small set of shared design tokens. Do not serialize the full page or assume hover styles will appear.

1. Version the card template, dimensions, content schema, font files and rendering dependency lock together.
2. Record approved font families/weights and asset digests. Treat variable-font rendering as a separate acceptance case.
3. Capture style diagnostics as structured build evidence. Fail publication on unexpected dropped declarations; review deliberate exceptions explicitly.
4. Validate the generated image visually after any token/font/library change. Keep the last approved card as fallback rather than publish a merely nonempty SVG.
5. Preserve meaningful page metadata and text independently; a raster or outlined-text image is not a replacement for page semantics.

Future application: an editorial case-study card factory using original typographic compositions, producing a reviewed image and a small provenance receipt per project. Prefer build-time generation where content changes infrequently; caching keys should include template/font/library identity, not only project ID.

## Alternatives and acceptance exercise
A manually authored static image has the fewest runtime dependencies for a small portfolio. A browser screenshot workflow may better capture existing browser CSS but introduces browser setup and capture-state discipline. Direct SVG authoring fits a tiny fixed layout. These are decision alternatives, not separately audited software shortlist entries.

Before adoption, test long and multilingual titles, line breaks, missing glyphs, the intended static and variable fonts, and downstream SVG/raster rendering. Compare inline invalid styles with matched/unmatched stylesheet errors; verify diagnostics block publication as intended. Compare the card with its own approved baseline, not an assumed pixel-identical webpage. Test asset failure, bounded input sizes and cold/warm rendering costs. None of these tests was performed.

## Limitations
Documentary research only: no installation, build, browser interaction, rendering, accessibility, performance, security or production integration test. npm metadata and tarball/source equivalence are unverified. Source links and dates are in sources.json; candidate metadata is in repos.json. Independent documentary QA is recorded in qa.md.

