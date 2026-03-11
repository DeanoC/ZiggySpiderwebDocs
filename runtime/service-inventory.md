# Venom Inventory and Usage Model (Current)

This document summarizes the Venoms that are currently implemented and how agents should discover and invoke them via the Acheron namespace.

## Discovery Order

1. `/projects/<project_id>/meta/mounted_services.json` (effective workspace service index)
2. `/services/*` paths bound into the current workspace by the active project template and later mount/bind changes
3. `/global/venoms/VENOMS.json` (compatibility discovery index for local aliases)
4. `/nodes/<node_id>/venoms/VENOMS.json` (node-scoped catalog, if advertised)
5. Inspect Venom contract files before invoking:
   - `README.md`, `SCHEMA.json`, `TEMPLATE.json`, `HOST.json`, `CAPS.json`, `OPS.json`, `PERMISSIONS.json`, `STATUS.json`

Spiderweb now publishes its built-in local venoms canonically under `/nodes/local/venoms/*`. `/global/*` remains a compatibility alias, but project-facing agent workflows should prefer `/services/*` when those binds are present.

## Agent Namespace Venoms (Implemented)

These Venoms are implemented by Spiderweb itself under `/nodes/local/venoms/*`, with `/global/*` retained as a compatibility alias:

- `chat` (input under `/global/chat/control/input`)
- `jobs` (status/result under `/global/jobs/<job_id>/*`)
- `events` (wait/next under `/global/events/*`)
- `memory` (control + result/status)
- `web_search` (control + result/status)
- `search_code` (control + result/status)
- `terminal` (terminal-v2 session model)
- `git` (repo checkout sync, status, and diff-range inspection)
- `github_pr` (provider PR sync, event intake, and top-level review publication)
- `mounts` (project mounts/binds + local workspace folder create)
- `sub_brains` (sub-brain list/upsert/delete)
- `agents` (agent list/create)
- `missions` (persistent mission lifecycle + service bridge)
- `pr_review` (thin PR Review use-case veneer layered over `missions`, exposing `intake`, `start`, `sync`, `run_validation`, `record_validation`, `draft_review`, and `record_review`, with orchestration routed through `git`, `github_pr`, `terminal`, and Spider Monkey runtime steps)

`github_pr` now also exposes `ingest_event`, which normalizes provider PR events, emits them through `/global/events/sources/agent/github_pr.json`, and auto-creates or reuses the matching `pr_review` mission.

Each Venom exposes `control/*.json` endpoints plus `status.json` and `result.json` for invocation results.

## Workspace Templates and Bound Services

Projects now carry a `template_id` that seeds their initial workspace service binds:

- `minimum`: binds `/services/mounts -> /nodes/local/venoms/mounts`
- `github`: binds `/services/mounts`, `/services/git`, `/services/github_pr`, `/services/missions`, `/services/pr_review`, `/services/terminal`, `/services/events`, `/services/library`, `/services/memory`, `/services/search_code`, and `/services/web_search`

The effective bound surface for a project is emitted through:

- `/projects/<project_id>/meta/binds.json`
- `/projects/<project_id>/meta/mounted_services.json`
- `/meta/workspace_binds.json`
- `/meta/workspace_services.json`

Agents should use those files to discover what is actually available in the current workspace instead of assuming every local venom is always mounted.

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
