# Worldfs Single-Websocket Hard Cut

This file records the transport state that is actually implemented now.

## Implemented

### SpiderApp to Spiderweb
- one websocket for normal GUI runtime use
- one dedicated websocket reader thread
- one serialized writer path
- reply dispatch keyed by control `id` and acheron `tag`
- filesystem reads, debug snapshots, chat submit/result reads, and node-service feed reads all share that websocket

### Spiderweb Runtime Handling
- `control` remains the bootstrap/admin plane
- runtime file access stays on `acheron-1`
- attach reports `layout:"worldfs-v1"`
- request paths do not sleep waiting for runtime warmup
- `job_result` reads refresh current data immediately instead of blocking for terminal state

### Spiderweb to SpiderNode
- router uses one websocket per endpoint connection
- replies and invalidations share that same endpoint websocket
- there is no separate router event websocket anymore

## Still True

The namespace itself was not replaced by a new project-root model.
The live contract is still worldfs/rootfs-shaped:
- `/nodes`
- `/agents`
- `/users`
- `/projects`
- `/global`
- `/meta`
- optional `/debug`
- dynamic absolute bind paths when configured

## Removed From The Active Client Contract

- second SpiderApp filesystem websocket
- control node-service watch/unwatch live subscription flow
- blocking runtime warmup wait in the fsrpc request path
- blocking wait in `job_result` file reads

## Control Boundary

Current control operations still used by the implementation:
- version/connect
- session attach/status/resume/list/close/restore/history
- project provisioning and workspace status
- node pairing, lease, service upsert/get, and node inventory
- auth status/rotate, metrics, audit

Current runtime operations use acheron file paths and generic file invalidations.

## Source Files

- `SpiderApp/src/gui/websocket_client.zig`
- `SpiderApp/src/gui/root.zig`
- `Spiderweb/src/server_piai.zig`
- `Spiderweb/src/fsrpc_session.zig`
- `Spiderweb/src/fs_router.zig`
