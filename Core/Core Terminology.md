# Core Terminology

This document defines the canonical meanings of terms used in Spider Web.  
These definitions are architectural, not colloquial.

---

## Agent

A process operating within the Spider Web namespace.

An agent:

- Reads from the filesystem
- Writes to the filesystem
- Executes capabilities exposed through the namespace
- Manages its own working context

An agent is not a wrapper around tools.  
It is a first-class process within the distributed system.

---

## Namespace

The unified filesystem tree presented across machines and platforms.

The namespace abstracts physical topology.  
Local, remote, and cloud resources appear as part of the same structure.

All capabilities, memory, and execution surfaces exist within the namespace.

---

## Capability

Any actionable resource exposed through the namespace.

Examples include:

- Executable nodes
- Data sources
- Device interfaces
- External services
- Memory structures

Capabilities are mounted into the namespace.  
They are not registered as separate integration primitives.

---

## Mount

The act of attaching a capability or resource into the namespace.

Mounting expands the world visible to agents without modifying the agent itself.

Mounting does not introduce new abstraction types.  
It extends the existing model.

---

## Node

A machine or execution surface participating in the Spider Web system.

A node may:

- Host files
- Expose capabilities
- Execute agents
- Provide storage

Nodes are part of the physical topology.  
They are abstracted through the namespace.

---

## Memory

Durable, structured data stored within the namespace.

Memory:

- Has a stable identifier
- May link to other memory
- Is inspectable and versionable
- Is not hidden runtime state

Memory is not a special subsystem.

---

## Context

The active working set of memory loaded for LLM processing.

Context is:

- Selected by the agent
- Derived from memory
- Mutable and transient

Context is not equivalent to memory.  
It is a view over memory.

---

## Working Set

The subset of memory currently loaded into context for a task.

Agents manage their working set through:

- Selection
- Summarization
- Compaction
- Eviction

---

## Primitive

A foundational operation guaranteed by Spider Web.

Examples include:

- Read
- Write
- List
- Execute
- Watch

Primitives are intentionally minimal.  
Complexity must emerge from composition.

---

## Topology

The physical distribution of nodes across devices, networks, and cloud infrastructure.

Topology must not leak into agent logic.