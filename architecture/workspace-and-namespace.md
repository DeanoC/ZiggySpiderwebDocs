# Workspace And Namespace

The workspace namespace is the main contract that Spiderweb exposes to workers.

## Core Roots

### `/services`

Project-bound service aliases. Workers should prefer these paths first because they represent the services mounted into the current workspace.

Common built-in bindings include:

- `/services/venom_packages`
- `/services/mounts`
- `/services/home`
- `/services/workers`
- `/services/chat`
- `/services/jobs`
- `/services/events`

Template-specific workspaces may also bind services such as `git`, `github_pr`, `missions`, `pr_review`, `terminal`, `library`, `search_code`, and `web_search`.

### `/nodes`

The physical and logical topology view.

Examples:

- `/nodes/local/...` for host-local surfaces
- `/nodes/<node_id>/fs` for node filesystem exports
- `/nodes/<worker_id>/venoms/*` for worker-owned loopback services

### `/agents`

Attached agent identities and related state. Namespace attaches can automatically ensure an agent exists and seed `/agents/<agent_id>`.

### `/global`

Global discovery aliases and catalog surfaces that are not tied to one project bind path.

### `/projects/<id>/meta`

Project-scoped metadata published as files.

Current metadata includes:

- `topology.json`
- `agents.json`
- `contracts.json`
- `paths.json`
- `nodes.json`
- `summary.json`
- `workspace_status.json`
- `mounts.json`
- `desired_mounts.json`
- `actual_mounts.json`
- `drift.json`
- `reconcile.json`
- `availability.json`
- `health.json`
- `binds.json`
- `mounted_services.json`
- `venom_packages.json`

### `/meta`

Top-level discovery files for attached clients, including `protocol.json` and mirrored workspace discovery snapshots.

## Service Discovery

The live contract is discoverable from the namespace itself.

A practical discovery order is:

1. `/meta/protocol.json`
2. `/projects/<id>/meta/mounted_services.json`
3. `/projects/<id>/meta/workspace_status.json`
4. `/services/<service>/README.md`
5. `/services/<service>/OPS.json`
6. `/services/<service>/SCHEMA.json`
7. `/services/<service>/CAPS.json`

Do not assume a service is mounted because a doc page mentions it. The namespace is authoritative.

## Source Paths Versus Bound Paths

Many services originate from host-local venom paths such as `/nodes/local/venoms/<venom_id>`.

Spiderweb then binds selected ones into `/services/<name>` for the current workspace. This split matters:

- `/nodes/local/venoms/*` tells you where a service originates
- `/services/*` tells you what the current workspace exposes
- `/projects/<id>/meta/binds.json` tells you how those two views are connected

## Routed Workspace Mode Versus Full Namespace Mode

`spiderweb-fs-mount` supports two attachment styles:

- `--workspace-url`: routed `/v2/fs` workspace mount focused on filesystem access
- `--namespace-url`: full Spiderweb attach that exposes `/services`, `/nodes`, `/agents`, `/global`, and project metadata directly

Use full namespace mode when the worker needs the complete discovery surface, not just the routed workspace export.
