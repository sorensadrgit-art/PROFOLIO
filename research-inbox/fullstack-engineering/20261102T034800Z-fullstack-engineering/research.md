# A deployment variable cannot rewrite an already-built browser bundle

Run: `20261102T034800Z-fullstack-engineering`  
Scheduled: 2026-11-02 03:48 UTC. Research began at 03:45 UTC after early delivery. Evaluated: 2026-11-02.

## Question and novelty

How should a Vite application distinguish build-time public configuration from runtime server configuration when promoting one artifact between environments?

The live catalog, branch tree and all five previous full-stack packages were read first. Those cover cache freshness, write concurrency, connection budgets, index migrations and React request ordering. No same-run artifact or unfinished full-stack package was observed. This is a focused deployment-contract reference, not a tool shortlist.

## Primary-source findings

1. **Build inputs become client code.** Vite replaces `import.meta.env` constants during build. Exposed custom values are strings; the default public prefix is `VITE_`. These variables must not contain secrets. Existing process variables outrank env files; mode-specific files outrank generic files. Mode and `NODE_ENV` are separate: choosing a mode does not alone determine development/production behavior. [S1](https://vite.dev/guide/env-and-mode)
2. **Prefix filtering is only one route into output.** The shared options document configurable `envPrefix`, reject an empty prefix, and describe `define` as static replacement. `publicDir` files are copied without transformation. Therefore reviewing only default-prefixed names is not a complete output review. [S2](https://vite.dev/config/shared-options#envprefix)
3. **The artifact has deployment geometry.** The build guide describes a static-hostable bundle and build-time base-path rewriting. It also identifies old-client dynamic-import failures when deployment removes old chunks. Configuration promotion and asset retention are distinct release concerns. [S3](https://vite.dev/guide/build)
4. **A backend can own HTML without rebuilding the asset loader.** Vite's backend integration guide uses a generated manifest to map source entries to built files and dependencies. This manifest is an asset map, not a runtime configuration or authorization service. [S4](https://vite.dev/guide/backend-integration)

## Original synthesis — proposed, not tested

Give every setting two declared properties: **who may see it** and **when it is bound**. A public service origin may be safe to disclose yet still be wrong for the deployment. A private server credential must never cross the browser boundary. An environment name is neither a permission check nor evidence of the API that actually receives traffic.

Choose one promotion contract explicitly:

| Contract | Release responsibility | Main trade-off |
|---|---|---|
| Build separately for each target | Record target-specific build inputs and artifact digest | More builds; each output needs verification |
| Promote one immutable bundle plus public runtime configuration | Define a versioned, validated public configuration endpoint/document and load it before dependent requests | Extra availability, caching and compatibility obligations |
| Backend-rendered public configuration | Pair server-rendered settings with compatible asset manifest and safe serialization | Backend coupling; HTML caching and injection risks |

These alternatives are original proposals, not a Vite runtime-config feature. A separate runtime configuration layer needs its own schema, deployment ownership and failure behavior. Do not introduce it merely to avoid a rebuild when the simpler per-target artifact contract is sufficient.

For immutable promotion, record a release identity, artifact digest, public configuration schema version and target identity as separate fields. Validate public values against allowed forms before starting dependent features. If configuration is absent, stale or incompatible, present an explicit unavailable state rather than silently falling back to an unintended service. Choose cache policy deliberately; do not make changing configuration appear to change immutable asset bytes.

Never send the entire server environment to the browser. Allowlist the small set of deliberately public values. Safely encode any server-rendered JSON for its exact HTML context; a JSON serializer alone is not a complete embedding/security design. Keep authentication and resource authorization at the server boundary. Feature flags in client configuration control presentation, not access rights.

A deployment-only environment edit cannot alter constants already embedded in an unchanged static artifact; this is an inference from S1, not a runtime test. Diagnose an unexpected endpoint by checking the delivered artifact and actual network destination, not just the hosting dashboard's current settings. Avoid logging configuration values that were not explicitly classified as public.

## Proposed acceptance checks — all NOT RUN

- Build with a harmless public marker A, serve the unchanged output under environment B, and inspect which marker is delivered.
- Verify string-to-boolean/number parsing against invalid and missing public input.
- Compare configured mode and `NODE_ENV` independently in the build receipt.
- Scan emitted HTML, JavaScript, source maps and copied public assets using synthetic canaries, never real credentials.
- Exercise missing, stale and incompatible runtime configuration without unintended API requests.
- Promote an artifact across target environments; verify actual API destination and authorization independently.
- Keep an older client open through deployment; evaluate old-chunk retention and recovery without discarding unsaved work.

## Compatibility, cost, maintenance and reuse

Vite is a JavaScript/TypeScript frontend build tool; this question concerns its browser output and an optional existing backend. The inspected current documentation mentions Rolldown build options. Exact Vite release, npm artifact, Node requirements, plugin/framework compatibility and source commit dates were not verified and remain unknown. Mutable documentation is not a lockfile: check the actual supported toolchain before implementation. Documentation examples were read, not executed demos. No maintenance cadence is inferred from a live website.

No new MCP connection or paid service is required for the proposed contract. Existing CI, static hosting and optional backend infrastructure remain application-owned. Per-target builds consume CI time/storage; runtime configuration adds a request or HTML generation plus monitoring/cache complexity. No costs or performance savings were measured. Installation, hosting configuration and API integration were not performed.

The actual Vite repository LICENSE is MIT: notice preservation applies to copies/substantial portions and warranty is disclaimed. Public availability does not remove those conditions; this is not an audit of bundled dependencies or third-party assets. [S5](https://github.com/vitejs/vite/blob/main/LICENSE)

Original future application: a deployment receipt panel that compares intended target, delivered artifact identity and observed API origin without displaying secrets. A mismatch should block release acceptance rather than trigger an automatic credential or hosting change.

No browser, build, runtime, deployment, security, accessibility, performance or integration tests were performed. No dependencies, application code, credentials or settings were changed. Documentary approval is not deployment approval.
