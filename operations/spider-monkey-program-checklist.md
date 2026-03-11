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
- Add project templates plus effective workspace service discovery so agents can inspect `/services/*` rather than assuming every local venom is always exposed.

## Phase 3: Operator Visibility

### [x] PR-SM6 (SpiderApp): mission and approval UX
- Add mission list/detail, approval queue, and recovery visibility.
- Surface persona pack and agent identity ownership details where useful.

## Phase 4: First Production Use Case

### [x] PR-SM7 (Spiderweb/SpiderApp): PR Review vertical slice
- Ingest PR context, run review missions, emit findings, optionally push fixes, and present operator-visible outcomes.
- Keep merge manual.
- Current branch scope:
  - [x] Keep Spiderweb generic with a mission `contract` bundle pointing at use-case-owned workspace state.
  - [x] Seed agent-facing mission-contract and PR Review playbook content in the Spiderweb library/templates.
  - [x] Add generic `bootstrap_contract` support to materialize mission contract files under `/nodes/local/fs/...`.
  - [x] Add a thin `pr_review` venom that can intake a PR from provider metadata, start review missions, and bootstrap their contract files on top of `missions`.
  - [x] Add review-specific venom operations for state sync plus validation/review artifact recording.
  - [x] Add `run_validation` so configured review commands execute through the terminal venom and persist durable service captures plus pass/fail status.
  - [x] Add first-class `git` and `github_pr` service venoms for checkout sync, diff inspection, provider PR sync, and top-level review publication dry runs.
  - [x] Add deeper PR Review runner/orchestration on top of that venom and contract.
  - [x] Surface PR Review-specific artifacts and outcomes in SpiderApp.
  - [x] Add seeded PR Review eval scenarios covering intake, validation success, validation failure, happy-path service orchestration/publication, and checkout-failure propagation.
  - [x] Add `github_pr ingest_event` automation so provider events emit `/global/events/sources/agent/github_pr.json` and auto-create or reuse the matching `pr_review` mission.
  - [x] Add file-backed PR Review repo onboarding through `/services/pr_review/control/configure_repo.json`, `get_repo.json`, and `list_repos.json`, backed by `/nodes/local/fs/pr-review/state/repos.json`.
  - [x] Make `github_pr ingest_event` consume configured repo defaults for auto-intake and mission bootstrap whenever the event payload does not override them.
  - [x] Add a runtime-server agentic eval that drives repo onboarding plus config-backed PR intake over Acheron strict-mode filesystem tools.
  - [x] Add `/services/pr_review/control/advance.json` as the deterministic runner step so Spider Monkey can resume missions, wait on the shared event feed, and advance sync/validation without hard-coding review judgment into the kernel.
  - [x] Add `/services/pr_review/control/save_draft.json` plus draft revision artifacts so Spider Monkey can keep evolving findings and recommendation drafts inside the workspace between events.
  - [x] Add `/services/pr_review/control/draft_review.json` so Spider Monkey can take the `ready_for_review` handoff, inspect the mission contract/artifacts, and persist a real draft revision through Acheron.

## Definition of done for this checklist

- No hatch/bootstrap compatibility path remains in active agent creation.
- Agent creation is explicit, persona-pack-based, and visible in control payloads.
- Future mission/runtime work can build on the new model without reintroducing legacy semantics.
