# Overview

The Spider ecosystem is built around one core idea: the agent should work against a mounted workspace, not a pile of bespoke APIs.

`Spiderweb` hosts that workspace. It exposes a namespace over Acheron, projects services and node exports into it, and lets external workers operate through normal filesystem operations such as read, write, and list.

## The Current Split

- `Spiderweb` hosts the control plane, workspace topology, Acheron namespace, mount client, and built-in workspace services.
- `SpiderMonkey` is the first-party worker that consumes a mounted workspace and performs work from outside Spiderweb.
- `SpiderProtocol` defines the exact wire contracts, generated protocol artifacts, language SDK inputs, and shared node runtime substrate.
- `SpiderNode` packages standalone node daemons that export filesystems and services into Spiderweb.
- `SpiderApp` is a client and operator surface for people interacting with the system.

## What Spiderweb Is

Spiderweb is a hosted distributed workspace OS. It owns:

- workspace creation and control-plane metadata
- namespace projection over Acheron
- built-in service surfaces under `/services`
- node and mount topology
- worker registration and per-agent identity seeding

Spiderweb does **not** own:

- model selection
- provider credentials
- an embedded agent thinking loop
- worker-private memory implementation details

Those belong to the worker process. SpiderMonkey is the first-party example, but any agent that can work through a filesystem can use Spiderweb.

## The Runtime Flow

1. An operator runs Spiderweb on a Linux or WSL host.
2. A workspace is created through the control plane.
3. `spiderweb-fs-mount` attaches to that workspace, either as a routed workspace mount or a full namespace mount.
4. The worker process enters the mounted tree and discovers the live contract from files such as `/meta/protocol.json`, `/projects/<id>/meta/mounted_services.json`, and `/services/*`.
5. Spiderweb projects built-in services, node exports, and worker-owned loopback services into the namespace.
6. Remote nodes extend the workspace under `/nodes/<node_id>`.

## The Namespace Model

The namespace is the contract.

Common roots include:

- `/services`: project-bound service aliases that workers are expected to use first
- `/nodes`: host-local, remote-node, and worker-owned surfaces
- `/agents`: attached agent identities and related state
- `/global`: global aliases and discovery surfaces
- `/projects/<id>/meta`: workspace metadata, mounted services, binds, health, topology, and status snapshots
- `/meta/protocol.json`: a protocol snapshot for mounted clients

This is why SpiderDocs focuses on topology, ownership, and operating flow. Exact message catalogs belong in `SpiderProtocol`.

## What To Read Next

- [Repository Map](ecosystem/repo-map.md)
- [System Architecture](architecture/system-architecture.md)
- [Workspace and Namespace](architecture/workspace-and-namespace.md)
- [Quickstart: Workspace Host](operations/quickstart-workspace-host.md)
