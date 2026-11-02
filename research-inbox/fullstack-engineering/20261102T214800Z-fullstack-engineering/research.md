# A generated ID is not a hydration compatibility contract

- Run: `20261102T214800Z-fullstack-engineering`
- Scheduled: 2026-11-02 21:48 UTC; evaluated: 2026-11-02.
- Question: How should repeated server-rendered controls preserve DOM identity through hydration, especially when multiple React roots share a page?
- Scope: documentary engineering reference, not an implementation or certification. Five primary sources; no discovery shortlist.

## Source findings

**S1 — Hydration is an initial-output contract.** `hydrateRoot` connects React to server-rendered HTML and expects the first client output to match. Different input data and browser-only rendering branches can violate that expectation. Attribute differences are not guaranteed to be repaired. `suppressHydrationWarning` is a one-level escape hatch and does not patch mismatched text. `onRecoverableError` can report recoveries; recovery is not evidence of correctness. An intentionally different client view can use a second rendering pass, with additional work and potentially jarring changes. [hydrateRoot](https://react.dev/reference/react-dom/client/hydrateRoot)

**S2 — Generated DOM identity has a narrower purpose.** `useId` supports accessibility relationships and related element IDs. It depends on matching server/client component trees. Independent roots need distinct `identifierPrefix` values; each server-rendered root must use the same prefix when hydrated. The hook is not a list-key or cache-key generator and is currently unavailable in async Server Components. Official examples demonstrate repeated controls and prefixed roots, but were not executed here. [useId](https://react.dev/reference/react/useId)

**S3 — The renderer participates in the agreement.** `renderToPipeableStream` provides Node.js streaming SSR and accepts the prefix that hydration must reuse. Web-stream environments have a different renderer API. A Node streaming example does not itself establish edge-runtime compatibility. [renderToPipeableStream](https://react.dev/reference/react-dom/server/renderToPipeableStream)

**S4 — Simpler rendering has tradeoffs.** `renderToString` returns HTML without streaming or waiting for suspended data; suspended content produces a fallback. It also exposes the prefix option. Choosing this renderer does not eliminate the hydration agreement. [renderToString](https://react.dev/reference/react-dom/server/renderToString)

**S5 — Reuse terms.** React's repository license is MIT: retain its copyright and permission notice when distributing copies or substantial portions; warranty is disclaimed. This does not license arbitrary demo assets or unrelated dependencies. [React license](https://github.com/react/react/blob/main/LICENSE)

## Dot synthesis: an identity agreement, not an ID workaround

Original recommendation, untested: write an explicit agreement for each hydrated island with three owners:
1. **Composition owner:** owns matching initial component structure and initial render inputs.
2. **Root owner:** allocates a unique page-level root namespace and transfers that exact prefix to both rendering endpoints.
3. **Control owner:** derives related DOM IDs together and binds labels/descriptions to the intended control.

Do not solve a tree mismatch by appending randomness. Do not use generated DOM IDs as database identifiers, analytics identities, or durable object references: those are different contracts. A namespace prevents cross-root collisions; it does not validate a control's meaning or authorize access.

For a component library embedded in several independently built widgets, document which layer supplies root options. Avoid adding a second root just to set an option when a framework already owns hydration. Framework adapters and actual dependency versions must be inspected before integration; this research does not establish Next.js-specific configuration.

## Adoption and alternatives

Purpose/technology: React/React DOM provide JavaScript component rendering, browser hydration, and server rendering APIs; TypeScript use requires the project's appropriate type setup. No new library, API key, MCP connection, paid service, or remote memory is proposed. Existing React/React DOM package compatibility and framework constraints remain project checks, not assumed results.

Alternatives: explicitly allocated stable DOM IDs can suit externally coordinated forms, but place uniqueness and parity responsibility on the application. A single React root avoids cross-root namespace coordination but may not fit independent deployments. Client-only rendering avoids SSR hydration altogether, at the cost of changing the delivery architecture. These are design tradeoffs, not measured performance rankings.

Costs/operations: Hydrated SSR requires build/server or prerender infrastructure and client JavaScript delivery. Prefix propagation adds integration ownership; a second client pass adds rendering work. No cost estimate or hosting compatibility was measured. Keep diagnostic events free of form values and user data; sample sanitized root identifiers rather than shipping full HTML.

Maintenance/version evidence: the consulted React documentation displays v19.3. This is a documentation label, not verification of an installed package or latest npm release. Exact release dates, source commits, dependency locks, and deployed versions are unknown. Sources are mutable; recheck against the project's pinned versions before implementation.

## Proposed acceptance exercise — NOT RUN

Build a disposable fixture with two independently hydrated widgets, each rendering repeated labeled controls. Assert unique IDs across the document and that each label/description relationship targets its intended control, then exercise typing/focus. Repeat with delayed JavaScript and different hydration order. Introduce a mismatched prefix and changed initial tree as negative cases; check diagnostic capture without relying on an exact error string or universal recovery behavior. Test the chosen server runtime and framework adapter separately.

Original future application: a design-system integration checklist that records root ownership alongside control semantics, preventing component reviews from overlooking page-level identity. This is a proposed workflow, not a delivered tool.

## Limits and provenance

Dot authored this synthesis from the five linked official sources; no Gemini output, repository code, or protected assets were imported. All eleven earlier full-stack reports were inspected for novelty; this slot had no existing artifacts at intake. Findings are documentary only. Runtime, performance, accessibility, browser, integration, installation, and deployment tests were not run. Publication approval means only the documented review and bounded artifact checks in `qa.md`.

