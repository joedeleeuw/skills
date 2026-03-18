---
name: powersync-service-streams-expo
description: "PowerSync Service architecture and operational guidance for replication, bucket behavior, Sync Streams adoption, and Expo/React Native client integration. Use when designing or troubleshooting source-db replication setup, stream subscription strategy, stream-vs-rules decisions, or Expo adapter/runtime choices."
---

# PowerSync Service Streams Expo

## Overview

Use this skill for service-side read-path design and client-side Expo sync strategy. Focus on replication mechanics, bucket/stream strategy, and practical Expo adapter tradeoffs.

## Start Sequence

1. Refresh docs index:
```bash
curl -fsSL https://docs.powersync.com/llms.txt
```
2. Classify the request into one or more domains:
   - Service replication and bucket behavior
   - Sync Streams strategy and subscriptions
   - Expo/React Native integration choices

## Reference Routing

- Navigation:
  - `references/docs-index.md`
- Service internals and bucket lifecycle:
  - `references/powersync-service-replication.md`
- Sync Streams strategy and subscription patterns:
  - `references/sync-streams-strategy.md`
- Source Postgres replication prerequisites:
  - `references/postgres-source-replication-setup.md`
- Expo and React Native integration:
  - `references/expo-react-native-integration.md`

## Non-Negotiable Invariants

- Sync Rules are stable/GA; Sync Streams are early alpha (until docs status changes).
- PowerSync Service is the read path from source DB to clients.
- Client writes must still flow through your app backend API.
- Expo Go adapter (`@powersync/adapter-sql-js`) is for sandbox development and has performance/consistency limitations.

## Workflow

1. Validate source DB replication setup and publication scope.
2. Choose sync model (Rules vs Streams) based on release risk and data access pattern.
3. Define global vs scoped data strategy (auto-subscribe vs on-demand subscription).
4. Choose Expo adapter path (Expo Go SQL.js vs native adapters in dev/prod builds).
5. Return concrete config/query/code changes and a verification checklist.

## Response Pattern

1. State whether issue is replication, stream strategy, or Expo integration.
2. Provide minimal change set first.
3. Include at least one risk and one validation step.
4. Link exact docs pages used.
