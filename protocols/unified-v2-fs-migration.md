# Unified-v2 FS Migration Notes

This page keeps the migration intent and compatibility framing. The exact
current protocol names, handshake order, and error envelopes now live in the
canonical generated `spider-protocol` references.

## Migration Summary

- Unified v2 requires explicit `channel` selection.
- Runtime Acheron and node-FS negotiation are mandatory.
- Canonical FS names use the `acheron.t_fs_*`, `acheron.r_fs_*`,
  `acheron.e_fs_*`, and `acheron.err_fs` families.
- Older helper envelopes and removed legacy names are not part of the public SDK
  surface.

## Canonical References

Canonical reference:
- [`node-fs-unified-v2.md`](../../Spiderweb/deps/spider-protocol/docs/protocols/node-fs-unified-v2.md)

Related canonical references:
- [`acheron-runtime-v1.md`](../../Spiderweb/deps/spider-protocol/docs/protocols/acheron-runtime-v1.md)
- [`unified-v2-control.md`](../../Spiderweb/deps/spider-protocol/docs/protocols/unified-v2-control.md)

## Notes

- Use the generated fixture files under
  `Spiderweb/deps/spider-protocol/sdk/spec/fixtures/` when updating external
  clients.
- Treat the canonical `spider-protocol` docs as authoritative when this summary
  becomes stale.
