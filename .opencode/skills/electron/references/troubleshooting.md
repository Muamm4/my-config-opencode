# Troubleshooting

Common issues and solutions when automating Electron apps.

## "Connection refused" or "Cannot connect"

**Causes:**
- App not launched with `--remote-debugging-port`
- App was already running before launch
- Port conflict

**Solutions:**
```bash
# Verify app was launched with the flag
lsof -i :9222 | grep -i electron

# If port in use by another process
lsof -i :9222
# Kill the conflicting process or use a different port
```

**Important**: The app MUST be launched with the flag. If it was already running, quit it first, then relaunch.

## App launches but connect fails

**Causes:**
- App needs more startup time
- Webview initialization delay

**Solutions:**
```bash
# Wait longer before connecting
sleep 5
agent-browser connect 9222
```

## Elements not appearing in snapshot

**Causes:**
- Multiple webviews/windows
- Wrong target tab

**Solutions:**
```bash
# List all targets
agent-browser tab

# Switch to the correct target
agent-browser tab 1
agent-browser snapshot -i
```

## Cannot type in input fields

**Causes:**
- Custom input components
- Focus issues

**Solutions:**
```bash
# Type at current focus without selector
agent-browser keyboard type "search query"

# Use inserttext to bypass key events
agent-browser keyboard inserttext "search query"
```

## Additional Debugging

```bash
# Get Chromium logs
agent-browser snapshot -v

# Check CDP endpoints
curl http://localhost:9222/json
```