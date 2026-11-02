# A TypeScript shape is not a runtime input contract

Run: `20261102T154800Z-fullstack-engineering`  
Scheduled: 2026-11-02 15:48 UTC. Retrieved and evaluated: 2026-11-02.

## Question and novelty

Where should a TypeScript application validate externally supplied configuration or API data, and why do `satisfies`, assertions and excess-property checks not replace that boundary?

The live catalog, full-stack directory and nine earlier full-stack reports were read before selection. No same-run directory or unfinished full-stack package was found. Earlier reports address cache freshness, optimistic updates, pools, migrations, request ordering, build-time configuration, retries, streams and pagination. This note addresses a separate input-contract boundary. It is not a tool shortlist or an audit of application code.

## Source findings

- **Author-time compatibility:** TypeScript 4.9 introduced `satisfies`. It checks compatibility with a target type while retaining useful expression-specific information instead of replacing it with a broad annotation. Its documented examples catch wrong keys on fresh object literals. This is a compiler feature, not a runtime parser. [S1](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-9.html)
- **Assertions do not inspect values:** Type assertions and non-null assertions are removed during compilation and add no runtime check. A wrong assertion does not itself produce a validation exception. `any` disables relevant checking and propagates through operations. [S2](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- **Checks can support narrowing:** JavaScript conditionals, `typeof`, discriminants and user-defined type predicates allow the checker to narrow types. The runtime work is done by the actual condition or function body, not by the predicate's return-type spelling. [S3](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
- **Exactness is contextual:** Object literals receive excess-property checks; moving a value through another variable can change those checks. Structural assignability is not a universal exact-key policy, nor does checking erase extra runtime fields. [S4](https://www.typescriptlang.org/docs/handbook/2/objects.html)
- **Output is a separate gate:** `noEmitOnError` controls whether TypeScript emits output when errors are reported and defaults to false. An emitted JavaScript file alone therefore does not establish a passing type-check. This option does not describe every framework's build pipeline. [S5](https://www.typescriptlang.org/tsconfig/noEmitOnError.html)

## Original synthesis: three contracts, not one — untested

Separate (1) authored source compatibility, (2) runtime shape acceptance and (3) permission/business validity. A payload can pass a shape parser yet name a resource the caller cannot edit. A server-generated static object can satisfy a type while its later network replacement has completely different values.

Use `satisfies` for checked source-owned tables and defaults. For data crossing a network, persisted-file or user-input boundary, place it into an `unknown`-typed local variable and pass it to an explicit runtime parser before application use. That annotation restricts downstream operations; it does not validate the received value. Avoid converting the input to the target interface with `as`, double assertions, or `any` just to silence diagnostics.

The parser should return a fresh minimal domain object or a structured failure. Decide explicitly whether unknown keys are rejected, stripped or retained. Do not spread the original input into a write model after validating only selected properties. Define finite-number/range constraints, string lengths, optional-versus-null policy, nested collection bounds and discriminant combinations. These are proposed application rules, not properties automatically supplied by a TypeScript interface.

A small original control-flow contract (pseudocode, not tested code):

```text
raw: unknown = receiveBoundedInput()
parsed = parseEditInput(raw)
if parsed.failed: return safeValidationFailure(parsed.fieldCodes)
principal = authenticateRequest()
authorized = authorizeEdit(principal, parsed.value.recordId)
if not authorized: return forbiddenWithoutPrivateDetails()
return commitEdit(parsed.value, principal)
```

The order of authentication and parsing is application-specific; enforce request-size/resource limits before expensive parsing, and never let this sketch substitute for a complete authentication design. Treat parsing and authorization as distinct gates even if one library combines their plumbing. Return safe field codes instead of echoing complete potentially sensitive payloads into logs or client errors.

For schema evolution, attach an explicit input version where incompatible wire formats coexist. Transform accepted old versions into one current internal representation and test both rejection and transformation. Static client types cannot force an already-deployed older client to send the new representation.

## Choosing an implementation

For a tiny stable shape, a handwritten parser avoids a new dependency but makes completeness and maintenance the team's responsibility. A runtime schema library can centralize parsing and generated/inferred types, but its coercion, defaulting and unknown-key behavior must be reviewed and tested. A schema-driven API generator can align clients and servers, yet generated declarations alone still do not execute validation. No library is recommended or installed by this report.

TypeScript's purpose is static JavaScript tooling; the examined syntax requires a compatible compiler/transpiler (at least TypeScript 4.9 for `satisfies`). The documentation includes interactive examples, but none was executed here. Pin the application's compiler and confirm a dedicated type-check actually runs, that failure blocks promotion, and which tool emits the shipped bundle. Runtime parsing belongs wherever untrusted values enter the trusted domain, including server endpoints even when the client also checks.

No hosting, API key or MCP connection is needed for this documentary pattern. Implementation costs are local/CI checking time, parser CPU/allocation, payload limits and maintenance; none was measured. The historical TypeScript v4.9.5 source licence is Apache-2.0, with notice/licence and redistribution conditions, not unrestricted reuse. That pinned licence is not a licence determination for every website page, dependency or future release. [S6](https://raw.githubusercontent.com/microsoft/TypeScript/v4.9.5/LICENSE.txt)

## Proposed acceptance fixture — all NOT RUN

1. Fresh authored defaults with a misspelled key fail the intended type-check; verify the gate's exit result, not just emitted files.
2. A remote value with a wrong scalar, null, missing nested field or unsupported discriminant is rejected before a mutation.
3. Extra properties follow the documented reject/strip/retain policy and cannot leak into persistence through a later spread.
4. An intentionally incorrect handwritten predicate is caught by negative runtime fixtures; its annotation is not treated as proof.
5. Oversized collections and numeric edge cases obey explicit resource/domain limits.
6. A well-shaped unauthorized record ID still fails authorization without leaking private details.
7. Old-version payloads either transform through a tested path or fail with an explicit version error.

Future application: generate a review checklist linking each public endpoint's wire schema, runtime parser, authorization rule and negative fixture. This is an original proposal, not an implemented integration.

## Limitations and provenance

Six primary sources were evaluated. The release-note version is 4.9 and the licence is pinned to v4.9.5; neither identifies the current installed or latest release. Handbook pages are mutable, exact release/commit dates were not established and remain null in metadata. No repositories, assets or substantial source text were copied. No compiler, application, browser, runtime validation, integration, security, accessibility or performance test was run. Approval means independent documentary review only, not deployment permission or a security certification.
