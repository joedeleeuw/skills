# Sync Rules Operators and Functions

## Base Query Limits (Sync Rules)

Supported SQL is intentionally limited:
1. `SELECT` only.
2. No `JOIN`, no `GROUP BY`/aggregations, no `ORDER BY`, no `LIMIT`.
3. No subqueries in Sync Rules.
4. Limited operators/functions when filtering on parameters.

Note:
- Sync Streams can use a limited subquery pattern and different parameter access syntax.

## Operators

Supported:
- Comparison: `=`, `!=`, `<`, `>`, `<=`, `>=`
- Null checks: `IS NULL`, `IS NOT NULL`
- Math: `+`, `-`, `*`, `/`
- Logical: `AND`, `OR`, `NOT`
- Casting: `CAST(x AS type)`, `x :: type`
- JSON extraction: `->`, `->>`
- Text concat: `||`
- Array membership: `<left> IN <right>` (JSON-array semantics)

## Functions

Common supported functions:
- Text: `upper`, `lower`, `substring`
- Encoding/size/type: `hex`, `base64`, `length`, `typeof`
- JSON: `json_each`, `json_extract`, `json_array_length`, `json_valid`, `json_keys`
- Null/branching: `ifnull`, `iif`
- UUID/date-time: `uuid_blob`, `unixepoch`, `datetime`
- PostGIS helpers: `ST_AsGeoJSON`, `ST_AsText`, `ST_X`, `ST_Y`

## Parameter Filtering Constraints

When filtering on token/client/bucket parameters:
- Keep predicates simple.
- `=` is the safest default.
- `IN` and null checks are supported with constraints.
- Complex boolean combinations can be restricted.

When transforming output columns or filtering normal row values:
- Operator restrictions are less strict.

## Postgres-Specific Tips

1. Convert `bytea` using `hex()` or `base64()` before syncing.
2. Convert geometry as needed with `ST_AsGeoJSON` or `ST_AsText`.
3. For JSONB fields, use JSON operators on text representations consistently.

Docs:
- https://docs.powersync.com/sync/rules/supported-sql.md
- https://docs.powersync.com/sync/rules/parameter-queries.md
- https://docs.powersync.com/sync/rules/data-queries.md
- https://docs.powersync.com/sync/streams/overview.md
