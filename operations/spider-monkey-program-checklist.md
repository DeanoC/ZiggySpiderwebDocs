# Spider Monkey Production Checklist

This checklist tracks the clean-cut Spider Monkey program work without reintroducing hatch/bootstrap compatibility paths.

Status legend:

- `[ ]` not started
- `[~]` in progress
- `[x]` implemented in current branch

## Phase 1: Clean-Cut Agent Foundation

### [x] PR-SM1 (Spiderweb): persona-pack agent creation
- Replace hatch-oriented agent creation with persona-pack seeding.
- Preserve selected `persona_pack` in `agent.json` and expose it in agent inventory payloads.
- Remove the old hatch-state fields from current control/runtime surfaces.

### [x] PR-SM2 (Spiderweb): managed identity ownership rules
- Keep system runtime contract memories protected.
- Keep agent-owned identity/personality memories mutable after initial seed.
- Document the code-vs-memory ownership boundaries.

### [x] PR-SM3 (Spiderweb): mission state foundation
- Add persistent mission records, state transitions, and recovery metadata.
- Add approval-gated transitions for high-impact actions.

## Phase 2: Service-Venom Runtime

### [x] PR-SM4 (SpiderProtocol/SpiderNode): durable service venom contract
- Standardize service manifests, supervision, restart, and status reporting for long-running agent work.
- Ensure Git/GitHub, terminal, search, testing, and future services are consumable as discoverable workspace services.

### [x] PR-SM5 (Spiderweb): mission-to-service binding
- Bind mission steps to service venoms instead of hard-coded workflow branches.
- Record artifacts and step events uniformly.

## Phase 3: Operator Visibility

### [x] PR-SM6 (SpiderApp): mission and approval UX
- Add mission list/detail, approval queue, and recovery visibility.
- Surface persona pack and agent identity ownership details where useful.

## Phase 4: First Production Use Case

### [~] PR-SM7 (Spiderweb/SpiderApp): PR Review vertical slice
- Ingest PR context, run review missions, emit findings, optionally push fixes, and present operator-visible outcomes.
- Keep merge manual.
- Current branch scope:
  - [x] Keep Spiderweb generic with a mission `contract` bundle pointing at use-case-owned workspace state.
  - [x] Seed agent-facing mission-contract and PR Review playbook content in the Spiderweb library/templates.
  - [ ] Add PR Review mission runner/orchestration on top of that contract.
  - [ ] Surface PR Review-specific artifacts and outcomes in SpiderApp.

## Definition of done for this checklist

- No hatch/bootstrap compatibility path remains in active agent creation.
- Agent creation is explicit, persona-pack-based, and visible in control payloads.
- Future mission/runtime work can build on the new model without reintroducing legacy semantics.
