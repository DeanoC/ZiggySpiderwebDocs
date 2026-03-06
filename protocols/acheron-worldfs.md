# Acheron WorldFS

This is the current runtime contract implemented by `fsrpc_session`.

## Version And Attach

Runtime negotiation:
1. `acheron.t_version` with `version:"acheron-1"`
2. `acheron.t_attach`

`acheron.r_attach` currently returns:
- `qid`
- `layout:"worldfs-v1"`
- `project_id` or `null`
- `roots`
- `dynamic_bind_paths`
- `bind_count`

`/meta/protocol.json` currently reports:
- `channel:"acheron"`
- `version:"acheron-1"`
- `layout:"worldfs-v1"`

## Root Layout

The attached root is seeded with:
- `/nodes`
- `/agents`
- `/users`
- `/projects`
- `/global`
- `/meta`
- optional `/debug`

Dynamic project bind paths may also appear directly at `/`.

## Canonical Runtime Paths

Use these paths as the current contract:
- `/global/chat/control/input`
- `/global/jobs/<job_id>/status.json`
- `/global/jobs/<job_id>/result.txt`
- `/global/events/control/wait.json`
- `/global/events/next.json`
- `/global/events/sources/*`
- `/global/services/SERVICES.json`
- `/global/services/node-service-events.ndjson`
- `/projects/<project_id>/fs`
- `/projects/<project_id>/nodes`
- `/projects/<project_id>/agents`
- `/projects/<project_id>/meta`
- `/meta/protocol.json`
- `/meta/workspace_status.json`
- `/meta/workspace_availability.json`
- `/meta/workspace_health.json`

When debug is visible by policy:
- `/debug/stream.log`

## Invalidations

Runtime push traffic is limited to generic filesystem invalidations:
- `acheron.e_fs_inval`
- `acheron.e_fs_inval_dir`

Clients should treat file contents as authoritative and use invalidations to decide when to reread.

## Notes

- The retained node-service feed replaces the old control watch/event contract.
- The implementation still contains compatibility aliases in some places, but the canonical runtime contract is the path-based one above.

## Source Files

- `Spiderweb/src/fsrpc_session.zig`
- `Spiderweb/src/fs_control_plane.zig`
