# Nodes and Venom Thoughts and Vision

We support nodes and services via the Acheron protocol, but currently this is fragmented on how those services are implemented and registered.
The overall intention is to build a library of services that can be added into the spider web, easily, allowing distributed abilities to be added dynamic and easily added.

## Venom
Services over Acheron to the Spiderweb will be called Venom, Nodes will register available Venoms to be available in several namespaces in project Workspaces.
It possible multiple versions of the same Venom may be available in a spider web at the same time (for example a Mail Venom may have 2 version for 2 different email accounts)
Venoms will provide enough info so the user can decide to add one to a node, and via Acheron provide the interface and usage instruction to the agents.

## Nodes
Each node will present available Venoms via connecting with the Spiderweb with the appropriate security tokens.
Nodes can also offer library information, allowing knowledge (like skills) to be found by agents.


## Namespace
/global -> Venoms services here are not specific to the machine the node is running on
/nodes -> Venoms services here are local to the machine the node is running on
/agents -> Venoms services here are specific to the agents themselves

## Venom Implementation
The primary route for Venoms will be a WASM based with a contract allowing custom services to be written and installed without native compilation
For some venoms that is the option of native implementation into the Node software itself, this should be where efficiency or capability needs a closer native implementation
Capability should probably be gated in some way but for now we will define the minimum required until we have a proper design.
#### WASM Venom
Using a WASM JIT engine, Nodes will be able to implement particular services in a safe plugin style. The intention is that nodes could eventually retrieve venoms from a registry and use them to provide Acheron services. Node and WASM Venoms will share a interface ABI providing just enough surface for common services to be implemented.

## Current Implementation And changes
At the moment, there are 2 distinct nodes system, a local one used by the Spiderweb itself and a remote Node facilities.
In particular Spiderweb provides local filesystem, chat and debug facilities.
These should be moved to single system rather 2 different ones, all Spiderweb local services should be moved to Venom systems.

Probably most Node facilities should be a module that can be added to the Spiderweb service, separate Nodes and even the SpiderApp clients
