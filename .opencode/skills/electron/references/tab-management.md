# Tab Management

Electron apps often have multiple windows, webviews, or render processes. Use tab commands to list and switch between them.

## List Targets

```bash
# List all available targets (windows, webviews, etc.)
agent-browser tab
```

Output example:
```
  0: [page]    Slack - Main Window     https://app.slack.com/
  1: [webview] Embedded Content        https://example.com/widget
```

## Switch Between Targets

```bash
# Switch to a specific tab by index
agent-browser tab 2

# Switch by URL pattern
agent-browser tab --url "*settings*"
```

## Webview Support

Electron `<webview>` elements are automatically discovered and controlled like regular pages. Webviews appear as separate targets with `type: "webview"`.

```bash
# Connect to running Electron app
agent-browser connect 9222

# List targets
agent-browser tab

# Switch to webview
agent-browser tab 1

# Interact with the webview normally
agent-browser snapshot -i
agent-browser click @e3
agent-browser screenshot webview.png
```

**Note**: Webview support works via raw CDP connection.

## Common Scenarios

- **Multiple windows**: Slack opens separate windows for different workspaces
- **Settings panels**: Often render in their own webview
- **Popout chats**: Discord opens threads in separate windows

## Best Practice

Always call `agent-browser tab` after connecting to see all available targets before taking a snapshot.