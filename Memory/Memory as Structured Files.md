# Memory as Structured Files

Memory is not hidden runtime state.

It is durable, inspectable, versionable filesystem data.

Context becomes:
- Cache
- Snapshot
- Summary
- Compaction

All implemented through filesystem operations.

---
## Why This Reduces Fragmentation

In many agent frameworks, memory is implemented as a special subsystem:

- A vector database
- A hidden runtime structure
- A framework-managed context window
- An external service

This introduces another integration surface.

Memory becomes something separate from the rest of the system.

In Spider Web, memory is not a separate primitive. It is expressed through the same filesystem model as every other capability.

There is no special “memory connector.”

There is no distinct “context API.”

There is only the namespace.

This reduces the number of conceptual models an agent must understand. Files, execution surfaces, memory, and context are all represented the same way.

The same traversal logic that discovers tools discovers knowledge.

The same read operation that loads a configuration file loads a memory.

The same versioning mechanism that protects state protects context.

This eliminates a class of architectural decisions entirely.

The system does not need to decide whether something should be a skill, a connector, an MCP endpoint, or a special runtime construct.

If it can be represented as part of the namespace, it belongs.

In practical terms, this means that integration does not require a dedicated connector layer. If a knowledge base exists as files, it is already part of the system. There is no need for a special Obsidian integration, or a GitHub skill, or a bespoke memory API. The namespace is the integration surface.