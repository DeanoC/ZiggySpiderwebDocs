# Acheron Namespace

The Acheron namespace is the agent-visible filesystem exposed over the `acheron-1` protocol. The layout below reflects the current runtime implementation.

## Root Layout

```
/
├── agents/
│   └── <agent_id>/
│       ├── README.md
│       └── LINK.txt (for visible peer agents)
├── nodes/
│   └── <node_id>/
│       ├── venoms/<venom_id>/...
│       └── <resource_roots...>
├── global/
│   ├── chat/
│   ├── jobs/<job_id>/{status.json,result.txt,log.txt}
│   ├── events/{control/wait.json,next.json,sources/*}
│   ├── memory/...
│   ├── web_search/...
│   ├── search_code/...
│   ├── terminal/...
│   ├── mounts/...
│   ├── sub_brains/...
│   ├── agents/...
│   ├── projects/...
│   ├── venoms/VENOMS.json
│   └── library/...
└── debug/ (only when enabled by policy)
```

`acheron.t_attach` returns `layout="unified-v2-fs"` plus the canonical roots exposed to the session.

## Agent Namespace Venoms

Venom discovery starts at:

- `/global/venoms/VENOMS.json`

Each Venom directory contains a contract set (`README.md`, `SCHEMA.json`, `CAPS.json`, `OPS.json`, `PERMISSIONS.json`, `STATUS.json`) plus `control/*.json`, `status.json`, and `result.json` for invocation.

Implemented built-in Venoms:
- `memory`
- `web_search`
- `search_code`
- `terminal` (terminal-v2)
- `mounts`
- `sub_brains`
- `agents`

## Jobs and Events

- Chat input path: `/global/chat/control/input`
- Job status path: `/global/jobs/<job_id>/status.json`
- Job result path: `/global/jobs/<job_id>/result.txt`

Event wait flow:

1. Write a selector to `/global/events/control/wait.json`.
2. Read `/global/events/next.json` for the next matching event.

Supported event sources include:
- `/global/chat/control/input`
- `/global/jobs/<job_id>/status.json`
- `/global/events/sources/time/after/<ms>.json`
- `/global/events/sources/time/at/<unix_ms>.json`
- `/global/events/sources/agent/<parameter>.json`
- `/global/events/sources/hook/<parameter>.json`
- `/global/events/sources/user/<parameter>.json`

## Node Venoms

Node Venoms are projected from the control-plane catalog into:

- `/nodes/<node_id>/venoms/<venom_id>`
- `/nodes/<node_id>/<resource>` (compat view derived from Venom mounts)

If a node advertises an explicit empty catalog, no fallback resources are exposed for that node.

## Project Control

Project lifecycle and topology are managed through:

- `/global/projects/control/list.json`
- `/global/projects/control/get.json`
- `/global/projects/control/up.json`
- `/global/projects/control/invoke.json`

Project state such as `workspace_status`, `desired_mounts`, `actual_mounts`, `drift`, and reconcile health is surfaced through the project-control APIs rather than a public `/projects/<project_id>` root.

## Debug Pairing

When debug is enabled by policy (typically for `mother`), `/debug/` exposes pairing queue controls and invite workflows.

## Implementation Pointers

- Namespace session: `src/acheron/session.zig`
- Policy defaults: `src/workspaces/policy.zig`
- Control-plane topology: `src/acheron/control_plane.zig`
