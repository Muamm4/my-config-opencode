# Common Patterns

Essential workflows for automating Electron apps.

## Inspect and Navigate

```bash
# Launch app with CDP
open -a "Slack" --args --remote-debugging-port=9222
sleep 3  # Wait for app to start

# Connect and snapshot
agent-browser connect 9222
agent-browser snapshot -i

# Identify element from snapshot output
agent-browser click @e10  # Navigate to a section

# Re-snapshot after navigation
agent-browser snapshot -i
```

## Take Screenshots

```bash
agent-browser connect 9222
agent-browser screenshot app-state.png

# Full-page screenshot
agent-browser screenshot --full full-app.png

# Annotated screenshot
agent-browser screenshot --annotate annotated-app.png
```

## Extract Data

```bash
agent-browser connect 9222
agent-browser snapshot -i
agent-browser get text @e5

# Export entire state as JSON
agent-browser snapshot --json > app-state.json
```

## Fill Forms

```bash
agent-browser connect 9222
agent-browser snapshot -i

# Fill an input field
agent-browser fill @e3 "search query"

# Submit form
agent-browser press Enter
agent-browser wait 1000

# Re-snapshot to see result
agent-browser snapshot -i
```

## Keyboard Input (Alternative)

For apps with custom input components:

```bash
# Type at current focus (no selector needed)
agent-browser keyboard type "text"

# Bypass key events for custom components
agent-browser keyboard inserttext "text"
```

## Wait for State Changes

```bash
# Wait for navigation or animation
agent-browser wait 1000
agent-browser snapshot -i
```

## Workflow Summary

1. **Launch** with `--remote-debugging-port`
2. **Connect** via `agent-browser connect`
3. **List tabs** via `agent-browser tab` (if needed)
4. **Snapshot** via `agent-browser snapshot -i`
5. **Interact** using element refs
6. **Re-snapshot** after state changes