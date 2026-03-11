# Workspace Bootstrap Runbook

Spiderweb no longer performs Mother/system bootstrap or provider-backed onboarding internally. Bootstrap is now workspace-first and external-worker-driven.

## Manual Flow

1. Start Spiderweb.
2. Reveal the admin token:

```bash
spiderweb-config auth status --reveal
```

3. Create a workspace:

```bash
spiderweb-control --url ws://127.0.0.1:18790/ --auth-token <admin-token> workspace_create '{"name":"Demo","vision":"Mounted workspace"}'
```

4. Mount the workspace locally:

```bash
spiderweb-fs-mount --workspace-url ws://127.0.0.1:18790/ --auth-token <admin-token> --workspace-id <workspace-id> mount ./workspace
```

5. Start Spider Monkey, or another filesystem-capable worker, against that mounted directory.

## What Spiderweb Owns

- Workspace creation and control-plane state.
- Mounted namespace projection.
- Shared services such as `/services/mounts`, `/services/home`, and `/services/workers`.
- Durable queued jobs for external workers.

## What External Workers Own

- AI provider/model configuration.
- Agent runtime execution.
- Worker-private node venoms such as memory and sub-brains.
- Per-agent homes and worker lifecycle within the mounted namespace.

## Removed Legacy Flow

The old Mother/provider canaries and bootstrap scripts were removed during the Spider Monkey split. If you need equivalent validation now, use:

- `./scripts/acheron-smoke.sh`
- `./scripts/acheron-namespace-smoke.sh`
- a real mounted workspace plus Spider Monkey
