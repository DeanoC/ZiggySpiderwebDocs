# Unified-v2 Control, Agents, And Sessions

This file describes the active control-plane contract used by the current implementation.

## Handshake

Required sequence:
1. `control.version` with `{"protocol":"unified-v2"}`
2. `control.connect`

`control.version_ack` reports control/runtime protocol hints.
`control.connect_ack` reports role, default binding, and attach/bootstrap gating fields.

## Active Session Operations

Implemented session operations:
- `control.session_attach`
- `control.session_status`
- `control.session_resume`
- `control.session_list`
- `control.session_close`
- `control.session_restore`
- `control.session_history`

These remain part of bootstrap/selection flow even though runtime work is file-based over Acheron.

## Active Project Operations

Implemented project/workspace operations:
- `control.project_create`
- `control.project_update`
- `control.project_delete`
- `control.project_list`
- `control.project_get`
- `control.project_mount_set`
- `control.project_mount_remove`
- `control.project_mount_list`
- `control.project_token_rotate`
- `control.project_token_revoke`
- `control.project_activate`
- `control.project_up`
- `control.workspace_status`
- `control.reconcile_status`

## Active Node Operations

Implemented node/admin operations:
- `control.node_invite_create`
- `control.node_join_request`
- `control.node_join_pending_list`
- `control.node_join_approve`
- `control.node_join_deny`
- `control.node_join`
- `control.node_lease_refresh`
- `control.node_service_upsert`
- `control.node_service_get`
- `control.node_list`
- `control.node_get`
- `control.node_delete`

## Other Active Control Operations

- `control.auth_status`
- `control.auth_rotate`
- `control.metrics`
- `control.audit_tail`
- `control.ping`
- `control.pong`

## Removed From The Active Client Contract

These names are no longer part of the protocol and are rejected at parse time:
- `control.debug_subscribe`
- `control.debug_unsubscribe`
- `control.node_service_watch`
- `control.node_service_unwatch`
- `control.node_service_event`

Current replacement:
- read `/debug/stream.log` over Acheron for persisted debug stream snapshots
- read `/global/services/node-service-events.ndjson` over Acheron

## Attach And Runtime Boundary

After control bootstrap/attach, runtime work should move to:
- `acheron.t_version`
- `acheron.t_attach`
- file reads/writes/stats/walks/clunks under the worldfs namespace

## Source Files

- `Spiderweb/src/server_piai.zig`
- `SpiderProtocol` checkout: `Spiderweb/deps/spider-protocol/src/unified_types.zig`
- `SpiderApp/src/client/control_plane.zig`
