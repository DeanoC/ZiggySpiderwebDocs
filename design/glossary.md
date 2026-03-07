# Glossary

- **Acheron**: JSON-based filesystem protocol used for runtime and the agent-visible namespace.
- **Acheron Namespace**: The canonical agent-visible filesystem rooted at `/` and exposing `/nodes`, `/agents`, `/global`, and optional `/debug`.
- **Unified-v2**: Control-plane WebSocket protocol (control.* messages).
- **Control Plane**: Manages projects, mounts, tokens, nodes, and service catalog.
- **Node**: A host/runtime that exports filesystem or service namespaces.
- **Service Catalog**: Control-plane list of services advertised by a node.
- **Project**: Control-plane container for mounts, policy, and session attachment.
- **Mount**: Mapping from a node export into a canonical namespace path.
- **Agent**: Runtime identity loaded from `agents/<id>` with identity layers.
- **Session**: Control-plane binding of `(session_key, agent_id, project_id)`.
- **LTM Directory**: Persistent state location (`runtime.ltm_directory`).
