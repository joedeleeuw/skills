# Postgres Source Replication Setup for PowerSync

Baseline prerequisites:
1. Postgres 11+.
2. Logical replication enabled.
3. Dedicated replication role/user with required privileges.
4. Publication named `powersync`.

## Core Setup Pattern

1. Enable logical replication (host/provider-specific).
2. Create replication-capable role:
   - Include replication privilege requirements for your provider.
   - Grant `SELECT` access on required tables.
3. Create publication:
   - `CREATE PUBLICATION powersync ...`
   - Prefer explicit table lists for high-volume systems.

## Critical Operational Warning

If publication includes too many tables:
- PowerSync must read all updates in the publication, even if not referenced in current sync config.
- This can increase memory usage and replication lag.

Use scoped publication tables for large deployments instead of `FOR ALL TABLES`.

## Security and Access Notes

1. Restrict DB network access as required.
2. Avoid over-privileged replication roles.
3. Apply least privilege for table access and future default grants.

Docs:
- https://docs.powersync.com/configuration/source-db/setup.md
- https://docs.powersync.com/configuration/source-db/security-and-ip-filtering
