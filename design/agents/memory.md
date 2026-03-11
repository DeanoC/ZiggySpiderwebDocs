# Memory Model (Current)

Memory is managed by the runtime memory store and exposed via Acheron service paths.

## Access Paths

Use `/global/memory` control files instead of calling `memory_*` tools directly:

- `/global/memory/control/create.json`
- `/global/memory/control/load.json`
- `/global/memory/control/versions.json`
- `/global/memory/control/mutate.json`
- `/global/memory/control/evict.json`
- `/global/memory/control/search.json`

Results and status:
- `/global/memory/result.json`
- `/global/memory/status.json`

## Seeded Runtime Memories

The runtime seeds system instructions and loop/tool contracts on boot (see `src/memory_schema.zig`). These include:

- `system.policy`
- `system.loop_contract`
- `system.tool_contract`
- `system.completion_contract`

It also seeds identity memories from persona-pack files via `src/agents/system_hooks.zig`:

- `system.core`
- `system.soul`
- `system.agent`
- `system.identity`

## Ownership Rules

- `kernel_managed`
  - `system.core`
  - `system.policy`
  - `system.loop_contract`
  - `system.tool_contract`
  - `system.completion_contract`
  - write-protected and unevictable
- `agent_identity`
  - `system.soul`
  - `system.agent`
  - `system.identity`
  - mutable after seed, but kept unevictable so the agent always has a current self-model
- `working_memory`
  - goal, plan, run, context, and user-preference memories
  - mutable and evictable unless a specific workflow says otherwise

The Acheron memory namespace now reflects this classification in result payloads via `ownership` alongside `memory_path` when a response includes a memory id.

## Implementation Pointers

- Memory schema + seeds: `src/memory_schema.zig`
- Ownership model: `src/agents/memory_ownership.zig`
- Memory store: `ziggy-memory-store`
- Acheron bridge: `src/acheron/session.zig`
