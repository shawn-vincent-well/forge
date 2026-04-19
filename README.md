# forge

Spin up Claude Code agents in isolated git worktrees.

## Install

Add `~/projects/forge/bin` to your PATH:

```bash
export PATH="$HOME/projects/forge/bin:$PATH"
```

Requires: `git`, `tmux`, `bash`

## Usage

```bash
# Create a new agent session (defaults to feature type)
forge new "patient-context"
forge new bug "stale-websocket"
forge new experiment "try-zustand"

# Pass an initial command to Claude
forge new "review-pr-42" -- /review-pr 42

# List all sessions
forge ls

# Reconnect to a session (supports partial matching)
forge attach patient-context

# Tear down when done
forge teardown feature/patient-context

# Force teardown with unpushed work
forge teardown --force experiment/try-zustand
```

## How it works

`forge new` does the following:

1. Creates a git worktree branching from the base branch (develop/main)
2. Runs `npm install` if a package.json exists
3. Copies `.env` and `.mcp.json` from the main worktree
4. Generates a `CLAUDE.local.md` with worktree context
5. Creates a tmux session with two windows: Claude Code + shell
6. Attaches you to the session (or detaches if inside tmux)

## Types

- `feature` (default) — feature branch, long-lived, may produce PRs
- `bug` — bug fix or small change, short-lived
- `experiment` — throwaway exploration, may never merge

## Project detection

`forge` is project-aware. Run it from any git repo and it will:

- Detect the repo root and project name
- Find the base branch (develop > main > master)
- Create worktrees under `<parent>/<project>-worktrees/`
