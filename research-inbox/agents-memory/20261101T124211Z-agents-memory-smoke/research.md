# Project labels are not authorization: scoped recall for MCP memory services

Run: `20261101T124211Z-agents-memory-smoke` • Evaluated: 2026-11-01 UTC

**Question:** What must enforce project boundaries when an agent requests stored memory through MCP?

**Answer — original synthesis:** Treat `project_id` as an object selector, not proof of permission. Authenticate the caller, authorize the requested operation against server-owned membership, and enforce the resulting scope at every memory access path. A relevance filter improves retrieval; it does not by itself stop a caller from changing that filter.

## Source-backed findings

1. **Transport authentication is not project authorization.** The MCP 2025-11-25 authorization specification describes HTTP transport authorization; authorization is optional in MCP generally. Where supported, tokens must be supplied on every HTTP request and validated for the server's intended audience. Invalid or expired tokens receive 401. Stdio follows a different credential model; this HTTP flow is not a universal local-process sandbox. [MCP authorization](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization)
2. **Check actions on objects, not just login.** OWASP identifies object-ID manipulation as a cause of unauthorized API access, including when identifiers are UUIDs. Its guidance requires object-level permission checks at endpoints that act on object IDs. Comparing a session user ID with an input ID is not a general substitute for the application's actual ownership or membership rules. [OWASP API1:2023](https://api-security.owasp.org/editions/2023/en/0xa1-broken-object-level-authorization/)
3. **Database enforcement has explicit exceptions.** PostgreSQL 17 row-level security can govern rows read and modified. With RLS enabled and no applicable policy, normal row access defaults to denial. Owners normally bypass RLS; superusers and `BYPASSRLS` roles always bypass it. `FORCE ROW LEVEL SECURITY` subjects owners to policies, but does not remove superuser powers. Whole-table operations such as TRUNCATE are outside RLS. [PostgreSQL 17 RLS](https://www.postgresql.org/docs/17/ddl-rowsecurity.html)
4. **A session handle is not an identity credential.** MCP security guidance says not to authenticate using session IDs, and to verify inbound requests when authorization is implemented. It also rejects token passthrough and recommends binding session-related data to authenticated user information. [MCP security guidance](https://modelcontextprotocol.io/docs/2025-11-25/tutorials/security/security_best_practices)

## Proposed implementation pattern — not executed

Use three independent decisions:

- **Who?** Establish an authenticated principal using the transport's supported verifier. Do not trust `agent_id` supplied in tool arguments as identity.
- **May do what, where?** Resolve current project membership and operation permission server-side. Reject an unauthorized requested project rather than silently widening it.
- **Which records?** Apply that authorized scope to ID lookup, semantic search, listing, mutation, exports, cached results and handoff retrieval before returning content.

Original illustrative pseudocode, not a runnable SDK example:

```text
principal = authenticate(request)
project = parse_required_project(request)
require_permission(principal, project, requested_operation)
results = memory_store.search(scope=authorized_scope(principal, project), query=query)
return permitted_result_projection(results)
```

If PostgreSQL backs the store, evaluate RLS as defense in depth, with a non-owner runtime role lacking bypass and administrative permissions. Never expose arbitrary SQL or a caller-controlled identity-setting channel. This is a design recommendation, not a verified deployment recipe.

## Bounded test contract — all runtime checks NOT RUN

| Synthetic case | Required observation |
|---|---|
| Valid caller, authorized project | Save and recall succeed with matching evidence references |
| Valid caller, other project | Read, search, write and handoff denied independently |
| Change project in payload, path or nested filter | Authorization cannot be bypassed |
| Missing/invalid token; session ID alone | No memory content returned |
| Cached search after membership revocation | No stale authorization grants access |
| Runtime database identity | No table-owner, bypass-RLS or administrative path |

Common debugging trap: a search test returning no hits may reflect relevance, not isolation. Seed known synthetic records and also attempt exact-ID retrieval and mutations. Another trap is testing a privileged database connection and assuming it represents the runtime role. Compare identities explicitly.

## Limits and adoption decision

This is documentary analysis, not an integration, security certification or benchmark. MCP references target 2025-11-25; PostgreSQL guidance targets major 17, not every database or latest release. Documentation release/commit timestamps were not independently established. Public source pages were read on the evaluation date. No software was installed, no backend connected, and no real records inspected. These controls do not prevent separately privileged administrators from accessing underlying storage. Next step: evaluate the proposed test contract in an explicitly authorized disposable environment before selecting a memory service.
