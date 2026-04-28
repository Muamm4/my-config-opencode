# Getting Started with Slack Automation

Quick start guide for connecting to and setting up Slack browser automation.

## Connecting to Slack

### Connect to Existing Session

Use when Slack is already open in a browser:

```bash
agent-browser connect 9222
```

Port 9222 is typical for existing browser sessions.

### Open Slack Fresh

If Slack isn't running:

```bash
agent-browser open https://app.slack.com
```

## Initial Snapshot

After connecting, always take an interactive snapshot to see available elements:

```bash
agent-browser snapshot -i
```

This returns elements with refs (`@e1`, `@e2`, etc.) that you can interact with.

## Basic Workflow

1. **Connect/Navigate**: Open or connect to Slack
2. **Snapshot**: Get interactive elements with refs
3. **Navigate**: Click tabs, expand sections, or navigate to specific channels
4. **Extract/Interact**: Read data or perform actions
5. **Screenshot**: Capture evidence of findings

## Example: Quick Unread Check

```bash
# Connect to Slack
agent-browser connect 9222

# Take snapshot to locate unreads button
agent-browser snapshot -i

# Look for "More unreads" button
agent-browser click @e21  # Ref for "More unreads" button
agent-browser screenshot slack-unreads.png
```

## Prerequisites

- `agent-browser` CLI installed
- Chrome/Chromium with remote debugging enabled (port 9222)
- Access to a Slack workspace

## Next Steps

- See [navigation.md](./navigation.md) for sidebar and tab navigation
- See [common-tasks.md](./common-tasks.md) for specific automation patterns