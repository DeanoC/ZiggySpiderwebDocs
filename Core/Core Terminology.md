# Spider Terminology

This document defines the canonical meanings of terms used in the Spider ecosystem.  
These definitions are architectural, not colloquial.

---

## Spider Monkey

An AI agent operating within the Spiderweb.

A Spider Monkey:

- Lives and works in a cocoon
- Pulls on various threads to access venom that does work. 
- Speaks the Acheron protocol for tool like access
- Manages its own working context and memory

A spider monkey is not a wrapper around tools.  
It is independent agent performing work within its Cocoon.

---

## Spiderweb

A Spiderweb is a server and works as a hub that connects the other elements.



---
## Workspace 

A workspace is a namespace that binds various venom into a set a of threads that Spiders live in and operate on.

It is often a project that Spider Monkeys work within. It may be physically distributed by appears a single namespace. 

Each Workspace is represented by a namespace consisting of a root UNIX filesystem (Debian by default) with Acheron services and mounts threads for Spider Monkeys to use. 

The namespace abstracts physical topology.  

Local, remote, and cloud resources appear as part of the same structure.

All capabilities, memory, and execution surfaces exist within the namespace.

---

## Venom

Any actionable resource or service exposed through spider web via Acheron

Examples include:
- Executable nodes
- Data sources
- Device interfaces
- External services
- Memory structures

Venom are mounted into the Cocoon.  

---

## Nodes

A machine or execution surface that provide Venoms to the Spider Web.

A node may:

- Host files
- Expose capabilities
- Execute agents
- Provide storage

Nodes are part of the physical topology.  
They are abstracted through the namespace.

---
## Acheron

Acheron is the protocol used to access and use Venoms through the Spiderweb.
It is also the name of the stream of data, Acheron is a river of Venom that binds the Spiderweb to Spiders, Cocoon and Nodes.

It is a filesystem rpc, similar in design to Plan9/STYX protocol. 

All operations are defined in terms of files and directories. 

Examples include:

- Read
- Write
- List

Primitives are intentionally minimal.  
Complexity must emerge from composition.


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

