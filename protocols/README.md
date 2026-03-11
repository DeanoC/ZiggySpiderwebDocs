# Protocols

SpiderDocs is not the canonical protocol reference.

`SpiderProtocol` owns the exact wire contracts, generated protocol artifacts, SDK inputs, and shared runtime code. Use this folder only to understand how those protocols fit into the Spider ecosystem.

## Canonical References

- [SpiderProtocol Overview](https://github.com/DeanoC/SpiderProtocol/blob/main/docs/overview.md)
- [Unified v2 Control Protocol](https://github.com/DeanoC/SpiderProtocol/blob/main/docs/protocols/unified-v2-control.md)
- [Acheron Runtime Protocol](https://github.com/DeanoC/SpiderProtocol/blob/main/docs/protocols/acheron-runtime-v1.md)
- [Node FS Protocol](https://github.com/DeanoC/SpiderProtocol/blob/main/docs/protocols/node-fs-unified-v2.md)
- [Spider Venom WASM ABI](https://github.com/DeanoC/SpiderProtocol/blob/main/docs/protocols/spider-venom-wasm-abi-v1.md)

## SDK Direction

`SpiderProtocol` now carries generated specs and fixtures under `sdk/spec/` plus reference SDK surfaces for:

- TypeScript
- Python
- Go

That repo is the source of truth for any direct protocol client or future WASM service authoring support.

## Boundary Rule

When SpiderDocs and SpiderProtocol disagree, SpiderProtocol wins.

Do not treat old hand-maintained protocol pages, wildcard message families, or internal helper envelopes as the public contract. The generated SpiderProtocol references are the supported source.
