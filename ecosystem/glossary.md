# Glossary

## Spiderweb

The hosted distributed workspace OS. It runs the control plane, projects the namespace, and exposes built-in services and node exports to workers.

## Workspace

The operator-facing unit of work hosted by Spiderweb.

A workspace is the mounted namespace that workers use. In several current paths and control-plane internals, the same object still appears as a `project`, which is why metadata lives under `/projects/<id>/meta/*`.

## Namespace

The mounted filesystem-shaped contract projected by Spiderweb. This includes shared service aliases, node exports, agent identities, metadata, and worker-private loopback areas.

## Agent

A logical identity operating in Spiderweb. Spiderweb can seed and manage attached agent identities under `/agents/<agent_id>`, but it does not own the agent's model or cognition.

## Worker

A running process that performs work against a mounted workspace. SpiderMonkey is the first-party worker. A worker may register its own node presence and private loopback services.

## SpiderMonkey

The first-party Spider worker process. It mounts into a Spiderweb workspace from the outside and uses the filesystem as its contract.

## Node

A machine or runtime surface that exports filesystems or services into Spiderweb. Nodes appear under `/nodes/<node_id>`.

## Venom Package

A packaged service definition that Spiderweb can project into the namespace. Built-in examples include `home`, `workers`, `mounts`, `git`, `missions`, and `pr_review`.

## Service

A discoverable capability exposed through the namespace. Project-bound aliases live under `/services/<name>`. The same underlying service often originates under `/nodes/local/venoms/<name>` or another node path.

## Acheron

The filesystem-style protocol layer used for namespace and node-FS access. Spiderweb uses it to expose the mounted workspace contract over WebSocket.

## Control Plane

The WebSocket control surface used to create workspaces, attach sessions, manage topology, and negotiate namespace access.

## Mounted Service

A service that has been bound into the current workspace under `/services/*`. The live list is published in `/projects/<id>/meta/mounted_services.json`.

## Worker-Private Loopback Service

A service owned by a worker and projected into the namespace for that worker's own use. Current examples include `memory` and `sub_brains` under `/nodes/<worker_id>/venoms/*`.
