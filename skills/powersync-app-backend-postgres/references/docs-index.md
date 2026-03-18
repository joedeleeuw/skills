# PowerSync Docs Index

Primary index:
- https://docs.powersync.com/llms.txt

Refresh and search:

```bash
curl -fsSL https://docs.powersync.com/llms.txt
curl -fsSL https://docs.powersync.com/llms.txt | rg -n "app-backend|sync/types|supported-sql|react-native-and-expo"
```

Pages used by this skill:
- Client-side backend integration: https://docs.powersync.com/configuration/app-backend/client-side-integration.md
- Writing client changes: https://docs.powersync.com/handling-writes/writing-client-changes.md
- Sync types (Postgres mapping): https://docs.powersync.com/sync/types.md
- Parameter queries: https://docs.powersync.com/sync/rules/parameter-queries.md
- Data queries: https://docs.powersync.com/sync/rules/data-queries.md
- Supported SQL for Sync Rules: https://docs.powersync.com/sync/rules/supported-sql.md

Out-of-scope pointer:
- For service replication, stream strategy, and Expo runtime architecture, use `$powersync-service-streams-expo`.
