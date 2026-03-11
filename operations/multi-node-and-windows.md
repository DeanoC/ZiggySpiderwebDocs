# Multi-Node And Windows

This page covers the current practical story for remote nodes and Windows clients.

## Adding A Standalone Node

SpiderNode packages the deployable node runtime.

A typical invite-flow launch looks like:

```bash
./zig-out/bin/spiderweb-fs-node \
  --export "work=.:rw" \
  --control-url "ws://<server>:18790/" \
  --control-auth-token "<admin-token>" \
  --pair-mode invite \
  --invite-token "inv-..." \
  --node-name "edge-node"
```

That node can then surface exported filesystems or other services into the hosted namespace.

## Windows Mount Client

Windows is a supported target for the standalone mount client.

You can either build it from the Spiderweb repo:

```powershell
zig build fs-mount
zig build test-fs-mount
```

or use the installer script, which also ensures WinFSP is present:

```powershell
powershell -ExecutionPolicy Bypass -File .\install-fs-mount.ps1
```

Example namespace mount on Windows:

```powershell
spiderweb-fs-mount.exe `
  --namespace-url ws://127.0.0.1:18790/ `
  --project-id <workspace-id> `
  --auth-token <admin-or-user-token> `
  --agent-id codex `
  --session-key main `
  --mount-backend winfsp `
  mount X:
```

## Current Platform Reality

Use this operating rule today:

- host Spiderweb on Linux or WSL
- use Windows for the standalone mount client when that improves the user experience
- treat Windows node support as active expansion work, not the default system baseline

This matches the current external-agent guidance and CI coverage.

## Why This Matters

The project is intentionally moving toward a broader deployment model:

- hosted workspace OS in Spiderweb
- standalone nodes on more than one platform
- filesystem-first workers that can live anywhere

Windows support matters, but it does not change the core architectural split.
