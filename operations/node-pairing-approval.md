# Node Pairing And Approval

This is the current node onboarding and lease flow implemented by Spiderweb.

## Pairing Modes

### Invite flow
- admin creates an invite with `control.node_invite_create`
- node redeems it with `control.node_join`
- server returns `node_id`, `node_secret`, `lease_token`, and `lease_expires_at_ms`

### Approval flow
- node submits `control.node_join_request`
- admin reads `control.node_join_pending_list`
- admin approves with `control.node_join_approve` or denies with `control.node_join_deny`

Both flows end with the same node identity and lease material.

## Lease

Paired nodes refresh leases with:
- `control.node_lease_refresh`

## Service Catalog

Nodes publish service state with:
- `control.node_service_upsert`

Clients fetch current catalog state with:
- `control.node_service_get`
- Acheron reads under `/nodes/<node_id>/services/*`
- retained feed reads from `/global/services/node-service-events.ndjson`

## Important Notes

- `control.node_service_watch` is no longer recognized; use `/global/services/node-service-events.ndjson` over Acheron.
- The current monitoring path is the retained worldfs file feed.
- Router traffic to paired node endpoints uses one websocket per endpoint connection.

## Source Files

- `Spiderweb/src/server_piai.zig`
- `Spiderweb/src/fs_control_plane.zig`
- `Spiderweb/src/fs_router.zig`
