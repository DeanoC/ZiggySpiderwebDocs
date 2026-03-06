# Spiderweb Overview

Spiderweb is a unified-v2 control gateway plus an `acheron-1` worldfs runtime.

Current model:
- Bootstrap and admin actions happen over `control` on the main websocket.
- Attached runtime work happens over `acheron` as filesystem operations.
- The attached layout is `worldfs-v1`, not a `/workspace` root.
- SpiderApp uses one websocket to Spiderweb during normal GUI runtime use.
- Spiderweb uses one websocket per node endpoint in the router path.

## Runtime Shape

After `control.version` and `control.connect`, clients attach with:
- `acheron.t_version`
- `acheron.t_attach`

The attached `/` is seeded from `fsrpc_session` with:
- `/nodes`
- `/agents`
- `/users`
- `/projects`
- `/global`
- `/meta`
- optional `/debug`

Project bind paths are live too. Absolute paths such as `/src` or `/repo` can appear at `/` when control-plane binds are active.

## Canonical Paths In Use Now

Runtime paths used by the current GUI/CLI/server:
- chat input: `/global/chat/control/input`
- job status: `/global/jobs/<job_id>/status.json`
- job result: `/global/jobs/<job_id>/result.txt`
- wait config: `/global/events/control/wait.json`
- next event: `/global/events/next.json`
- service index: `/global/services/SERVICES.json`
- retained node-service feed: `/global/services/node-service-events.ndjson`
- protocol metadata: `/meta/protocol.json`
- workspace status: `/meta/workspace_status.json`
- workspace availability: `/meta/workspace_availability.json`
- workspace health: `/meta/workspace_health.json`
- debug stream snapshot: `/debug/stream.log` when debug is visible

Project views remain under `/projects/<project_id>/{fs,nodes,agents,meta}`.

## What Changed In The Hard Cut

Implemented now:
- no second SpiderApp filesystem websocket
- no separate Spiderweb router event websocket for node endpoints
- no request-path waiting for runtime warmup before replying
- no blocking wait in `job_result` reads
- `control.node_service_watch`, `control.node_service_unwatch`, and `control.node_service_event` are no longer recognized by SpiderProtocol or Spiderweb

Current replacement for node-service monitoring:
- read `/global/services/node-service-events.ndjson`
- use retained snapshots plus normal file polling/invalidation behavior

## Implementation Pointers

- websocket gateway: `Spiderweb/src/server_piai.zig`
- attached worldfs session: `Spiderweb/src/fsrpc_session.zig`
- node router: `Spiderweb/src/fs_router.zig`
- control-plane retained feeds: `Spiderweb/src/fs_control_plane.zig`
- GUI transport: `SpiderApp/src/gui/websocket_client.zig`
- GUI fs/chat runtime calls: `SpiderApp/src/gui/root.zig`
