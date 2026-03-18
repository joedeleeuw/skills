---
name: powersync-app-backend-postgres
description: Build and troubleshoot Postgres-focused PowerSync app-backend integration, including backend connector design (`uploadData`, `fetchCredentials`), JWT and endpoint credential flow, Postgres-to-SQLite type mapping, and Sync Rules operator/function constraints. Use when implementing client write upload paths, backend mutation APIs, auth refresh behavior, or type/operator mismatches.
---

# PowerSync App Backend Postgres

## Overview

Use this skill for Postgres-based write-path and connector implementation details. Focus on backend connector contract, safe write handling, type mapping correctness, and SQL/operator limits.

## Start Sequence

1. Refresh docs index:
```bash
curl -fsSL https://docs.powersync.com/llms.txt
```
2. Identify whether the request is about:
   - Connector implementation
   - Type mapping
   - Sync Rules operator/function support
3. Load only the matching reference files.

## Reference Routing

- Docs navigation and page discovery:
  - `references/docs-index.md`
- Backend connector and mutation flow:
  - `references/backend-connector.md`
- Postgres type mapping and conversion caveats:
  - `references/postgres-types.md`
- Supported SQL operators and functions in Sync Rules:
  - `references/sync-rules-operators.md`

For service replication, stream strategy, and Expo runtime choices, use:
- `$powersync-service-streams-expo`

## Workflow

1. Define connector behavior:
   - `fetchCredentials()` returns `{ token, endpoint }`.
   - `uploadData()` calls backend API and applies writes to Postgres.
2. Validate write-path safety:
   - Backend authorization checks.
   - Idempotency for retries.
   - Ordered mutation processing.
3. Validate type behavior:
   - Confirm Postgres column types map correctly to SQLite expectations.
   - Convert `bytea` before syncing to clients.
4. Validate query syntax:
   - Confirm Sync Rules SQL uses supported operators/functions only.
   - Account for parameter filtering restrictions.
5. Return concrete change recommendations plus a short verification checklist.

## Postgres-Only Guardrails

- Prefer `timestamptz` over `timestamp` for unambiguous temporal values.
- Treat `numeric/decimal` as text on clients; avoid assuming float semantics.
- Represent booleans as `1` and `0` client-side.
- Never use raw `bytea` as bucket parameters or direct client sync payload.
- Treat JSON/JSONB as serialized text values and use JSON operators/functions deliberately.

## Response Pattern

1. Describe the exact failure/risk in terms of connector, mapping, or SQL support.
2. Propose minimal code/query changes.
3. Include one validation step for each changed area.
4. Link the specific docs pages used.
