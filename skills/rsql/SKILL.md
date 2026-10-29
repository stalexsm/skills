---
name: rsql
description: Query databases and data files via the rsql CLI. Use when the task needs SQL against PostgreSQL, MySQL, MariaDB, SQLite, DuckDB, ClickHouse, SQL Server, Snowflake, CockroachDB, LibSQL/Turso, or when the task needs SQL over CSV, TSV, Parquet, JSON, JSONL, Excel, ODS, XML, YAML, Arrow, Avro files, including files on S3 or HTTP — exploring schemas, running one-shot queries, exporting or converting data. Load when the user says SQL, database, БД, запрос к базе, rsql, or asks to inspect or transform tabular data.
---

# rsql — SQL over any database or file

`rsql` is a single CLI that speaks SQL to 25+ data sources. For agents its value is the one-shot mode: connect, run, print machine-readable output, exit — no psql/mysql/duckdb client zoo, no per-database syntax for meta-commands.

## The one-shot pattern

Every query follows one shape:

```bash
rsql --url "<connection-url>" --format json --footer false --timer false -- "<SQL>"
```

- `--format json` (or `jsonl`, `csv`, `tsv`) — machine-readable stdout. `markdown` for human-facing reports.
- `--footer false --timer false` — the **clean-output flags**. Without them rsql appends a `2 rows (3ms)` line to stdout and breaks JSON parsing. Never omit them in scripts.
- `--` separates rsql flags from SQL. Multiple commands after `--` run in sequence; dot-commands work there too.

Completion criterion: the SQL ran and the output parsed clean. Anything else — treat as failure and re-run.

## Error checking is mandatory

rsql exits 0 even when SQL fails. The only failure signal is text on stdout/stderr:

```
Error: DriverError(IoError("error returned from database: (code: 1) no such table: nonexistent"))
```

After every call, scan output for a line starting with `Error:` before trusting results. Never use the exit code as a success signal.

## Discovering schema

Dot-commands go in the same `--` slot as SQL:

```bash
rsql --url "$URL" --footer false --timer false -- ".tables"          # list tables
rsql --url "$URL" --footer false --timer false -- ".describe users"  # columns, types, PK/FK, indexes
rsql --url "$URL" --footer false --timer false -- ".indexes users"   # indexes for a table
rsql --url "$URL" --footer false --timer false -- ".schemas"         # schemas
```

`.describe` reports primary/foreign keys with an "Inferred" column — relations guessed from naming conventions (`user_id` → `users`) are marked, not declared. Verify inferred facts against real DDL before relying on them.

Workflow for an unfamiliar database: `.tables` → `.describe` on the relevant tables → query with `SELECT ... LIMIT` (`--limit 100` also caps rows).

## Connection URLs

`--url` (or `DATABASE_URL` env var) selects the driver:

| Source | URL |
|---|---|
| PostgreSQL | `postgresql://user:pass@host:5432/db` |
| MySQL / MariaDB | `mysql://user:pass@host/db` |
| SQLite | `sqlite://path/to/db.sqlite` |
| DuckDB | `duckdb://` (memory) or `duckdb://file.duckdb` |
| ClickHouse | `clickhouse://user:pass@host/db` |
| SQL Server | `sqlserver://user:pass@host/db` |
| Turso | `libsql://?auth_token=...` |
| Snowflake | `snowflake://user:pass@org.snowflakecomputing.com/db` |
| CSV / TSV | `csv://data.csv` |
| Parquet | `parquet://data.parquet` |
| JSON / JSONL | `json://data.json`, `jsonl://data.jsonl` |
| Excel / ODS | `excel://book.xlsx` |
| S3 object | `s3://key:secret@host/bucket/object?region=...` |
| HTTP(S) file | `https://example.com/data.parquet` |
| Embedded Postgres | `postgresql://?embedded=true` |

Files queried through Polars-based drivers (csv, json, parquet, excel) can quietly return strings with extra quoting or type surprises — when output looks mangled, re-check against a real database driver or inspect raw values.

## Credentials

Connection strings carry passwords in argv, visible in `ps` output. Prefer `DATABASE_URL` in the environment over `--url` on the command line, and never paste discovered credentials into logs or report text.

## Scripts and bulk work

```bash
rsql --url "$URL" --format csv --footer false --timer false -f script.sql > out.csv
```

For data conversion, point the source at one format and export as another:

```bash
rsql --url "csv://input.csv" --format parquet --footer false --timer false -- "SELECT * FROM input" > out.parquet
```

Set `DISABLE_UPDATE_CHECK=1` in the environment so rsql skips its network update check on every invocation.

## Format cheat sheet

| Goal | Format |
|---|---|
| Parse in a script | `json`, `jsonl`, `csv`, `tsv` |
| Show a human | `markdown`, `psql`, `unicode` |
| Wide rows, one per line | `expanded` |
| Wide export | `csv`, `tsv`, `jsonl` (fastest) |

Available always: `--header true|false`, `--rows true|false`, `--limit N`, `--color never`.
