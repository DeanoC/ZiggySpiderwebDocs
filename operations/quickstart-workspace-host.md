# Quickstart: Workspace Host

This is the current baseline flow for bringing up a usable Spider workspace.

Assumption: Spiderweb runs on Linux or WSL.

## 1. Build Spiderweb

```bash
git clone --recurse-submodules https://github.com/DeanoC/Spiderweb.git
cd Spiderweb
zig build
```

## 2. Reset Or Inspect Local Auth

```bash
./zig-out/bin/spiderweb-config auth reset --yes
./zig-out/bin/spiderweb-config auth status --reveal
```

Save the revealed admin token. You will use it for workspace creation and the initial mount.

## 3. Start Spiderweb

```bash
./zig-out/bin/spiderweb
```

## 4. Create A Workspace

In another shell:

```bash
./zig-out/bin/spiderweb-control \
  --auth-token <admin-token> \
  workspace_create \
  '{"name":"Demo","vision":"Mounted workspace demo"}'
```

Capture the returned workspace ID.

## 5. Mount The Workspace

```bash
./zig-out/bin/spiderweb-fs-mount \
  --workspace-url ws://127.0.0.1:18790/ \
  --workspace-id <workspace-id> \
  --auth-token <admin-or-user-token> \
  mount /mnt/spiderweb-demo
```

Optional sanity check without creating an OS mount:

```bash
./zig-out/bin/spiderweb-fs-mount \
  --workspace-url ws://127.0.0.1:18790/ \
  --workspace-id <workspace-id> \
  --auth-token <admin-or-user-token> \
  readdir /
```

## 6. Start SpiderMonkey

Build SpiderMonkey:

```bash
git clone https://github.com/DeanoC/SpiderMonkey.git
cd SpiderMonkey
zig build
```

Run it against the mounted workspace:

```bash
./zig-out/bin/spider-monkey \
  run \
  --agent-id spider-monkey \
  --worker-id spider-monkey-a \
  --workspace-root /mnt/spiderweb-demo
```

## 7. What Happens Next

Once SpiderMonkey starts successfully, it will typically:

- claim an agent home through `/services/home`
- register itself through `/services/workers`
- project worker-private `memory` and `sub_brains` surfaces under `/nodes/<worker_id>/venoms/*`
- begin consuming jobs and workspace state through the mounted contract

## Next Steps

- [External Worker Flow](external-worker-flow.md)
- [Multi-Node and Windows](multi-node-and-windows.md)
