# Auth And Boundaries

The Spider security model starts with a simple rule: Spiderweb hosts the environment, but workers own their own intelligence and external credentials.

## Control And Mount Credentials

Current flows use control-plane credentials such as:

- admin or operator tokens for workspace creation and topology operations
- auth tokens for mounting and namespace attach
- workspace or project scoped tokens where the client flow supports them

Treat these as Spiderweb access credentials, not worker-provider credentials.

## What Spiderweb Owns

Spiderweb owns:

- workspace and project access control
- namespace attachment and session setup
- mounted service exposure
- agent identity seeding
- topology and status metadata

Spiderweb does not own:

- model provider selection
- provider API keys
- worker prompts or reasoning loop
- the internals of worker-private loopback services

## Shared Versus Private Surfaces

Use the namespace layout to reason about boundaries:

- `/services/*`: shared workspace capability surface
- `/projects/<id>/meta/*`: shared discovery, health, and topology data
- `/agents/<agent_id>`: agent identity and related seeded state
- `/nodes/<worker_id>/venoms/*`: worker-owned loopback surfaces such as `memory` and `sub_brains`

If a secret belongs only to a worker, do not treat the shared workspace as the default place to store it.

## Practical Rules

1. Keep provider and model credentials outside Spiderweb unless you are explicitly modeling them as workspace data.
2. Treat `/services/*` as a shared contract that other workers and operators may inspect.
3. Treat worker-private loopback surfaces as owned by the worker process even though Spiderweb projects them.
4. Use the live metadata files to understand what the current workspace actually exposes.

## Current Security Posture

The most stable current deployment is still a Linux or WSL Spiderweb host with mounted clients and workers attaching from there or from Windows. Documented protocol behavior is canonical in SpiderProtocol; live namespace files are canonical for what a specific workspace currently exposes.
