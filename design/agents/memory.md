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

## Implementation Pointers

- Memory schema + seeds: `src/memory_schema.zig`
- Memory store: `ziggy-memory-store`
- Acheron bridge: `src/acheron/session.zig`
