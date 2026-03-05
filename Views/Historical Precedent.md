# Historical Precedent

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