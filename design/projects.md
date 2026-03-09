# Projects (Current Model)

Projects are the unit of mount composition and access policy in Spiderweb. They are managed by the control plane and exposed through control APIs plus the canonical Acheron namespace.

## Project Fields (Control Payload)

`control.project_*` responses include:

- `project_id`
- `name`
- `vision`
- `status`
- `kind` (for example `system_builtin` or `user`)
- `is_delete_protected`
- `token_locked`
- `created_at_ms`
- `updated_at_ms`
- `project_token` (admin only / when requested)
- `access_policy`

## Workspace & Mounts

A project’s effective namespace is built from mounts that map node exports into canonical paths. The control plane exposes:

- `control.project_mount_set`
- `control.project_mount_remove`
- `control.project_mount_list`
- `control.workspace_status`

Mounted resources become visible under canonical namespace roots such as:

- `/nodes/<node_id>/...`
- `/agents/<agent_id>`
- `/global/projects`

`control.workspace_status` and project metadata files provide `workspace_status`, `mounts`, `desired_mounts`, `actual_mounts`, `drift`, and availability/health summaries.

## Tokens & Access Policy

Projects can require a token for specific actions. The access policy supports action-scoped checks:

- `read`
- `observe`
- `invoke`
- `mount`
- `admin`

Admin tokens bypass project token checks. User tokens must satisfy the project’s policy and any service-level permissions.

## Implementation Pointers

- Control plane: `src/control_plane.zig`
- Control gateway: `src/server_piai.zig`
- Acheron namespace projection: `src/acheron/session.zig`
