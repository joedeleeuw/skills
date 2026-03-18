# PowerSync Service Replication and Streaming

PowerSync Service responsibilities:
1. Replicate data from source DB using Sync Rules or Sync Streams definitions.
2. Stream bucket operations to clients.

## Replication Lifecycle

Initial replication:
- On new Sync Rules/Streams deployment, snapshot referenced tables/collections.

Incremental replication:
- Continue via CDC mechanism (Postgres logical replication, MongoDB change streams, etc.).

Processing path:
1. Pre-process source changes according to sync config.
2. Partition into buckets (explicit in Rules, implicit in Streams).
3. Persist operation history in bucket storage.
4. Serve incremental operations to authenticated clients.

## Bucket Notes

- Bucket ID combines definition name and parameter values.
- Buckets deduplicate shared data across users.
- Bucket operation history is append-oriented and compacted over time.

## Streaming Notes

After auth:
1. Determine relevant buckets.
2. Stream operations since client checkpoint.
3. Keep monitoring for membership and operation updates.

Important:
- Streaming uses bucket storage state, not direct source DB queries.

## Troubleshooting Checklist

1. Validate source replication health and publication/table coverage.
2. Validate bucket IDs expected for current auth/parameters.
3. Validate operation history growth and compaction behavior.
4. Validate checkpoint progression on clients.
5. Validate sync config version rollout state.

Docs:
- https://docs.powersync.com/architecture/powersync-service.md
- https://docs.powersync.com/maintenance-ops/compacting-buckets.md
