# Backend Connector (Postgres)

PowerSync write path:
- Client writes local SQLite.
- SDK queues writes.
- SDK calls `uploadData()`.
- Your backend applies writes to Postgres.
- PowerSync Service replicates and streams changes back.

Required methods:
- `uploadData()`: upload queued mutations to backend API.
- `fetchCredentials()`: return `{ token, endpoint }` for PowerSync connection auth.

## Implementation Checklist

1. `fetchCredentials()`:
   - Return a JWT that PowerSync can verify.
   - Return the current PowerSync instance endpoint.
   - Refresh token before expiry.
2. `uploadData()`:
   - Send mutation batches in queue order.
   - Apply server-side validation/authorization.
   - Make writes idempotent for retries.
   - Return deterministic success/failure outcomes.
3. Backend API:
   - Treat mutation payload as untrusted input.
   - Enforce tenant/user scoping server-side.
   - Use transactional boundaries where needed.

## Failure Modes

1. Token refresh drift:
   - Symptoms: periodic reconnect/auth failures.
   - Fix: shorten local cache and fetch earlier.
2. Non-idempotent mutation handlers:
   - Symptoms: duplicate rows or repeated side effects.
   - Fix: idempotency keys or deterministic upsert semantics.
3. Authorization only on client:
   - Symptoms: data leakage or unauthorized writes.
   - Fix: enforce auth checks in backend mutation API.

Docs:
- https://docs.powersync.com/configuration/app-backend/client-side-integration.md
- https://docs.powersync.com/configuration/auth/overview
- https://docs.powersync.com/configuration/auth/custom
