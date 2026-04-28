# Debugging Slack Automation

Troubleshooting guide for common issues when automating Slack.

## Check Console for Errors

```bash
agent-browser console
agent-browser errors
```

## Get Current Page State

```bash
agent-browser get url
agent-browser get title
agent-browser screenshot page-state.png
```

## Element Not Found

If you can't find an element:

1. **Check it's visible**
   ```bash
   # Is the element on screen or off-screen?
   agent-browser screenshot current-state.png
   # Compare screenshot to what you expected
   ```

2. **Try expanding/scrolling**
   ```bash
   # Sidebar might need scrolling
   agent-browser scroll down 300 --selector ".p-sidebar"
   agent-browser snapshot -i
   ```

3. **Check current URL**
   ```bash
   agent-browser get url
   # Verify you're in the right section
   ```

4. **Wait for page to load**
   ```bash
   agent-browser wait --load networkidle
   agent-browser wait 1000
   agent-browser snapshot -i
   ```

## Common Ref Patterns (Session-Dependent)

These refs vary per session, but follow patterns:

| Element | Typical Ref Range | How to Find |
|---------|------------------|-------------|
| Home tab | e10-e20 | `snapshot -i \| grep "Home"` |
| DMs tab | e10-e20 | `snapshot -i \| grep "DMs"` |
| Activity tab | e10-e20 | `snapshot -i \| grep "Activity"` |
| Search | e5-e10 | `snapshot -i \| grep "Search"` |
| More unreads | e20-e30 | `snapshot -i \| grep "More unreads"` |
| Channel refs | e30+ | `snapshot -i \| grep "treeitem"` |

**Always take a fresh snapshot** to find current refs for the current session.

## Taking Notes/Capturing State

When you need to document findings from Slack:

```bash
# Take annotated screenshot (shows element numbers)
agent-browser screenshot --annotate slack-state.png

# Take full-page screenshot
agent-browser screenshot --full slack-full.png

# Get current URL for reference
agent-browser get url

# Get page title
agent-browser get title
```

## Rate Limiting

Slack may rate-limit rapid interactions. Add delays between commands if needed:

```bash
agent-browser wait 1000
# or
sleep 1
```

## Session-Specific Issues

- Screenshots and snapshots are tied to the current browser session
- If session disconnects, reconnect with `agent-browser connect 9222`
- Each new session will have different element refs