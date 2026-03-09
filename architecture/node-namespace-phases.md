# Node Namespace Status (Current)

This document replaces the older phase plan with the features that are actually implemented today in Spiderweb.

## Implemented Capabilities

- **Venom catalog projection**
  - Control-plane Venom metadata is projected into `/nodes/<node_id>/venoms/*`.
  - Venom descriptors include `mounts`, `ops`, `runtime`, `permissions`, `schema`, and optional `help_md`.

- **Venom manifest support (node side)**
  - Node runtimes can publish Venom manifests and have them upserted into the catalog.

- **Permission filtering**
  - Non-admin sessions only see node Venoms allowed by `PERMISSIONS.json`.
  - Admin sessions bypass Venom visibility filtering.

- **Invoke-aware mount gating**
  - Workspace mounts derived from invoke-capable Venoms are omitted when policy disallows `invoke`.

- **Node Venom event stream**
  - `control.venom_watch` + `control.venom_event` for live catalog updates.
  - Optional replay and on-disk event history via `node-venom-events.ndjson`.

## Non-Goals (Not Implemented Here)

The following are outside this repo’s scope or not implemented in Spiderweb itself:

- GUI-specific watch tooling
- Client CLI commands
- Node runtime supervision UX

## Implementation Pointers

- Control catalog + projection: `deps/spider-protocol/src/spiderweb_node/venom_catalog.zig`, `src/acheron/session.zig`
- Control operations: `src/server_piai.zig`
- Node Venom events: `src/server_piai.zig`
