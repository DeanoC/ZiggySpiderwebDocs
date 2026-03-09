# Missions (Current)

Spiderweb now exposes a first-class `/global/missions` namespace backed by a persistent mission store.

## Access Paths

Use mission control files instead of ad-hoc runtime state:

- `/global/missions/control/create.json`
- `/global/missions/control/list.json`
- `/global/missions/control/get.json`
- `/global/missions/control/heartbeat.json`
- `/global/missions/control/checkpoint.json`
- `/global/missions/control/invoke_service.json`
- `/global/missions/control/recover.json`
- `/global/missions/control/request_approval.json`
- `/global/missions/control/approve.json`
- `/global/missions/control/reject.json`
- `/global/missions/control/resume.json`
- `/global/missions/control/block.json`
- `/global/missions/control/complete.json`
- `/global/missions/control/fail.json`
- `/global/missions/control/cancel.json`

Results and status:

- `/global/missions/result.json`
- `/global/missions/status.json`

`invoke_service.json` is the bridge from mission steps to workspace-mounted service venoms. Provide:

- `mission_id`
- `service_path` for the venom root, for example `/global/memory`
- either a raw `payload` / `request` body or an `op` plus `arguments`
- optional `invoke_path` when the discovered service exposes a non-default invoke target

Spiderweb resolves the service invoke path, writes through the live Acheron venom interface, then records the request, result, status, artifact, and mission event uniformly.

## Mission Record Shape

Each mission record persists:

- `mission_id`, `use_case`, `title`, `stage`, and `state`
- bound `agent_id`, `project_id`, `run_id`, `workspace_root`, and `worktree_name`
- `created_by`, `created_at_ms`, `updated_at_ms`, and `last_heartbeat_ms`
- `checkpoint_seq`, `recovery_count`, `recovery_reason`, `blocked_reason`, and `summary`
- recent `artifacts`, recent `events`, and optional `pending_approval`

Service-bound mission steps emit:

- a `mission.service_invoked` event with service path, invoke path, request, result, status, artifact, and actor
- a mission artifact entry, defaulting to the service `result.json` path unless overridden

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
