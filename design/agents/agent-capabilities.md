# Agent Capabilities

Spiderweb still has an internal agent capability engine, but it is no longer the main product model.

## Current Role

The capability engine is private runtime machinery used inside the agent loop.

It currently handles:

- memory operations such as `memory_load`, `memory_create`, and `memory_search`
- internal event-bus style messaging such as `talk_user`, `talk_agent`, `talk_brain`, and `talk_log`

These are implementation details behind the agent runtime. They are not the public way to add new functionality to Spiderweb.

## What Is Not Current

`wait_for` and the older talk/event correlation flow have been removed from the active runtime path. Timer, event, and interrupt behavior should come back only as real Venoms.
Do not reintroduce them as hidden agent tools.

## Public Model

The public model is:

- Acheron for filesystem-style access
- Venoms for actionable capability surfaces
- Spider Monkeys as the agents that consume those surfaces

Direct provider tool exposure remains intentionally small:

- `file_read`
- `file_write`
- `file_list`

Everything else should trend toward Venom-backed surfaces.

## Implementation Pointers

- Agent capability engine: `src/agents/capability_engine.zig`
- Agent runtime loop: `src/agents/agent_runtime.zig`
- Provider/runtime orchestration: `src/agents/runtime_server.zig`
- Acheron projection of public surfaces: `src/acheron/session.zig`
