# Architectural Principles

Spider Web is built around a small set of structural commitments.

These are not implementation details.  

They are invariants.

If a feature violates them, the feature is wrong.

## 1. Filesystem as the Universal Capability Model

All capabilities are exposed through the namespace.

[[Filesystem as the Universal Capability Model]]
[[Filesystem Model vs API-Centric Agent Systems]]

## 2. Agents as First-Class Processes

Agents are not external orchestrators.

[[Agents as First-Class Processes]]

## 3. Environment Transparency

Local, remote, and cloud are not semantic categories, its all one environment.

[[Environment Transparency]]

## 4. Memory as Structured Files

Memory is not hidden runtime state its files.

[[Memory as Structured Files]]

## 5. Capability Discovery Through Traversal

New functionality appears through namespace expansion.

[[Capability Discovery Through Traversal]]

## 6. Simplicity by Design

Complexity emerged from composition, the design is constrained to "just enough" to achieve this

[[Design Constraints]]