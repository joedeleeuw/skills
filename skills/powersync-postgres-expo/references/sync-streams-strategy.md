# Sync Streams Strategy

Status (as documented on February 12, 2026):
- Sync Streams are early alpha.
- Sync Rules are stable/GA.

## When to Use Sync Streams

Prefer Sync Streams when:
1. You need on-demand data per screen/tab/feature.
2. You need repeated subscriptions with different parameter sets.
3. You want TTL-based warm-cache behavior after unsubscribe.
4. You can accept early-alpha risk.

Prefer Sync Rules when:
1. You need stable production behavior today.
2. You need explicit bucket definitions and mature operational characteristics.
3. You want "sync upfront" offline-first by default.

## Stream Definition Model

Streams include:
- `query`
- `auto_subscribe` (optional)
- `priority` (optional)
- `accept_potentially_dangerous_queries` (optional)

Key differences from Sync Rules:
- Streams use a single query instead of separate parameter/data queries.
- Parameters are accessed through:
  - `subscription.parameter(...)`
  - `connection.parameter(...)`
  - `auth.parameter(...)` and `auth.user_id()`
- Limited subquery patterns are supported in streams.

## Global-like Data in Streams

For data every user should receive:
- Use stream definitions with `auto_subscribe: true`.

For on-demand scoped data:
- Use explicit `syncStream(...).subscribe(...)` with subscription parameters.

## Risk Checklist

1. Confirm SDK/service version minimums for streams.
2. Confirm Rust client requirement on older SDK versions.
3. Confirm streams config includes correct `edition` setting.
4. Confirm stream query complexity stays within supported limits.
5. Confirm subscriptions are cleaned up and TTL expectations are explicit.

Docs:
- https://docs.powersync.com/sync/overview.md
- https://docs.powersync.com/sync/streams/overview.md
