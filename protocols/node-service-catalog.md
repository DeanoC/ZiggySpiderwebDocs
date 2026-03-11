# Node Service Catalog Overview

This page explains the role of node-exported venoms and namespace drivers at a
high level. It no longer carries the canonical message list because that drifted
from the implementation.

## What The Catalog Represents

- Nodes publish venom metadata into the control plane.
- Spiderweb projects that metadata into the namespace and into workspace mount
  selection.
- Executable namespace venoms can be backed by native process, native in-proc,
  or WASM runtimes.

## Canonical References

Canonical reference:
- [`unified-v2-control.md`](../../Spiderweb/deps/spider-protocol/docs/protocols/unified-v2-control.md)

Related canonical references:
- [`namespace-driver-abi-v1.md`](../../Spiderweb/deps/spider-protocol/docs/protocols/namespace-driver-abi-v1.md)
- [`spider-venom-wasm-abi-v1.md`](../../Spiderweb/deps/spider-protocol/docs/protocols/spider-venom-wasm-abi-v1.md)

## Notes

- The canonical control reference is the source of truth for currently supported
  `control.*` catalog operations.
- Older watch/event names that were previously documented here are intentionally
  not repeated in this overview unless they are present in the canonical
  generated reference.

The node catalog conceptually carries:
- identity and platform information for a node
- a set of published venoms or services
- endpoint and mount projection hints
- runtime, capability, schema, and permission metadata

That metadata is projected into the Acheron namespace and used by workspace
mount selection and policy gating.

## Implementation Pointers

- `deps/spider-protocol/src/spiderweb_node/fs_node_service.zig`
- `deps/spider-protocol/src/spiderweb_node/venom_catalog.zig`
- `src/server_piai.zig`
