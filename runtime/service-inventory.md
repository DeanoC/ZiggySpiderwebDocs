# Venom Inventory and Usage Model (Current)

This document summarizes the Venoms that are currently implemented and how agents should discover and invoke them via the Acheron namespace.

## Discovery Order

1. `/global/venoms/VENOMS.json` (agent namespace index)
2. `/nodes/<node_id>/venoms/VENOMS.json` (node-scoped catalog, if advertised)
3. Inspect Venom contract files before invoking:
   - `README.md`, `SCHEMA.json`, `TEMPLATE.json`, `HOST.json`, `CAPS.json`, `OPS.json`, `PERMISSIONS.json`, `STATUS.json`

## Agent Namespace Venoms (Implemented)

These Venoms are implemented by Spiderweb itself under `/global`:

- `chat` (input under `/global/chat/control/input`)
- `jobs` (status/result under `/global/jobs/<job_id>/*`)
- `events` (wait/next under `/global/events/*`)
- `memory` (control + result/status)
- `web_search` (control + result/status)
- `search_code` (control + result/status)
- `terminal` (terminal-v2 session model)
- `mounts` (project mounts/binds + local workspace folder create)
- `sub_brains` (sub-brain list/upsert/delete)
- `agents` (agent list/create)
- `missions` (persistent mission lifecycle + service bridge)
- `pr_review` (thin PR Review use-case veneer layered over `missions`)

Each Venom exposes `control/*.json` endpoints plus `status.json` and `result.json` for invocation results.

## Node Venoms (Catalog-Driven)

Node Venoms are advertised through the control-plane catalog (`control.venom_*`) and projected into:

- `/nodes/<node_id>/venoms/<venom_id>`
- `/nodes/<node_id>/<resource>` (compat view derived from Venom mounts)

If a node advertises an empty Venom list, no fallback resources are exposed for that node.

## Invoke Contract

1. Read `OPS.json` for the `invoke` target (or `paths.invoke`).
2. Read `HOST.json` to understand runtime contract and supervision behavior.
3. If needed, seed a payload from `TEMPLATE.json`.
4. Write the payload to the invoke path.
5. Read `status.json` and `result.json` until completion.

Event waits can be driven through `/global/events/control/wait.json` + `/global/events/next.json`.

## Implementation Pointers

- Namespace and Venom projection: `src/acheron/session.zig`
- Node Venom catalog: `deps/spider-protocol/src/spiderweb_node/venom_catalog.zig`
- Project policy gating: `src/acheron/control_plane.zig`, `src/server_piai.zig`
