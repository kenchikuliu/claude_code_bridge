# Session Cleanup Tools

## Problem

When CCB exits unexpectedly (crash, `Ctrl+C`, terminal closed), tmux backend sessions (`codex-*`, `gemini-*`, `opencode-*`) remain running as zombie processes. Over time, these accumulate and consume system resources.

## Solution

Two utility scripts to manage CCB sessions effectively:

### 1. `ccb-start` - Smart Launcher

Automatically cleans up zombie sessions and starts CCB with proper initialization.

**Features:**
- Intelligent zombie detection (only kills sessions whose parent process is dead)
- Auto-detects if running inside tmux
- Handles conda environment activation
- Uses current directory (not hardcoded path)
- Per-directory lock file management
- Syncs tmux environment variables

**Usage:**
```bash
# Start CCB in current directory
ccb-start

# Start with custom backends
ccb-start codex gemini

# Works from any directory
cd ~/my-project && ccb-start
cd ~/another-project && ccb-start  # Different instance
```

**What it does:**
1. Scans for `codex-*`, `gemini-*`, `opencode-*` tmux sessions
2. Checks if parent PID is still alive
3. Kills only orphaned sessions
4. Checks for existing CCB instance in current directory
5. Creates tmux session if needed
6. Activates conda environment
7. Syncs environment variables to tmux
8. Launches CCB with default backends

### 2. `ccb-cleanup` - Manual Cleanup

Interactive tool to manually clean up zombie sessions.

**Usage:**
```bash
ccb-cleanup
```

**Output:**
```
🔍 检查僵尸 tmux sessions...
⚠️  发现 5 个僵尸 sessions
是否清理这些 sessions? (y/N)
```

## Installation

These scripts are in the `bin/` directory. The `install.sh` script will add them to your PATH.

## Why Zombies Happen

CCB creates separate tmux sessions for each backend. When CCB's main process dies unexpectedly:

1. Main CCB process terminates
2. Backend tmux sessions continue running (by design)
3. Sessions become orphaned without cleanup

**Normal exit** (proper cleanup):
- Using CCB's built-in exit command
- `ccb kill` command

**Abnormal exit** (creates zombies):
- `Ctrl+C` on main process
- Terminal window closed
- System crash or forced kill

## Technical Details

### Zombie Detection Algorithm

```bash
# Extract PID from session name: codex-2768391-xxx → 2768391
PID=$(echo "$session" | cut -d- -f2)

# Check if process exists
if ! ps -p "$PID" > /dev/null 2>&1; then
    # Process dead → zombie session
    ZOMBIE_SESSIONS+=("$session")
fi
```

### Lock File Mechanism

CCB uses per-directory lock files:
```
~/.cache/ccb/projects/<dir_hash>/lock
```

Each directory gets a unique hash, allowing multiple CCB instances in different directories without conflicts.

### Environment Variable Synchronization

`ccb-start` syncs environment variables to tmux:
```bash
tmux set-environment -g ANTHROPIC_API_KEY "$ANTHROPIC_API_KEY"
tmux set-environment -g ANTHROPIC_BASE_URL "$ANTHROPIC_BASE_URL"
```

This ensures API credentials and proxy settings propagate correctly to new panes.

## Troubleshooting

**"CCB already running" error:**
```bash
ccb-start
# → ❌ CCB 已经在运行 (PID: 12345)
```

Solutions:
1. Connect to existing instance in another terminal
2. Use `ccb-cleanup` to remove stale locks
3. Change to a different directory

**Conda activation fails in tmux:**

The script explicitly sources conda:
```bash
source "$HOME/anaconda3/etc/profile.d/conda.sh"
```

If using a different conda path, set the `CONDA_BASE` variable in the script.

**Environment variables not updating:**

Ensure `~/.tmux.conf` includes:
```tmux
set-option -g update-environment "ANTHROPIC_API_KEY ANTHROPIC_BASE_URL ..."
```

Then reload: `tmux source-file ~/.tmux.conf`

## Best Practices

1. **Use `ccb-start` for launching**: Ensures clean state
2. **Run `ccb-cleanup` weekly**: Prevents zombie accumulation
3. **Exit properly**: Use CCB's exit command, not `Ctrl+C`
4. **One instance per directory**: Avoid conflicts

## Integration with ~/.tmux.conf

For automatic environment variable propagation, add to `~/.tmux.conf`:

```tmux
# Auto-update these variables in new panes/windows
set-option -g update-environment "\
  ANTHROPIC_API_KEY \
  ANTHROPIC_BASE_URL \
  ANTHROPIC_AUTH_TOKEN \
  HTTP_PROXY \
  HTTPS_PROXY \
  ALL_PROXY \
  NO_PROXY"

# Use login shell for new panes (loads .bashrc)
set -g default-command "${SHELL} -l"
```

Then: `tmux source-file ~/.tmux.conf`

## Contributing

To add session cleanup for new backends (e.g., `droid-*`):

1. Update regex in `ccb-start` and `ccb-cleanup`:
   ```bash
   grep -E "codex-|gemini-|opencode-|droid-"
   ```

2. Test with zombie sessions:
   ```bash
   # Create test zombie
   tmux new-session -d -s "test-12345-abc"
   # Should be detected and cleaned
   ```
