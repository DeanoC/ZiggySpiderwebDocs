This use case is as a PR reviewer for one of more GitHub repos, a Spider Monkey attached to this project watching for new pr, reviews changes, does fixes, reply to other reviewers etc.

## Example workflow

User tells the Spider Monkey, which GitHub repos to monitor for PR as part of the project vision
Spider Monkey set each up
 - Clones the repo (main branch) into the workspace
 - Installs and toolchain or dependency
 - Does a test build of main as a baseline
 - Sets up a GitHub monitor for new PRs to ti (existing open PRs are considered new)

 When a new PR is detected:
 - Reads the PR 
 - Switches and pull that branch
 - Uses a specialist code review brain to review it
 - Writes the reviews to the GitHub repo
 When a new PR review thread/comment occurs
 - Assess it and decides on how to resolve it
 - If coding, starts a coding sub-brain (possible using an AI CLI tool)
 - when solves comment and resolve the thread
 - Re-review similar to a new PR
 - If no actionable review threads/comments, merge it into main

## Venoms

### Timers (Required)
Ability to wait for a specified time, before continuing

### Interrupt (Required)
Ability for a push notification to wait the Spider Monkey even if waiting for something else, if not waiting should show a Spider Monkey notification.

### GitHub (Required)
A venom that can access GitHub including PR review threads and CI reports.

### Git (Required)
A venom that allows full git access to clone branches and apply fixes needed for the review

### Terminal Execution (Required)
Ability to kick off builds and see results

### AI CLI coding (Optional)
Ability to fire up a CLI AI program and inform and monitor whilst it does any coding required

### Package Installer (Optional)
Able to use the package installer of the project root system to install tools required (compilers, AI CLI tools etc.)
