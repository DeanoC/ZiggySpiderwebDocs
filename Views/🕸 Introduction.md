Ziggy Spider Web is an AI agent platform built from a different architectural assumption than most current agent systems.

Instead of embedding agents inside applications or wrapping them in service APIs, Spider Web treats agents as processes living inside a distributed operating system.

The system spans multiple machines and platforms, presenting a unified namespace. Agents do not operate inside a single runtime, container, or cloud boundary. They operate inside the system itself.

---

## Agents That Only Need One Skill

In Spider Web, an agent only needs to understand one interface: the filesystem.

Everything else emerges from that.

If an agent can:
- Read
- Write
- List
- Watch
- Execute

it can operate across the entire distributed environment.

New capabilities are not injected into the agent.  
They are mounted into the namespace.

A new machine, service, device, or tool simply appears as another part of the filesystem tree.

No new SDK.  
No new agent training.  
No environment-specific branching logic.

Just a larger world.

---

## Distributed by Default

Spider Web does not distinguish between local, remote, and cloud as separate architectural domains.

They are implementation details.

An agent can:

- Read an image from a phone
- Perform heavy processing on a cloud node
- Coordinate tasks from a desktop
- Persist results to durable storage

All without switching abstractions.

From the agent’s perspective, it is one system.

---

## Memory and Context as Files

Most agent frameworks treat memory and context as hidden internal state.

Spider Web externalizes it.

Context is cache.  
Summaries are compacted files.  
Long-term memory is durable storage.  
State transitions are versioned changes.

An agent manages its memory the same way it manages any other resource: through filesystem operations.

This unifies execution, memory, capability discovery, and coordination under a single model.

---

# Why This Matters (For People Like Us)

If you’ve built or extended something like OpenClaw, you’ve probably experienced:

- Tool sprawl
- API fragmentation
- Context management hacks
- Environment-specific assumptions
- “Where does this state actually live?”

Spider Web reduces the number of primitives.

Instead of adding more tools, it simplifies the substrate.

It does not try to make agents smarter.

It makes the environment coherent.

[[Problem Statement]]

[[Historical Precedent]]