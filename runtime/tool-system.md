# Provider Tool Loop

Spiderweb exposes a very small direct tool surface to providers through the runtime loop. There is no standalone `tool.list`/`tool.call` WebSocket API for clients.

The broader system model is Acheron + Venoms:

- Acheron provides filesystem-style access
- Venoms provide actionable capability surfaces
- provider tools stay intentionally minimal

## Provider Tool Loop

1. Client sends `session.send`.
2. Runtime builds provider context and tool schema.
3. Provider emits exactly one tool call per response (multi-tool batches are rejected).
4. Runtime executes the tool and returns a `tool_result` to the provider.
5. The loop repeats until the provider returns a final assistant message.
6. Client receives `session.receive` plus any `tool.event`/`memory.event` frames.

### Provider-Exposed Tools

Direct provider schema exposure is intentionally limited:
- `file_read`
- `file_write`
- `file_list`

These are the only tool names accepted in `agent.control` `tool.call` while running in Acheron strict mode.

### Internal Agent Capabilities

Spiderweb still has internal runtime capabilities used by the agent loop, but those are not the preferred public extension model.

Public capability surfaces such as terminal, search, events, chat, and filesystem access should be reached through Venoms and Acheron paths rather than by expanding the direct provider tool list.

## Constraints & Safeguards

- One tool call per provider response (enforced).
- Tool call cap per request (see `runtime_server.zig` guard rails).
- File tools are the only direct provider tools.
- Tool retry and lease behavior is configured via `runtime` settings in `config.json`.

## Implementation Pointers

- Tool loop: `src/agents/runtime_server.zig`
- Tool registry/exec: `ZiggyToolRuntime/src/tool_registry.zig`, `ZiggyToolRuntime/src/tool_executor.zig`
- Agent capability engine: `src/agents/capability_engine.zig`
- Acheron session projection: `src/acheron/session.zig`
- Runtime configuration: `src/config.zig`
