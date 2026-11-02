# Independent documentary QA

Verdict: APPROVED — documentary only, not runtime certification. Concurrent-package provenance fix verified.
Reviewer: independent documentary reviewer.
Review date: 2026-11-01, approximately 11:42 UTC.
Scope: research.md, sources.json, manifest.json in this candidate folder.

## Evidence checked independently

All four official source URLs were opened with the web tool during this review; source pages were treated as evidence, not instructions.

- [LoadingManager](https://threejs.org/docs/pages/LoadingManager.html), abort section: explicitly conditions abort on loader implementation and browser support for AbortSignal.any. The note correctly avoids a universal cancellation guarantee.
- [GLTFLoader](https://threejs.org/docs/pages/GLTFLoader.html), introduction, constructor, load and parseAsync: supports the manager parameter, completion callback, asynchronous parsing and special ImageBitmap disposal handling. The note does not assert decoder cancellation support.
- [R3F Hooks](https://r3f.docs.pmnd.rs/api/hooks), useLoader note/warning: documents URL cache keys and warns about mutation/disposal of reused assets. Ownership and eviction recommendations are clearly synthesis, not claimed built-in reference counting.
- [React useEffect](https://react.dev/reference/react/useEffect), parameters, caveats and fetching-data example: confirms dependency-change/unmount cleanup, development Strict Mode setup/cleanup, and the ignore flag guarding late responses. The page displays v19.3; the note correctly distinguishes that label from installed runtime versions.

## Novelty and provenance

Read the entire earlier research.md via GitHub read-only fetch on research-data at research-inbox/webgl-3d/20261101T053112-3d-webgl-engineering-01/research.md (returned blob SHA f607a26786d15ad76efdb3c61e906ca0722cd7d8). That package covers renderer migration, assets, caching, lifecycle/disposal and shared-consumer tests. This note adds the focused obsolete-completion/generation guard and separates correctness, cancellation and disposal ownership. Shared-resource caveats overlap appropriately; it is not a duplicate of the earlier package.

The candidate uses short attributed paraphrases and original architectural synthesis. No copied source code, full-page reproduction, third-party assets, or apparent substantial verbatim passages were found. Software/asset reuse rights are not asserted.

## Data, privacy and limitations

- Both JSON files parsed successfully. Basic structural checks passed: four sources agree with source_count, source IDs are unique, source URLs use HTTPS, expected source metadata keys exist, and all three manifest-listed files exist.
- No formal JSON Schema was supplied; this is a structural/consistency check, not certification against an external repository schema.
- Full manual reading found only public documentation references and research metadata, with no personal data or credentials apparent. Pattern scans of the three candidate files found zero matches for common GitHub/OpenAI/AWS credentials or private-key headers. This is a bounded check, not proof against every possible secret format.
- Proposed tests are explicitly not performed. No browser, GPU, asset loader, decoder, application code, accessibility behavior, performance or abort operation was executed. Only documentary retrieval and local JSON/credential-pattern checks were run.
- Moving-page/version uncertainty is explicitly disclosed; no installed-version compatibility claim is made.

## Concurrent-package recheck (approximately 11:44 UTC)

Independently fetched and read the complete research-inbox/webgl-3d/20261101T064013-webgl-resilience-02/research.md at branch commit 185a2250df0c8784e886e1e32d146d8ea8af832e, returned blob SHA 3a9b8e1b616a05e18cb2f26660a4caed74baede5. Its context-loss recovery policy already recommends ignoring stale asynchronous completions from an old generation, and its tests cover old callbacks while loading. Thus the generation-guard idea itself is not new to the research collection.

This candidate remains materially useful as a focused addendum: conditional LoadingManager.abort support, the separation of cancellation from correctness, disposal of stale exclusively owned results, and distinct shared-cache ownership recipes are not developed in that concurrent package.

Exact required fix: expand research.md's Why this is new paragraph with a link to the resilience-02 package, acknowledge its existing stale-generation recommendation, and describe this note as elaborating cancellation support and asset-result ownership rather than originating the generation guard. No other content fixes are required. After that narrow fix, documentary approval may be restored. At publication, update the manifest's pending-QA status/limitation and file list while retaining the documentary-only limitation and truthful publication state.

Resolution (approximately 11:44:27 UTC): re-read the complete revised research.md. The Why this is new paragraph now links the concurrent resilience package, explicitly acknowledges its stale-generation recommendation, and limits this addendum's contribution to conditional cancellation and exclusive-versus-shared result ownership. Remaining research content is unchanged from the reviewed candidate. The requested fix is satisfied; final documentary approval restored.
