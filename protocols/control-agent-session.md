# Control Agent Session Overview

This page is an architecture-level summary of the control-plane session flow.
The exact wire contract now lives in the canonical `spider-protocol` docs so it
stays aligned with the Zig implementation and generated fixtures.

## Current Model

- Control traffic uses the unified-v2 websocket envelope on the base websocket path.
- Clients negotiate the control protocol first, then establish a control session.
- Session, agent, workspace, project, node, and venom operations all sit on that
  same `control.*` message family.

## What To Read For Exact Wire Details

Canonical reference:
- [`unified-v2-control.md`](../../Spiderweb/deps/spider-protocol/docs/protocols/unified-v2-control.md)

Related canonical references:
- [`acheron-runtime-v1.md`](../../Spiderweb/deps/spider-protocol/docs/protocols/acheron-runtime-v1.md)
- [`node-fs-unified-v2.md`](../../Spiderweb/deps/spider-protocol/docs/protocols/node-fs-unified-v2.md)

## Notes

- If this overview and the generated canonical reference disagree, treat the
  canonical reference as authoritative.
- Payload details and fixture examples are maintained under
  `Spiderweb/deps/spider-protocol/sdk/spec/`.

Common control-plane workflows include:
- agent discovery and selection
- session attach, resume, restore, and close
- workspace and project provisioning
- node and venom catalog management
- auth and audit surfaces

## Implementation Notes

Primary references:
- `src/server_piai.zig`
- `src/agents/agent_registry.zig`
- `SpiderProtocol/src/unified_types.zig`
