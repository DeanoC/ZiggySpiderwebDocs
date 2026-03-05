# Design Constraints

Spider Web is defined as much by what it refuses to do as by what it enables.

These constraints shape the architecture.

If a proposed feature violates them, the architecture must be reconsidered before the feature is accepted.

Why? [[Filesystem Model vs API-Centric Agent Systems]]

---

## 1. No Parallel Capability Models

Spider Web will not introduce multiple first-class integration primitives.

There will not be separate concepts for:
- Skills
- Connectors
- Plugins
- MCP endpoints
- Tool registries

All capabilities must be expressible through the filesystem namespace.

If a feature requires a new integration abstraction, the model is being eroded.

---

## 2. No Hidden Memory Subsystems

Memory is not allowed to become an opaque internal mechanism.

There will be no:

- Implicit context injection
- Hidden vector store bindings
- Framework-managed “magic” memory

All memory must be durable, inspectable, and represented as structured filesystem data.

---

## 3. No Environment-Specific Logic in Agents

Agents should not need to branch behavior based on:

- Local vs remote
- Cloud vs device
- Specific infrastructure providers

Physical topology must not leak into agent logic.

---

## 4. Minimal Primitive Surface

Spider Web minimizes the number of core operations.

Complexity must emerge from composition, not from adding new primitives.

If solving a problem requires introducing a new foundational concept, that problem should be re-evaluated.

---

## 5. Explicit State Ownership

All state must have a clear location and representation.

There should be no ambiguity about:

- Where memory lives
- Where execution state resides
- Where persistent data is stored

If state cannot be located and inspected through the namespace, it does not belong in the system.

---
Related:  
- [[Architectural Principles]]  
- [[Filesystem as Universal Capability Model]]  
- [[Memory as Structured Files]]