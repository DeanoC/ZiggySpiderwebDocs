# System Architecture

Spider is organized as a hosted environment plus external workers.

## The Layers

### SpiderProtocol

The shared substrate. It defines the control protocol, Acheron runtime protocol, node-FS protocol, generated SDK artifacts, and shared node runtime modules.

### Spiderweb

The workspace host. It owns:

- control-plane sessions
- workspace creation and topology
- mounted namespace projection
- project binds into `/services`
- metadata under `/projects/<id>/meta/*`
- worker registration and agent identity seeding

### `spiderweb-fs-mount`

The bridge from WebSocket protocols into a local filesystem view. It supports:

- routed workspace mode with `--workspace-url`
- full namespace attach with `--namespace-url`
- `auto`, `fuse`, and `winfsp` mount backends

### External Workers

SpiderMonkey is the first-party worker, but the architecture is intentionally broader: any agent that can work through filesystem operations can use a mounted Spiderweb workspace.

### SpiderNode

The deployable node runtime that exports filesystems and services from other machines into the hosted namespace.

## Runtime Flow

1. Spiderweb starts and exposes its control plane.
2. An operator creates a workspace.
3. Spiderweb binds a set of services into `/services` for that workspace.
4. `spiderweb-fs-mount` attaches and projects the namespace locally.
5. A worker enters the mounted tree and discovers live services through metadata and control files.
6. If the worker registers itself, Spiderweb creates a worker node under `/nodes/<worker_id>` and projects worker-private venoms there.
7. Remote SpiderNode daemons extend the namespace with additional exported filesystems and services.

## Ownership Boundaries

Spiderweb owns:

- workspace hosting
- namespace topology
- mounted services
- session attachment
- control-plane metadata

Workers own:

- their thinking loop
- provider and model configuration
- provider credentials
- worker-private loopback behavior

SpiderProtocol owns:

- wire compatibility
- generated SDK artifacts
- shared node and runtime substrate

## Why The Filesystem Contract Matters

Spider does not ask every worker to speak a different tool API.

Instead, the system projects a filesystem-shaped contract:

- service capabilities are read from files such as `README.md`, `OPS.json`, `SCHEMA.json`, and `CAPS.json`
- actions happen through file writes and reads
- topology and health are published as regular files under `/meta` and `/projects/<id>/meta`

That makes the environment stable even when the worker implementation changes.

## Related Docs

- [Workspace and Namespace](workspace-and-namespace.md)
- [Workers and Services](workers-and-services.md)
- [Nodes and Topology](nodes-and-topology.md)
