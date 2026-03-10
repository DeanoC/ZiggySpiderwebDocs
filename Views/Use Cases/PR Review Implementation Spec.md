# PR Review Implementation Spec

This document turns [[PR Review Project]] into an implementation-oriented specification for Codex Coder.

The target is a first real vertical slice of Spider: a project-bound Spider Monkey that can watch a GitHub repository, review new pull requests, respond to follow-up review activity, and merge only when repository policy allows it.

## Scope

In scope for this spec:

- repository onboarding for PR review
- pull request event intake
- local checkout and validation
- review generation and publishing
- follow-up handling for review comments and CI changes
- merge-readiness evaluation and merge execution
- persistent task state and reporting

Out of scope for this spec:

- general feature development planning
- multi-platform social promotion
- home assistant flows
- arbitrary provider-specific review logic beyond GitHub-first support

## Product Goal

Deliver a Spider review worker that can operate continuously inside a project context and handle the full PR review loop with minimal human babysitting.

The first usable slice should support a single GitHub repository and a single Spider Monkey bound to one project.

## Relevant Runtime Constraints

This spec assumes the current Spider runtime model described elsewhere in this repo:

- agent-visible paths should use canonical namespace paths only, primarily `/agents`, `/nodes`, and project-bound `/services`
- project lifecycle and topology are managed through `/global/projects/control/*.json`
- memory is accessed through `/global/memory/control/*.json`
- repository checkout and diff inspection should use `/services/git/control/*.json` when the workspace binds it
- provider PR synchronization and top-level review publication should use `/services/github_pr/control/*.json` when the workspace binds it
- terminal execution is performed through `/global/terminal/...`
- asynchronous waiting should use `/global/events/control/wait.json` and `/global/events/next.json`
- provider-exposed tools are intentionally narrow, so orchestration should happen through filesystem services rather than ad hoc tool APIs

## Primary Actors

- User: registers repos, configures policy, and approves high-impact exceptions
- Spider primary brain: owns project-level reporting, policy checks, and escalation
- PR Review sub-brain: runs the PR review workflow for a repository or PR task
- GitHub adapter: turns GitHub webhooks, polling, or CLI/API responses into project events and actions
- Terminal service: runs checkout, build, lint, and test commands in the project namespace
- Memory service: stores durable summaries, policy state, and task handoff data

## External Dependencies

### Required

- GitHub access with read/write permission for pull requests, review comments, commit statuses, and merge actions
- Git access for clone, fetch, branch checkout, commit, push, and merge operations
- terminal execution surface for repo setup and validation
- event source capable of surfacing GitHub PR, review, and CI changes into Spider

### Optional

- AI coding worker for bounded fixes
- package installer for missing SDKs, compilers, and CLIs
- repo-specific review scripts or static analysis integrations

## Repository Policy Baseline

The current repository policy in [review-policy.md](D:/Projects/Spider/SpiderDocs/security/review-policy.md) establishes several important constraints that this implementation must respect:

- direct pushes to the default branch are not allowed
- `chatgpt-codex-connector` review is required before merge
- all Codex review threads must be resolved before merge
- auto-merge is only allowed when there are zero open Codex comments or threads

This means merge automation is policy-aware, not merely "CI is green."

## Proposed System Model

The PR review system should be built around a project-scoped controller with three layers:

1. Intake layer: converts GitHub events into normalized project tasks
2. Execution layer: performs local checkout, validation, and review/fix actions
3. Policy layer: decides whether Spider may comment, push, resolve, or merge

This keeps event collection, execution, and approval logic separate enough to debug and evolve.

## Kernel Boundary

Spiderweb should not persist PR-specific fields in its core mission schema.

Instead, the Spiderweb mission record should stay generic and only hold:

- `use_case`
- normal mission lifecycle state
- a generic mission `contract` bundle with:
  - `contract_id`
  - `context_path`
  - `state_path`
  - `artifact_root`

PR Review specifics such as repo identity, PR metadata, review findings, validation output, thread state, and merge recommendation should live in the workspace or service-managed files referenced by that contract.

The use case entrypoint should be a dedicated Venom layered above the generic mission Venom:

- `/services/github_pr/control/ingest_event.json` normalizes a GitHub PR event, emits `/global/events/sources/agent/github_pr.json`, and auto-creates or reuses the matching `pr_review` mission using a stable run id
- `/services/pr_review/control/intake.json` manually loads one provider PR into a fresh mission, bootstraps the contract files, and can persist an initial provider sync capture
- `/services/pr_review/control/start.json` creates the mission, derives contract paths, and bootstraps the initial context/state files
- `/services/pr_review/control/advance.json` is the deterministic runner step: it can resume the mission, wait on `/global/events/...`, drive the next sync/validation pass, and return a `runner.status` / `runner.next_action` pair for Spider Monkey to interpret
- `/services/pr_review/control/sync.json` updates the PR state file and can orchestrate provider sync, checkout sync, repo status capture, and diff capture through the underlying `github_pr` and `git` Venoms while persisting durable service snapshots under the review `artifact_root`
- `/services/pr_review/control/run_validation.json` opens a terminal session, runs configured review commands through `/global/terminal`, records per-command service captures, and writes a structured validation artifact with exit-code-backed pass/fail status
- `/services/pr_review/control/record_validation.json` writes validation output and refreshes the latest validation state
- `/services/pr_review/control/draft_review.json` hands the mission off to Spider Monkey for one mission-aware drafting step: the agent reads the contract/state/artifacts and is expected to persist the next draft revision through `save_draft.json`
- `/services/pr_review/control/save_draft.json` writes the agent's evolving findings/recommendation draft to the latest draft files and also snapshots each revision under a draft history directory
- `/services/pr_review/control/record_review.json` writes findings, recommendation, review-comment drafts, related review artifacts, and can optionally publish the top-level review through `github_pr`
- `/services/git/*` and `/services/github_pr/*` provide the repo/provider actions that PR Review orchestration should call instead of dropping to raw shell glue
- `/services/missions/*` remains the generic lifecycle substrate underneath it when that workspace template binds it

These `/services/*` paths are the project-facing bind targets. The canonical local implementation now originates those Venoms under `/nodes/local/venoms/*`, with `/global/*` retained as a compatibility alias. Agents should prefer the bound workspace paths whenever they exist.

## Project Configuration Model

Each project using this use case should have a PR review configuration record.

Suggested fields:

```json
{
  "project_id": "ziggy-pr",
  "use_case": "pr_review",
  "repositories": [
    {
      "repo_key": "owner/repo",
      "provider": "github",
      "default_branch": "main",
      "checkout_path": "/nodes/local/fs/pr-review/repos/owner__repo",
      "review_policy_paths": ["/nodes/local/fs/policy/pr-review.md"],
      "default_review_commands": ["pnpm test", "pnpm lint"],
      "auto_intake": true
    }
  ],
  "approval_policy": {
    "push_fix_requires_approval": false,
    "merge_requires_approval": false,
    "resolve_threads_requires_approval": false
  }
}
```

Notes:

- `checkout_path` is the canonical stored field in the current branch. `local_checkout_path` can still be accepted as an alias when onboarding older records.
- `checkout_path` is a namespace path inside the project rootfs, not a host-internal path.
- The exact storage location can evolve, but the configuration shape should stay explicit and file-backed.

## Working Filesystem Layout

The review worker needs stable project-owned working paths for clones, outputs, and task state.

Proposed project rootfs layout:

```text
/nodes/local/fs/
  pr-review/
    repos/
      owner__repo/
    runs/
      pr-123/
        summary.md
        review-findings.json
        validation.json
        thread-actions.json
    state/
      repos.json
      active-tasks.json
      merge-queue.json
```

Namespace usage around that workspace:

- `/nodes/local/fs/...` for project-owned local files
- `/global/projects/control/*.json` for project lifecycle/status
- `/global/memory/control/*.json` for durable compact summaries and search
- `/global/events/...` for waiting on GitHub or timer events
- `/global/terminal/...` for shell execution
- `/nodes/...` only when additional mounted capabilities are needed

The important rule is that all agent-facing paths remain canonical namespace paths. No internal mountpoint paths should leak into prompts, state, or operator messages.

Current control surface in the feature branch:

- `/services/pr_review/control/configure_repo.json`
- `/services/pr_review/control/get_repo.json`
- `/services/pr_review/control/list_repos.json`

Those operations own the canonical `repos.json` file under `/nodes/local/fs/pr-review/state/repos.json`.

## Persistent State Model

The first implementation does not need a complex database. File-backed state plus runtime memory is sufficient.

### Repository state

Track one record per monitored repository:

- repository identity
- current onboarding status
- last successful baseline commit
- last baseline validation result
- current webhook or polling watermark
- merge policy snapshot

### Pull request state

Track one record per active PR:

- PR number and repository key
- head SHA and base SHA
- current task state
- latest CI status snapshot
- latest review summary
- open actionable thread count
- last local validation result
- last Spider action timestamp

### Thread action state

Track one record per actionable thread:

- thread identifier
- classification: question, bug, style, CI, blocked, informational
- required action: reply, fix, escalate, ignore
- local task linkage if a fix is in progress
- resolution status

### Memory summaries

Use `/global/memory` for compact searchable summaries, for example:

- last known status of each active PR
- recurring failure patterns in a repo
- reviewer-specific preferences or common issue patterns

The full operational state should remain in project files; memory should store summaries and retrieval aids rather than act as the only source of truth.

## Event Intake Model

The implementation should normalize incoming signals into a small event set:

- `pr.opened`
- `pr.synchronized`
- `pr.reopened`
- `review.submitted`
- `review.thread_updated`
- `ci.updated`
- `timer.retry`
- `user.intervention`

Suggested event flow:

1. GitHub adapter receives a webhook, poll result, or CLI query result.
2. Adapter writes the event to `/services/github_pr/control/ingest_event.json`.
3. `github_pr` normalizes the event, emits it on `/global/events/sources/agent/github_pr.json`, and auto-creates or reuses the matching `pr_review` mission.
4. PR Review sub-brain waits using `/global/events/control/wait.json`.
5. Matching event wakes the task and loads current PR state.
6. Task decides whether the event is new work, superseded work, or ignorable noise.

The design should prefer idempotent handling because GitHub events can repeat or arrive out of order.

## PR State Machine

Suggested PR task states:

- `discovered`
- `onboarding_blocked`
- `ready_for_checkout`
- `validating`
- `reviewing`
- `awaiting_author`
- `awaiting_ci`
- `fixing`
- `ready_to_merge`
- `merged`
- `blocked`

Suggested transitions:

1. `discovered` -> `ready_for_checkout` when repo onboarding is complete
2. `ready_for_checkout` -> `validating` after branch fetch and checkout
3. `validating` -> `reviewing` when local checks finish
4. `reviewing` -> `awaiting_author` when Spider requests changes or asks questions
5. `reviewing` -> `awaiting_ci` when code review is complete but required checks are pending
6. `awaiting_author` -> `fixing` when Spider is allowed to apply a fix itself
7. `fixing` -> `validating` after a branch update
8. `awaiting_ci` -> `ready_to_merge` when policy checks and CI both pass
9. `ready_to_merge` -> `merged` after successful merge
10. Any state -> `blocked` when permissions, environment, or ambiguity stop progress

## Review Execution Flow

### 1. Repository onboarding

For each configured repository:

1. Clone or refresh the default branch into `/nodes/local/fs/pr-review/repos/...`
2. Install required dependencies and tools
3. Run baseline validation commands
4. Persist onboarding result and failure details
5. Refuse active PR review for repos without a known-good baseline unless policy explicitly allows degraded review mode

### 2. PR checkout

When a PR event is accepted:

1. Fetch the PR branch and current base branch
2. Create or refresh a local worktree or checkout for the PR
3. Record head SHA, base SHA, changed files, and author metadata
4. Skip redundant work if the exact head SHA was already fully processed and no new comments/CI events exist

### 3. Validation

Run the repo's configured review commands, split into:

- mandatory checks required before publishing a positive review
- optional diagnostics that enrich the review but do not block all feedback

Persist:

- exit codes
- truncated logs or referenced log paths
- start/end timestamps
- whether the failure appears environmental, flaky, or branch-caused

### 4. Review synthesis

Spider should produce structured review output before posting to GitHub.

Suggested output model:

```json
{
  "summary": "High-level review result",
  "decision": "approve | comment | request_changes",
  "findings": [
    {
      "severity": "high | medium | low",
      "type": "correctness | regression | security | test_gap | maintainability",
      "path": "src/example.ts",
      "line": 42,
      "message": "Why this matters and what should change"
    }
  ],
  "suggested_actions": [
    "Add a regression test for X",
    "Handle null response from Y"
  ]
}
```

This keeps local reasoning structured even if the published GitHub review is shorter.

### 5. Publishing review output

Spider publishes one of:

- approval
- request changes
- comment-only review

Publishing rules:

- do not approve when required checks are failing or when actionable high-severity findings remain
- do not request changes for noise-level issues that should be comments
- attach line comments when the finding is localized and actionable
- prefer one coherent review over many fragmented comments when possible

## Comment and Thread Handling

When new review discussion appears:

1. Load the updated thread and classify the request
2. Decide whether Spider should:
   - reply with explanation
   - apply a code fix
   - wait for the PR author
   - escalate to the user
3. If fixing:
   - update the PR branch
   - rerun the narrowest relevant validation
   - reply with what changed
4. Resolve the thread only when:
   - the issue is materially addressed
   - the repository policy allows Spider to resolve it
   - no new evidence suggests the issue still exists

The system should avoid "auto-resolve because a commit happened." Resolution must be evidence-based.

## CI Handling

CI updates should be normalized into status categories:

- `pending`
- `success`
- `failed_branch`
- `failed_environment`
- `unknown`

Suggested handling:

- `pending`: remain in `awaiting_ci`
- `success`: reevaluate merge readiness
- `failed_branch`: open or update a fix task if Spider is allowed to act
- `failed_environment`: report and retry only within defined limits
- `unknown`: report ambiguity and avoid merge

## Merge Readiness Policy

Before merging, Spider must verify:

- branch protection requirements are satisfied
- required reviews are present
- all Codex review threads are resolved
- required CI checks are green
- no newer PR commit invalidated the local review result
- project policy allows merge without extra user approval

If any check fails, the PR remains unmerged with an explicit blocking reason in task state and user-visible reporting.

## Permissions and Guardrails

### Required guardrails

- Never push directly to the default branch.
- Never merge based solely on a stale local state snapshot.
- Never resolve review threads without evidence that the issue is fixed.
- Never treat missing tooling or broken onboarding as a silent pass.
- Never leak internal runtime paths in comments or logs intended for users.

### Approval gates

The policy layer should be able to require user approval for:

- first-time repo onboarding
- applying code changes on behalf of the PR author
- resolving another reviewer's thread
- merge execution
- retries after repeated environmental failures

## Reporting Model

The primary brain should maintain a readable status summary per active PR.

Suggested summary fields:

- repo and PR number
- current state
- latest decision
- blocking reasons
- last validation result
- open thread count
- next expected event

Suggested outputs:

- machine-readable JSON state in `/nodes/local/fs/pr-review/state/...`
- human-readable markdown summaries in `/nodes/local/fs/pr-review/runs/pr-<n>/summary.md`
- searchable compact summaries in runtime memory

## Failure Handling

Spider should distinguish between:

- repo onboarding failure
- transient terminal failure
- missing permissions
- GitHub API failure
- merge policy failure
- ambiguous review request

Each should produce a different recovery path rather than collapsing into a generic "review failed."

## Milestones

### M0. Skeleton and project config

Deliver:

- project config schema for PR review
- repository registration file
- empty task runner that can load config and persist repo state

### M1. Single-repo onboarding

Deliver:

- clone or refresh one repo
- run baseline install and validation commands
- persist onboarding state and logs

Acceptance:

- Spider can tell the user whether the repo is review-ready and why

### M2. PR intake and local review

Deliver:

- ingest one PR event
- fetch and checkout PR branch
- run configured review commands
- produce a local structured review result

Acceptance:

- for a known PR, Spider can produce a review summary and findings without posting them yet

### M3. GitHub review publishing

Deliver:

- map structured review output to GitHub review API actions
- publish comment-only, approve, or request-changes reviews
- record published review identifiers in state

Acceptance:

- Spider can post a technically useful review to GitHub for one PR

### M4. Thread follow-up and bounded fixes

Deliver:

- ingest review thread updates
- classify actions
- apply a small fix when allowed
- rerun targeted validation
- reply in-thread and update task state

Acceptance:

- Spider can close a simple review loop without user babysitting

### M5. Merge readiness and merge

Deliver:

- branch protection and required-review evaluation
- unresolved-thread check
- merge action with policy gate

Acceptance:

- Spider merges only when repository policy is truly satisfied

## Thin Slice Recommendation

The first thin slice should stop at `M3`.

That yields a usable demonstration:

1. onboard one repo
2. detect or manually load one PR
3. run review commands locally
4. publish a structured GitHub review

This is enough to prove that Spider can operate as a real PR reviewer before attempting auto-fix and merge automation.

## Current Seeded Eval Coverage

The current branch now includes seeded PR Review regression scenarios in Spiderweb test coverage. These are not the final long-running agent harness, but they provide a first agentic contract layer for the use case:

- manual provider intake bootstrapping through the `pr_review` Venom
- validation success propagation through terminal-backed review commands
- validation failure propagation on non-zero command exit codes
- happy-path review setup with provider sync, checkout sync, repo status capture, diff capture, and dry-run top-level review publication
- failure-path checkout sync propagation, ensuring the `pr_review` Venom reports a failed status while still persisting the service capture artifact for operator or agent diagnosis

The purpose of these seeded evals is to lock in the mission/service behavior before the fully autonomous Spider Monkey review loop is layered on top.

## Thin Slice Status

The current implementation now reaches the intended `M3` thin-slice boundary:

1. manually load one PR through `/services/pr_review/control/intake.json`
2. sync provider and checkout state through `/services/pr_review/control/sync.json`
3. run configured local review commands through `/services/pr_review/control/run_validation.json`
4. publish a structured top-level GitHub review through `/services/pr_review/control/record_review.json` with `publish_review`

## Open Questions

- Should GitHub intake start with webhooks, polling, or `gh`-driven sync?
- Should each active PR run in a separate sub-brain immediately, or should the first version process tasks serially?
- How much review reasoning should be retained in memory versus only in project files?
- What is the minimum repo setup contract: explicit commands only, or convention-based defaults?
- Which actions are safe to auto-approve by default versus opt-in only?

## Implementation References

- [PR Review Project.md](D:/Projects/Spider/SpiderDocs/Views/Use%20Cases/PR%20Review%20Project.md)
- [Codex Coder Backlog.md](D:/Projects/Spider/SpiderDocs/Views/Use%20Cases/Codex%20Coder%20Backlog.md)
- [Acheron Namespace](D:/Projects/Spider/SpiderDocs/protocols/acheron-worldfs.md)
- [Project RootFS and Namespace Unification RFC](D:/Projects/Spider/SpiderDocs/architecture/rfc-project-rootfs-and-namespace-unification.md)
- [Agent + Project Policy](D:/Projects/Spider/SpiderDocs/runtime/agent-project-policy.md)
- [Review Policy](D:/Projects/Spider/SpiderDocs/security/review-policy.md)
- [Memory Model](D:/Projects/Spider/SpiderDocs/design/agents/memory.md)
- [Tool System](D:/Projects/Spider/SpiderDocs/runtime/tool-system.md)
