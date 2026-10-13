# Backend-Specific Execution

Read only the section matching the selected connection type. The authorization, connection selection, policy, and uncertain-write rules in `SKILL.md` also apply here.

## Redis

- Use `dbx_execute_redis_command`, not SQL execution. Pass the selected `connection_id` and use its `db` argument for the logical database rather than sending `SELECT`.
- Prefer bounded commands on known keys. For key discovery, use `SCAN` and follow the returned cursor until completion; do not substitute `KEYS` or treat one page as a complete inventory.
- Inspect the actual command result and MCP policy denial. Do not assume SQL sessions, `use_transaction`, or SQL rollback apply to Redis writes. Destructive commands such as `FLUSH*` require the authorized target/scope and sufficient DBX policy.

## MongoDB

- Use `dbx_list_tables` to discover collections and `dbx_execute_query` for supported MongoDB shell commands. Consult the supported parser/error output instead of assuming arbitrary JavaScript or a full interactive shell is available.
- Scope reads with a filter and limit. MongoDB command results are capped at 100 rows; SQL `max_rows` does not expand them. Use deterministic, bounded pages and explicit count commands when completeness is required.
- Updates/deletes need a verifiably effective filter unless Full access allows the broader operation. An all-document filter such as `_id: {$exists: true}` is still high risk. Aggregations containing `$out` or `$merge` are writes and require write authorization/policy.
- Do not promise SQL-style session transactions or rollback for these commands.

## Message Queues

- Use `dbx_list_tables` to discover the available topics/queue objects before selecting the user's target when it is unknown.
- `dbx_peek_messages` supports **Kafka only**, reads without committing consumer offsets, and is not a continuous subscription. The tool may be absent when the build lacks MQ support or policy scopes it out.
- Kafka peek defaults to the latest 20 messages. `count` accepts 1-100; `start_position` is `latest`, `earliest`, or `offset`. Supply a non-negative `offset` only for offset mode; an optional non-negative `partition` narrows the read.
- Inspect both `incomplete` (partial broker read) and `outputTruncated` (whole-message omissions under the 256 KiB output budget). Payloads include base64 plus UTF-8 previews; do not equate the preview with the complete payload or the returned sample with the entire topic.
- Use `dbx_send_message` only for an authorized publication to the selected Kafka, RocketMQ, or RabbitMQ target. Encode the intended bytes as standard `payload_base64`; if `payload_text` is supplied, keep it consistent with those bytes. Consult the tool schema for keys, partitions, headers, and RabbitMQ exchange/routing/namespace fields.
- Publishing a message is a write with possible downstream effects. On an ambiguous send result, do not resend automatically and risk duplicate delivery; preserve the uncertainty and inspect available delivery evidence.

## Salesforce

Salesforce uses stateless REST requests. It supports SOQL reads through `dbx_execute_query` and object discovery through `dbx_list_tables`; SQL sessions, `dbx_execute_batch`, and SQL transactions are refused. Omit `database` unless DBX policy explicitly requires it.

For a write to one record:

1. Inspect the object's fields and call `dbx_salesforce_current_user`. Show the connected identity/org and the reported `Modify All Data` flag so the user can identify the account responsible for the change.
2. Call `dbx_salesforce_prepare_write` with `connection_id`, the object API name, and `op` (`insert`, `update`, or `delete`). Insert/update need a non-empty `fields` map without `Id`; update/delete need the separate record `id`. For insert, omit `id`; for delete, omit `fields`.
3. Preparation validates policy and returns a concrete summary plus a `confirm_token`; it does not send the write. Show that summary and wait for user approval before applying it, as required by the tool workflow.
4. Call `dbx_salesforce_apply_write` with that token to apply exactly the approved write. Tokens are single-use and expire after 5 minutes. An expired token requires new preparation; a changed write requires new preparation and approval of the changed summary.
5. Inspect Salesforce's result/per-record errors and re-query the record through SOQL when applicable. An applied write cannot be rolled back with SQL transaction tools. On an uncertain outcome, inspect state before any retry.

Preparing a write requires the connection's **Allow DML** switch in DBX Settings -> MCP, which is off by default. A policy/permission error is not evidence of a bad record ID and does not authorize enabling that setting.
