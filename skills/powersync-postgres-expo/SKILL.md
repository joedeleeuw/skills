---
name: powersync-postgres-expo
description: "PowerSync with Postgres: backend connector design, type mapping, Sync Rules, service replication, Sync Streams, and Expo/React Native client integration. Use when implementing write paths (uploadData, fetchCredentials, mutations), read paths (replication, buckets, streams), or choosing Expo adapter strategy."
---

# PowerSync

## Overview

Full-stack PowerSync guidance for Postgres-backed apps. Covers the write path (client → your backend → Postgres), the read path (Postgres → PowerSync Service → client), and Expo/React Native integration.

## Start Sequence

1. Refresh docs index:
```bash
curl -fsSL https://docs.powersync.com/llms.txt
```
2. Classify the request:
   - **Write path**: connector implementation, mutations, type mapping, Sync Rules SQL
   - **Read path**: service replication, bucket behavior, Sync Streams
   - **Client**: Expo/React Native adapter choices, SDK integration
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
- Service internals and bucket lifecycle:
  - `references/powersync-service-replication.md`
- Sync Streams strategy and subscription patterns:
  - `references/sync-streams-strategy.md`
- Source Postgres replication prerequisites:
  - `references/postgres-source-replication-setup.md`
- Expo and React Native integration:
  - `references/expo-react-native-integration.md`

## Non-Negotiable Invariants

- PowerSync Service is the read path from source DB to clients.
- Client writes must flow through your app backend API, never directly to PowerSync.
- Sync Rules are stable/GA; Sync Streams are early alpha.
- Expo Go adapter (`@powersync/adapter-sql-js`) is for sandbox dev only.

## Write Path Workflow

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

## Read Path Workflow

1. Validate source DB replication setup and publication scope.
2. Choose sync model (Rules vs Streams) based on release risk and data access pattern.
3. Define global vs scoped data strategy (auto-subscribe vs on-demand subscription).
4. Choose Expo adapter path (Expo Go SQL.js vs native adapters in dev/prod builds).

## Postgres Guardrails

- Prefer `timestamptz` over `timestamp` for unambiguous temporal values.
- Treat `numeric/decimal` as text on clients; avoid assuming float semantics.
- Represent booleans as `1` and `0` client-side.
- Never use raw `bytea` as bucket parameters or direct client sync payload.
- Treat JSON/JSONB as serialized text values.

## Response Pattern

1. State whether issue is write path, read path, or client integration.
2. Propose minimal code/config changes.
3. Include one validation step per changed area.
4. Link the specific docs pages used.
