# Repository Map

The Spider ecosystem is split across several repositories on purpose. Each repo owns a different layer of the system.

## Spiderweb

[Spiderweb](https://github.com/DeanoC/Spiderweb) is the hosted workspace OS.

It owns:

- the control plane
- workspace creation and topology
- Acheron namespace projection
- the `spiderweb-fs-mount` client
- built-in service surfaces and project binds
- worker registration and session attachment

Read Spiderweb when you need to understand how a workspace is hosted, mounted, or projected into `/services`, `/nodes`, `/agents`, and `/projects/<id>/meta/*`.

## SpiderMonkey

[SpiderMonkey](https://github.com/DeanoC/SpiderMonkey) is the first-party external worker.

It owns:

- the worker process lifecycle
- workspace scanning and job handling
- worker home bootstrapping through `/services/home`
- worker registration through `/services/workers`
- worker-private loopback surfaces such as `memory` and `sub_brains`

Read SpiderMonkey when you need to understand how an external agent is expected to behave once it has a mounted workspace.

## SpiderProtocol

[SpiderProtocol](https://github.com/DeanoC/SpiderProtocol) is the canonical protocol and runtime substrate repo.

It owns:

- unified-v2 control protocol types
- Acheron runtime and node-FS message catalogs
- generated protocol specs and fixtures
- TypeScript, Python, and Go SDK inputs and reference implementations
- shared node runtime modules used by Spiderweb and SpiderNode

Read SpiderProtocol when you need exact wire behavior, SDK surfaces, or shared runtime code. SpiderDocs should link to it, not duplicate it.

## SpiderNode

[SpiderNode](https://github.com/DeanoC/SpiderNode) packages standalone node daemons.

It owns:

- deployable node runtimes outside the full Spiderweb repo
- filesystem export daemons
- pairing flows for attaching standalone nodes to Spiderweb

Read SpiderNode when you need to add storage or services from another machine into a Spiderweb-hosted namespace.

## SpiderApp

[SpiderApp](https://github.com/DeanoC/SpiderApp) is a client and operator surface.

It owns:

- CLI and GUI interaction with Spiderweb
- project and topology inspection
- filesystem browsing and operator workflows

Read SpiderApp when you need user-facing control and inspection tooling. It consumes the architecture; it does not define it.

## How To Think About The Split

- `SpiderProtocol` defines the substrate.
- `Spiderweb` hosts the environment.
- `SpiderNode` extends the environment from other machines.
- `SpiderMonkey` works inside the environment.
- `SpiderApp` lets people inspect and operate the environment.
