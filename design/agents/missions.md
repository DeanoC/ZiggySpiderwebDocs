# Missions (Current)

Spiderweb now exposes a first-class mission venom backed by a persistent mission store. Workspace-facing agents should prefer `/services/missions` when the active project binds it; `/nodes/local/venoms/missions` is the canonical local origin, and `/global/missions` is the compatibility alias.

## Access Paths

Use mission control files instead of ad-hoc runtime state:

- `/services/missions/control/create.json`
- `/services/missions/control/list.json`
- `/services/missions/control/get.json`
- `/services/missions/control/heartbeat.json`
- `/services/missions/control/checkpoint.json`
- `/services/missions/control/bootstrap_contract.json`
- `/services/missions/control/invoke_service.json`
- `/services/missions/control/recover.json`
- `/services/missions/control/request_approval.json`
- `/services/missions/control/approve.json`
- `/services/missions/control/reject.json`
- `/services/missions/control/resume.json`
- `/services/missions/control/block.json`
- `/services/missions/control/complete.json`
- `/services/missions/control/fail.json`
- `/services/missions/control/cancel.json`

Results and status:

- `/services/missions/result.json`
- `/services/missions/status.json`

`bootstrap_contract.json` materializes JSON-backed contract files and artifact directories under the canonical local workspace mount (`/nodes/local/fs/...`) using Spiderweb's configured `local_fs_export_root`.

`invoke_service.json` is the bridge from mission steps to workspace-mounted service venoms. Provide:

- `mission_id`
- `service_path` for the venom root, for example `/services/git` or `/services/github_pr`
- either a raw `payload` / `request` body or an `op` plus `arguments`
- optional `invoke_path` when the discovered service exposes a non-default invoke target

Spiderweb resolves the service invoke path, writes through the live Acheron venom interface, then records the request, result, status, artifact, and mission event uniformly.

## Mission Record Shape

Each mission record persists:

- `mission_id`, `use_case`, `title`, `stage`, and `state`
- bound `agent_id`, `project_id`, `run_id`, `workspace_root`, and `worktree_name`
- an optional generic `contract` bundle:
  - `contract_id`
  - `context_path`
  - `state_path`
  - `artifact_root`
- `created_by`, `created_at_ms`, `updated_at_ms`, and `last_heartbeat_ms`
- `checkpoint_seq`, `recovery_count`, `recovery_reason`, `blocked_reason`, and `summary`
- recent `artifacts`, recent `events`, and optional `pending_approval`

The mission kernel stays generic. Use-case-specific state does not get new hard-coded Spiderweb fields; it lives in workspace or service-managed files referenced by the mission `contract`.

Service-bound mission steps emit:

- a `mission.service_invoked` event with service path, invoke path, request, result, status, artifact, and actor
- a mission artifact entry, defaulting to the service `result.json` path unless overridden

`create`, `checkpoint`, `bootstrap_contract`, `invoke_service`, and state-transition writes may all update the mission `contract` bundle so long-running use cases can move or promote their workspace-backed state files without changing the kernel schema.

Current states:

- `planning`
- `running`
- `waiting_for_approval`
- `blocked`
- `recovering`
- `completed`
- `failed`
- `cancelled`

## Approval Model

Missions may request approval for high-impact actions by writing `request_approval.json`. That moves the mission to `waiting_for_approval` and records the requested action plus optional payload.

Resolution is admin-gated at the Acheron layer:

- non-admin sessions may request approval
- only admin sessions may write `approve.json` or `reject.json`

Approval resolution emits mission events and returns the mission to `running` on approval or `blocked` on rejection.

## Persistence

The shared mission store lives alongside other Spiderweb LTM state in `runtime_config.ltm_directory` as `missions.json` when LTM persistence is configured.

This store is shared across Acheron sessions through the runtime registry, so mission state survives session rebinds and process restarts.

## Implementation Pointers

- Mission persistence and state machine: `src/mission_store.zig`
- Acheron namespace bridge: `src/acheron/session.zig`
- Runtime registry wiring: `src/server_piai.zig`
