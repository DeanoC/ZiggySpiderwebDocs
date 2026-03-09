# Persona Packs (Current)

Spiderweb now creates agents by seeding a persona pack directly into the agent directory.

Current seed files:

- `SOUL.md`
- `AGENT.md`
- `IDENTITY.md`
- optional `USER.md`
- optional `agent.json`

The seeded files define the initial persona. After first runtime load, the living identity belongs to the agent as managed memory and may evolve over time. Shared persona-pack content remains curated; the runtime does not keep a hatch/bootstrap compatibility layer for non-system agents.

Ownership boundary:

- `system.core` and runtime contracts remain kernel-managed, write-protected, and unevictable.
- `system.soul`, `system.agent`, and `system.identity` are agent-owned identity memories: seeded by the kernel, then left mutable so the agent can evolve.
- Working memories such as active goals and plans remain mutable workspace state rather than persona-pack content.

Current creation flow:

1. Create agent directory via `/global/agents/control/create.json`.
2. Resolve the selected `persona_pack` id to a curated pack directory (the control surface may still accept explicit internal pack paths for tests/setup code).
3. Copy the required persona seed files into `agents/<agent_id>/`.
4. Persist the selected `persona_pack` in `agent.json`.
5. Load those files into runtime identity memories on first attach/run.

Implementation pointers:
- `src/agents/agent_registry.zig`
- `src/agents/system_hooks.zig`
- `src/acheron/session.zig`
