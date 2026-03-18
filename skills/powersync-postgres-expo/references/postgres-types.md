# Postgres Type Mapping for PowerSync

PowerSync Sync Rules and Sync Streams use SQLite types:
- `null`
- `integer` (64-bit signed)
- `real` (64-bit float)
- `text` (UTF-8)
- `blob` (binary)

## Common Postgres to SQLite Mappings

| Postgres | PowerSync/SQLite | Notes |
| --- | --- | --- |
| `text`, `varchar` | `text` | |
| `int2`, `int4`, `int8` | `integer` | |
| `numeric`, `decimal` | `text` | Preserve arbitrary precision as string |
| `bool` | `integer` | `1=true`, `0=false` |
| `float4`, `float8` | `real` | |
| `uuid`, `enum` | `text` | |
| `timestamptz` | `text` | Format `YYYY-MM-DD hh:mm:ss.sssZ` |
| `timestamp` | `text` | Format `YYYY-MM-DD hh:mm:ss.sss` |
| `date`, `time` | `text` | |
| `json`, `jsonb` | `text` | Operate with JSON functions/operators |
| `bytea` | `blob` | Convert to hex/base64 before syncing to clients |
| `geometry` (PostGIS) | `text` | Hex/WKB representation by default |
| Arrays | `text` | JSON array encoding |

Additional type families:
- `DOMAIN` types: `text` or mapped via compatibility behavior.
- Custom types: `text` (or compatibility-driven behavior).
- Ranges/multiranges: `text` with compatibility-dependent representation.

## Practical Guardrails

1. Prefer `timestamptz` for cross-timezone correctness.
2. Treat `numeric`/`decimal` as strings client-side unless explicitly converted.
3. Do not sync raw `bytea` to client tables; convert in query first.
4. Do not use binary values as bucket parameters.
5. Handle booleans as integer semantics in SQLite (`0`/`1`).

## JSON Behavior

- `json`/`jsonb` are stored as text representations.
- JSON operators/functions work on that text.
- Keep schemas and query expressions explicit to avoid type confusion.

Docs:
- https://docs.powersync.com/sync/types.md
- https://docs.powersync.com/sync/rules/supported-sql.md
