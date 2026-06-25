---
name: dbx-mcp
description: Use when the user asks to use DBX MCP, DBX, database connections, inspect schemas or tables, write or execute SQL, open DBX UI results, or move structured data into a database through DBX across MySQL, PostgreSQL, SQLite, DuckDB, ClickHouse, SQL Server, MongoDB, and other DBX-supported databases.
---

# DBX MCP

## Overview

Use DBX MCP as the default database workspace when database access is requested and `mcp__dbx` tools are available. Keep the workflow source-backed: discover connections, inspect schema, then query or mutate with explicit checks.

Do not store credentials, database-specific secrets, or one project's connection details in this skill.

## Workflow

1. **Find or create a connection**
   - Start with `mcp__dbx.dbx_list_connections` unless the user directly provides all connection fields.
   - Add a connection with `mcp__dbx.dbx_add_connection` when the user provides host, port, database type, database name, username, and password if needed.
   - Use clear connection names such as `local_mysql_app`, `prod_readonly_pg`, or the user's requested name.

2. **Inspect before querying**
   - Use `mcp__dbx.dbx_list_tables` to discover tables and views.
   - Use `mcp__dbx.dbx_get_schema_context` for SQL-writing context across several tables.
   - Use `mcp__dbx.dbx_describe_table` for exact columns on a single table.

3. **Read safely**
   - Prefer bounded reads: include `LIMIT`, date filters, or targeted IDs.
   - Use `mcp__dbx.dbx_execute_query` when results should come back to Codex.
   - Use `mcp__dbx.dbx_execute_and_show` when the user wants results in the DBX desktop UI.
   - Summarize important results in chat because the user may not see raw tool output.

4. **Write carefully**
   - For inserts or updates, inspect the target schema first and check for duplicates or existing rows.
   - Use transactions when the execution path supports them, especially for multi-table writes.
   - Prefer soft-delete conventions already present in the schema instead of hard deletes.
   - Before destructive operations (`DELETE`, `DROP`, broad `UPDATE`, truncation), get explicit user confirmation unless the user has already given a precise destructive command.

5. **Verify after writes**
   - Re-query affected rows, counts, and integrity conditions.
   - For choice/lookup-style data, verify required children exist and labels are non-empty.
   - Report IDs, counts, and any assumptions made.

## Tool Map

| Task | Tool |
| --- | --- |
| List configured connections | `mcp__dbx.dbx_list_connections` |
| Add a connection | `mcp__dbx.dbx_add_connection` |
| Remove a connection | `mcp__dbx.dbx_remove_connection` |
| List tables/views | `mcp__dbx.dbx_list_tables` |
| Describe one table | `mcp__dbx.dbx_describe_table` |
| Get compact schema for SQL | `mcp__dbx.dbx_get_schema_context` |
| Execute SQL and return rows | `mcp__dbx.dbx_execute_query` |
| Execute SQL in DBX UI | `mcp__dbx.dbx_execute_and_show` |
| Open a table in DBX UI | `mcp__dbx.dbx_open_table` |

## Common Requests

| User asks | Do |
| --- | --- |
| "Connect local MySQL" | Add/list connection, then verify with `dbx_list_tables`. |
| "Show this table schema" | Use `dbx_describe_table`; include database/schema when known. |
| "Help me write SQL" | Get schema context first, then draft SQL; execute only if asked. |
| "Run this SQL" | Check risk; run with `dbx_execute_query`; summarize output. |
| "Open results in DBX" | Use `dbx_execute_and_show` or `dbx_open_table`; DBX desktop must be running. |
| "Build database rows from a document or spreadsheet" | Extract source data, inspect schema, insert in batches/transactions, then verify counts and sample rows. |

## Failure Handling

- If DBX tools are unavailable, search for a DBX/plugin capability before falling back.
- If authentication fails, ask only for the missing connection fields; do not guess credentials.
- If a query errors, read the error literally, inspect schema again, then revise.
- If a write partially fails, stop, report what is known, and verify database state before retrying.
