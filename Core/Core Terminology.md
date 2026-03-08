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

It is independent agent performing work within its Project. It has long term memory and is encourages to develop its own personality and evolve itself over time.

A Spider Monkey may use sub-brains to perform functions. Sub-brains are worker agents without a personality or separate identity, they are specialised to do work in the background, whilst the Spider Monkey's primary brain runs the show and talks to Users and other Spider Monkeys.


Spider Monkey often called just agents, but Spider Monkey refers to the specific type agents natively in the Spider ecosystem. In future its possible other alien agents may interact with a Spiderweb

---

## Spiderweb

A Spiderweb is a server and works as a hub that connects the other elements.



---
## Project 

A Project is a namespace that binds various venom into a set a of threads that Spiders live in and operate on.

Each Project has a Vision as decided by the User, it may evolve over time but is the overall goal that Spider Monkeys working on the project use.

It is the workspace that Spider Monkeys work within. It may be physically distributed by appears a single namespace. 

Each Project is represented by a namespace consisting of a root UNIX filesystem (Debian by default) with Acheron services and mounts threads for Spider Monkeys to use. 

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

Venom are mounted into a Project.  

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
It is also the name of the stream of data, Acheron is a river of Venom that binds the Spiderweb to Spider Monkeys, Project and Nodes.

It is a filesystem RPC, similar in design to Plan9/STYX protocol. 

All operations are defined in terms of files and directories. 

A few primitive are used to support a wide range of functionality:
- Read
- Write
- List

Primitives are intentionally minimal. Complexity emerges from composition of these simple primitives.

---
## Memory

Durable, structured data stored within the namespace.

Memory:

- Has a stable identifier
- May link to other memory
- Is inspectable and version-able
- Is not hidden runtime state

Memory is not a special subsystem.

---

## Context

The active working set of memory loaded for LLM processing.

Context is:
- Selected by the agent
- Derived from memory
- Mostly mutable and transient

Context is not equivalent to memory.  It is a view over memory.

A few pieces of context are not mutable or transient, these define the operating instructions or other important things that an LLM must always have to function.
