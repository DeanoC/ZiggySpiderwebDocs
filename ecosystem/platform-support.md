# Platform Support

The Spider ecosystem is cross-platform in pieces, but it is not symmetric across every component.

## Primary Host Model

The current primary host model is:

- Spiderweb server on Linux or WSL
- `spiderweb-fs-mount` attaching from Linux or Windows
- worker process operating against the mounted workspace

This is the path reflected in the current Spiderweb README, external-agent guide, and mount smoke coverage.

## Windows Today

Windows support is real for the standalone mount client:

- `spiderweb-fs-mount.exe` builds on Windows
- WinFSP is the expected mount backend for drive-letter mounts
- the installer script provisions WinFSP when needed
- namespace mounts such as `mount X:` are part of the supported flow

This makes Windows useful for the final mounted workspace and desktop tooling even when the Spiderweb host runs on Linux or WSL.

## Windows Node Support

Windows node support is emerging rather than complete.

Current signals from the codebase and CI:

- there is a Windows FS source adapter
- standalone mount tests run on Windows CI
- Windows node operation tests run on Windows CI
- SpiderNode is framed as deployable on Linux, macOS, and Windows

Treat Windows nodes as an active expansion area, not yet the baseline operating model for the whole system.

## Non-Goals Right Now

These are not the current default story:

- full native Windows hosting for the main Spiderweb server
- embedding the worker's AI runtime inside Spiderweb
- using SpiderDocs as the canonical protocol source

## Practical Recommendation

If you are bringing up a new environment today:

1. host Spiderweb on Linux or WSL
2. mount the workspace with `spiderweb-fs-mount`
3. run SpiderMonkey or another worker against that mount
4. use Windows only where it adds value for the final mounted client experience
