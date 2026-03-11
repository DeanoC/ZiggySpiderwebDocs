# Spider Ecosystem Handbook

This repository is the current handbook for the Spider ecosystem.

It describes the system as it exists now:

- `Spiderweb` is the hosted distributed workspace OS and namespace host.
- `SpiderMonkey` is the first-party external worker process.
- `SpiderProtocol` owns the canonical wire contracts, generated SDK inputs, fixtures, and shared runtime substrate.
- `SpiderNode` packages standalone node daemons that extend the namespace.
- `SpiderApp` is a client and operator surface, not the center of the architecture.

This handbook intentionally replaces old design notes and stale worldview documents. If a concept is not described here, do not assume it is part of the current supported model.

## Start Here

1. [Overview](overview.md)
2. [Repository Map](ecosystem/repo-map.md)
3. [System Architecture](architecture/system-architecture.md)
4. [Quickstart: Workspace Host](operations/quickstart-workspace-host.md)

## What This Handbook Covers

- how the Spider repos fit together
- how Spiderweb projects a workspace namespace
- how workers, services, mounts, and nodes interact
- how to run the current Linux or WSL hosted flow
- where the canonical protocol and SDK references live

## What This Handbook Does Not Do

- duplicate protocol message tables that already live in `SpiderProtocol`
- preserve historical RFCs, TODOs, or speculative design notes as current guidance
- describe Spiderweb as an embedded agent AI runtime
- describe provider keys or model configuration as a Spiderweb concern

## Core Repositories

- [Spiderweb](https://github.com/DeanoC/Spiderweb): workspace host, control plane, mount client, built-in workspace services
- [SpiderMonkey](https://github.com/DeanoC/SpiderMonkey): first-party external worker
- [SpiderProtocol](https://github.com/DeanoC/SpiderProtocol): protocol library, SDK artifacts, canonical protocol docs, node runtime substrate
- [SpiderNode](https://github.com/DeanoC/SpiderNode): standalone node runtime packaging
- [SpiderApp](https://github.com/DeanoC/SpiderApp): operator and client surface
