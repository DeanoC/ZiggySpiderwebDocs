# Nodes And Topology

Spiderweb can host a workspace locally while projecting additional filesystems and services from other machines.

## Local Versus Remote Nodes

The host running Spiderweb appears as the local node.

Examples of local source paths:

- `/nodes/local/fs`
- `/nodes/local/venoms/<venom_id>`

Remote paired nodes appear under their own IDs:

- `/nodes/<node_id>/fs`
- `/nodes/<node_id>/...`

## What SpiderNode Adds

SpiderNode packages the standalone node runtime so operators can attach nodes without cloning the entire Spiderweb server repo.

A typical invite-flow start looks like:

```bash
./zig-out/bin/spiderweb-fs-node \
  --export "work=.:rw" \
  --control-url "ws://<server>:18790/" \
  --control-auth-token "<admin-token>" \
  --pair-mode invite \
  --invite-token "inv-..." \
  --node-name "edge-node"
```

That node can then contribute exported filesystems and other services into the hosted namespace.

## How Nodes Show Up In A Workspace

Spiderweb publishes node and mount state through metadata such as:

- `/projects/<id>/meta/nodes.json`
- `/projects/<id>/meta/topology.json`
- `/projects/<id>/meta/workspace_status.json`
- `/projects/<id>/meta/mounts.json`
- `/projects/<id>/meta/mounted_services.json`

Workers should use those files to understand the live topology instead of assuming a fixed machine layout.

## Multi-Node Composition

Spiderweb combines topology from several sources:

- host-local venom surfaces
- project binds into `/services`
- remote SpiderNode exports
- worker registration surfaces

The result is a single namespace where local and remote capability discovery use the same filesystem contract.

## Operational Reality

Today the most mature setup is still:

- Spiderweb host on Linux or WSL
- additional nodes attached as needed
- Windows used for mount clients or selected node experimentation

That keeps the server story stable while the cross-platform node surface continues to grow.
