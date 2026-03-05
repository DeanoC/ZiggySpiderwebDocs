
Modern AI agent frameworks have made agents accessible, but not structurally coherent.

They typically grow by accumulation:

- More tools
- More APIs
- More runtime wrappers
- More context management layers
- More service boundaries

Over time, the system becomes a coordination problem.

---
## 1. Tool and API Fragmentation

Agents frequently operate across a growing mix of integration patterns:

- Connectors
- Skills
- MCP interfaces
- Filesystems
- REST APIs
- Databases
- Vector stores
- Message queues
- Cloud services
- Local scripts

Each represents a different model for capability exposure.

The problem is not that these exist.  
The problem is that they coexist without a unifying substrate.

Should GitHub access be:

- A skill?
- A connector?
- An MCP endpoint?
- A CLI tool?
- A mounted filesystem?
- A REST integration?

All are valid.  
None are universal.

For both users and agents, this creates ambiguity:

- Which interface should be used?
- Which is authoritative?
- Which has state?
- Which persists memory?
- Which is discoverable?
- Which requires custom orchestration?

The more integration patterns are added, the more the agent must reason about meta-interfaces instead of domain tasks.

This increases cognitive load for humans and decision complexity for agents.

[[Filesystem Model vs API-Centric Agent Systems]]

---
## 2. Context as Hidden State

In many frameworks, memory and context are internal to the agent runtime.

They are:

- Implicit
- Opaque
- Hard to inspect
- Hard to version
- Hard to migrate

Context windows are treated as scarce magic rather than managed resources.

This leads to:

- Ad-hoc summarization
- Manual compaction strategies
- Fragile caching
- Loss of continuity across environments

---
## 3. Environment Silos

Local, remote, and cloud execution are usually separate domains.

Agents must understand:

- Where they are running
- What they are allowed to access
- Which APIs exist in which environment

This results in environment-specific branching logic and brittle assumptions.

---
## 4. State Ownership Ambiguity

Where does an agent’s state actually live?

- In memory?
- In a database?
- In a vector store?
- In logs?
- In a tool registry?

The answer is often: all of the above.

This diffuses responsibility and complicates reasoning about behavior.

---
## Historical Precedent

The problem of distributed capability is not new.

When systems began scaling across networks and machines, the creators of Unix faced a similar question:

How do you expose heterogeneous resources through a coherent model?

The answer evolved beyond Unix into systems like Plan 9 from Bell Labs and later Inferno.

Their solution was radical in its simplicity:

- Everything is a file.
- The network is part of the namespace.
- Capabilities are mounted.
- Discovery happens through filesystem traversal.

These systems were designed for distributed computing long before cloud-native became common terminology.

They were not widely adopted commercially.

But architecturally, they solved a real problem:

Unifying heterogeneous resources under a single abstraction.

Spider Web applies the same principle to AI agents.

Where Plan 9 unified machines, Spider Web unifies capabilities.

Where Inferno exposed services through a filesystem namespace, Spider Web exposes agent memory, tools, and execution surfaces the same way.

The difference is not the model.

The difference is the class of actor operating within it.

Agents are fundamentally distributed processes.

The filesystem model fits them naturally.

---
## Summary

Current agent systems optimize for capability expansion.

They add more tools.

They increase surface area.

They hide state.

They abstract away the environment.

But as systems scale across machines, users, and workflows, these abstractions accumulate friction.

The result is not distributed intelligence.

It is distributed complexity.