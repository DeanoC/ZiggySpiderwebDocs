# Workers And Services

Spiderweb is built for external workers.

## Worker Model

A worker is a normal process that receives a mounted workspace and uses the filesystem as its contract.

Spiderweb does not host the worker's model runtime or provider credentials. It hosts the environment the worker operates in.

SpiderMonkey is the first-party example of this model:

- it starts outside Spiderweb
- it is pointed at a mounted workspace root
- it claims a durable home through `/services/home`
- it registers its worker node through `/services/workers`
- it maintains heartbeats while it runs
- it detaches cleanly on shutdown

## Worker Registration

The `workers` service is the control point for attached external workers.

Its control files include:

- `control/register.json`
- `control/heartbeat.json`
- `control/detach.json`

When a worker registers, Spiderweb creates a live worker node under `/nodes/<worker_id>` and projects worker-owned venom surfaces there.

## Worker-Owned Services

The current worker-private loopback services are:

- `memory`
- `sub_brains`

They are projected under:

- `/nodes/<worker_id>/venoms/memory`
- `/nodes/<worker_id>/venoms/sub_brains`

These are worker-owned surfaces. Spiderweb exposes them into the namespace, but the worker manages their actual behavior.

## Shared Workspace Services

Services under `/services/*` are shared workspace contract surfaces, not private worker state.

The default workspace binds focus on:

- workspace and package discovery
- mounts management
- agent home allocation
- worker registration
- session-scoped chat and jobs
- event delivery

The GitHub-oriented template adds workflow and developer services such as:

- `git`
- `github_pr`
- `missions`
- `pr_review`
- `terminal`
- `library`
- `search_code`
- `web_search`

## Service Contract Shape

Service directories are designed to be self-describing.

Workers should expect to discover capabilities through files such as:

- `README.md`
- `SCHEMA.json`
- `CAPS.json`
- `OPS.json`
- `PERMISSIONS.json`
- `STATUS.json`
- `status.json`
- `result.json`
- `control/*`

This is the main reason Spider can support multiple worker implementations without rewriting the whole environment for each one.

## Ownership Rules

Use these boundaries when reasoning about bugs or responsibilities:

- if it is a workspace bind, Spiderweb owns it
- if it is worker-private loopback state, the worker owns it
- if it is a wire compatibility question, SpiderProtocol owns it
