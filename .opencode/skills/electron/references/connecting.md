# Connecting to CDP

After launching an Electron app with `--remote-debugging-port`, connect agent-browser to control it.

## Basic Connection

```bash
# Connect to a specific port
agent-browser connect 9222

# All subsequent commands now target the connected app
agent-browser snapshot -i
```

## Alternative Connection Methods

```bash
# Use --cdp flag on each command
agent-browser --cdp 9222 snapshot -i

# Auto-discover a running Chromium-based app
agent-browser --auto-connect snapshot -i
```

## Named Sessions (Multiple Apps)

```bash
# Connect to Slack on port 9222
agent-browser --session slack connect 9222

# Connect to VS Code on port 9223
agent-browser --session vscode connect 9223

# Interact with each independently
agent-browser --session slack snapshot -i
agent-browser --session vscode snapshot -i
```

## Connection Verification

After connecting, run a snapshot to verify the connection:

```bash
agent-browser connect 9222
agent-browser snapshot -i
# Expected: Interactive element list with @e0, @e1, etc.
```

## Session Persistence

Once connected via `connect`, all subsequent commands target the connected app without needing `--cdp` or `--session`. Use new `connect` commands to switch targets.