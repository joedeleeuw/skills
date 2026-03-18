# Expo and React Native Integration

This reference covers client-side integration choices for Expo-based apps using PowerSync.

## Adapter Choice

Production/dev builds (recommended):
- Use native adapters (`@powersync/op-sqlite` preferred, or quick-sqlite).

Expo Go sandbox:
- Use `@powersync/adapter-sql-js` only for sandbox development.
- Expect slower performance and weaker consistency guarantees vs native adapters.

## Integration Flow

1. Install `@powersync/react-native` and adapter dependencies.
2. Define client schema matching synced data.
3. Instantiate `PowerSyncDatabase` with chosen adapter/open factory.
4. Implement backend connector (`fetchCredentials`, `uploadData`).
   - Use `$powersync-app-backend-postgres` for detailed connector contract and write-path hardening.
5. Call `connect()` and monitor sync status/watch queries.

## Stream-Specific Notes

If using Sync Streams:
1. Use `db.syncStream(name, params).subscribe(...)` for on-demand data.
2. Track sync progress and call `waitForFirstSync()` where needed.
3. Unsubscribe when inactive; use TTL intentionally.

## Expo Go Caveats

- SQL.js adapter rewrites full database file per write.
- Potential data corruption/missing data if process dies during write.
- Use native adapter for performance and reliability before production.

## Practical Guidance

1. Keep one database instance per filename.
2. Keep schema aligned with Sync Rules/Streams output.
3. Use Expo Go only for quick UI/dev loops, not performance validation.
4. Validate reconnect/auth cycle and write upload path in a development build.

Docs:
- https://docs.powersync.com/client-sdks/reference/react-native-and-expo.md
- https://docs.powersync.com/client-sdks/frameworks/expo-go-support.md
- https://docs.powersync.com/configuration/app-backend/client-side-integration.md
