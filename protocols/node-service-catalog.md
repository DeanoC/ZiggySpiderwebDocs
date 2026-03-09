# Node Venom Catalog Spec

The Venom catalog describes node-exported namespace Venoms and their mount metadata. It is the control-plane source used to project `/nodes/<node_id>/venoms/*` and dynamic mount roots in the Acheron namespace.

## Control Operations

- `control.venom_upsert`
- `control.venom_get`
- `control.venom_watch`
- `control.venom_unwatch`
- `control.venom_event` (server push)

## `control.venom_upsert`

### Request fields

- `node_id` (`string`, required)
- `node_secret` (`string`, required)
- `platform` (`object`, optional)
  - `os` (`string`, optional)
  - `arch` (`string`, optional)
  - `runtime_kind` (`string`, optional)
- `labels` (`object<string,string>`, optional)
- `venoms` (`array`, optional)

Each Venom entry:

- `venom_id` (`string`, required)
- `kind` (`string`, required)
- `version` (`string`, default `"1"`)
- `state` (`string`, required)
- `endpoints` (`array<string>`, required, absolute paths)
- `capabilities` (`object`, optional, default `{}`)
- `mounts` (`array<object>`, optional, default `[]`)
  - `mount_id` (`string`, required)
  - `mount_path` (`string`, required, absolute path)
  - `state` (`string`, optional; defaults to Venom state)
- `ops` (`object`, optional, default `{}`)
  - `invoke` (`string`, optional)
  - `paths.invoke` (`string`, optional)
- `runtime` (`object`, optional, default `{}`)
- `permissions` (`object`, optional, default `{}`)
- `schema` (`object`, optional, default `{}`)
- `help_md` (`string`, optional)

### Example

```json
{
  "node_id": "node-2",
  "node_secret": "secret-...",
  "platform": { "os": "linux", "arch": "amd64", "runtime_kind": "native" },
  "labels": { "site": "hq-west", "tier": "edge" },
  "venoms": [
    {
      "venom_id": "camera",
      "kind": "camera",
      "version": "1",
      "state": "online",
      "endpoints": ["/nodes/node-2/camera"],
      "capabilities": { "still": true },
      "mounts": [
        {
          "mount_id": "camera",
          "mount_path": "/nodes/node-2/camera",
          "state": "online"
        }
      ],
      "ops": { "model": "namespace", "style": "plan9" },
      "runtime": { "type": "native_proc", "abi": "namespace-driver-v1" },
      "permissions": { "default": "deny-by-default" },
      "schema": { "model": "namespace-mount" },
      "help_md": "Camera namespace driver"
    }
  ]
}
```

## `control.venom_get`

Request fields:
- `node_id` (`string`, required)

Response fields:
- `node_id`
- `node_name`
- `platform`
- `labels`
- `venoms`

## `control.venom_watch`

Subscribes to catalog events. Server responds with `control.venom_event` frames for upserts and changes.

## Validation Notes

- Venom IDs and kinds are identifier-safe strings.
- Venom IDs must be unique within a single upsert payload.
- `endpoints` must be absolute-style paths.
- `capabilities` must be a JSON object.
- `mounts`, when present, must use absolute `mount_path` values.
- `ops`, `runtime`, `permissions`, and `schema` must be JSON objects.
- `ops.invoke` / `ops.paths.invoke`, when provided, override the default `control/invoke.json` resolve path.

## Namespace Permission Projection

`/nodes/<node_id>/venoms/*` visibility for non-admin sessions evaluates Venom `permissions` metadata:

- `allow_roles` (`array<string>`, optional)
- `default` (`string`, optional)
- `require_project_token` / `project_token_required` (`bool`, optional)

Admin sessions bypass Venom permission filtering.

## Workspace Mount Gating

`control.workspace_status` applies invoke-policy checks to mount projection for non-admin actors:

- If a mount maps to an invoke-capable Venom, it is omitted unless
  - project access policy allows `invoke`
  - Venom `permissions` allow the actor

## `spiderweb-fs-node` Provider Mapping

When `spiderweb-fs-node` runs in control daemon mode (`--control-url`), it auto-upserts Venom metadata:

- FS provider (enabled by default):
  - `venom_id`: `fs`
  - `kind`: `fs`
  - endpoint: `/nodes/<node_id>/fs`
  - capabilities: `rw`, `export_count`
  - mounts: `/nodes/<node_id>/fs`
- Terminal provider (`--terminal-id <id>`):
  - `venom_id`: `terminal-<id>`
  - `kind`: `terminal`
  - endpoint: `/nodes/<node_id>/terminal/<id>`
  - capabilities: `pty=true`, `terminal_id`, `invoke=true`
  - ops: `invoke=control/invoke.json` (`paths.exec` alias)
  - runtime: `native_proc` (`entry=internal-terminal-invoke`)
  - mounts: `/nodes/<node_id>/terminal/<id>`
- Extra namespace Venoms (from `--venom-manifest` / `--venoms-dir`):
  - appended after built-in providers
  - validated for shape and duplicates before publish

Use `--no-fs-venom` to disable FS Venom advertisement and `--label <key=value>` to attach node labels.

Implementation pointers:
- `deps/spider-protocol/src/spiderweb_node/fs_node_service.zig`
- `deps/spider-protocol/src/spiderweb_node/venom_catalog.zig`
- `src/server_piai.zig`
