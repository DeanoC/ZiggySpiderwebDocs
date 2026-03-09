# Agent Design (Current)

Agents are defined by directories under `agents/` and loaded by the runtime registry at startup or on demand.

## Identity Layers

Initial identity prompts are seeded from persona-pack files and composed from layered files (in order):

- `SOUL.md`
- `AGENT.md`
- `IDENTITY.md`
- `USER.md`

Only the first occurrence of a heading is kept across layers; later duplicates are skipped to avoid conflicts.

After seeding, the runtime loads these files into managed identity memories. `CORE.md` remains system-managed, while the agent-owned identity may evolve over time.

## `agent.json`

If present, `agent.json` supplies structured metadata such as:

- `persona_pack`
- `name`
- `description`
- `capabilities`
- `is_default`

If metadata is missing, the registry falls back to identity-file inference.

## Registry & Discovery

The agent registry:

- scans `agents/<agent_id>/` directories
- tracks `identity_loaded`
- tracks `persona_pack` when recorded in `agent.json`
- exposes `control.agent_list` / `control.agent_get`

## Implementation Pointers

- Registry: `src/agents/agent_registry.zig`
- Identity loader: `src/identity.zig`
- Persona seeding: `src/agents/system_hooks.zig`
- Control responses: `src/server_piai.zig`
