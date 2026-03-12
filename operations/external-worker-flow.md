# External Worker Flow

Spiderweb is designed so a worker can operate through the mounted filesystem alone.

## Choose The Attachment Mode

### Routed Workspace Mode

Use `--workspace-url` when you mainly need the routed workspace filesystem.

Example:

```bash
./zig-out/bin/spiderweb-fs-mount \
  --workspace-url ws://127.0.0.1:18790/ \
  --workspace-id <workspace-id> \
  --auth-token <token> \
  mount /mnt/spiderweb
```

### Full Namespace Mode

Use `--namespace-url` when the worker needs the full Spiderweb namespace, including `/services`, `/nodes`, `/agents`, `/global`, and workspace metadata.

Example:

```bash
./zig-out/bin/spiderweb-fs-mount \
  --namespace-url ws://127.0.0.1:18790/ \
  --workspace-id <workspace-id> \
  --auth-token <token> \
  --agent-id codex \
  --session-key main \
  mount /mnt/spiderweb
```

Namespace attach can automatically ensure the agent identity and seed `/agents/<agent_id>`.

## First Files A Worker Should Read

The worker should discover the live environment in this order:

1. `/meta/protocol.json`
2. `/projects/<workspace_id>/meta/mounted_services.json`
3. `/projects/<workspace_id>/meta/workspace_status.json`
4. `/services/<service>/README.md`
5. `/services/<service>/OPS.json`
6. `/services/<service>/SCHEMA.json`
7. `/services/<service>/CAPS.json`
8. `/agents/<agent_id>/`

This is more reliable than assuming a service exists because of a document or template name.

## Worker Expectations

Any external worker should assume:

- the namespace is the source of truth
- `/services/*` is the preferred shared capability surface
- `/projects/<workspace_id>/meta/*` describes the live topology and health
- worker-private surfaces may appear under `/nodes/<worker_id>/venoms/*`
- service contracts are defined by files, not out-of-band SDK assumptions

## What Spiderweb Does Not Require

Spiderweb does not require a Spider-specific AI runtime binary.

If the worker can:

- read files
- write files
- list directories

it can interact with the mounted workspace.

SpiderProtocol SDKs are still useful when you want direct protocol clients instead of a filesystem mount, but the mounted-workspace model is the main architecture.
