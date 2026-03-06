# Spiderweb Docs

These docs are intentionally small and code-grounded.

Source of truth:
- `Spiderweb/src/server_piai.zig`
- `Spiderweb/src/fsrpc_session.zig`
- `Spiderweb/src/fs_router.zig`
- `Spiderweb/src/fs_control_plane.zig`
- `SpiderApp/src/gui/root.zig`
- `SpiderApp/src/gui/websocket_client.zig`
- `SpiderProtocol` checkout: `Spiderweb/deps/spider-protocol/src/unified_types.zig`

Kept docs:
- `overview.md` - current system shape and canonical runtime paths
- `protocols/control-agent-session.md` - unified-v2 control handshake and active control operations
- `protocols/acheron-worldfs.md` - acheron-1 worldfs contract as implemented
- `architecture/worldfs-single-websocket-hard-cut.md` - current transport status after the hard cut
- `operations/node-pairing-approval.md` - node pairing and lease flow still implemented today

Removed docs were deleted because they described RFCs, migrations, or control-watch flows that no longer match the implementation.
