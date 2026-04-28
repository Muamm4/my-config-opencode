# Best Practices for Slack Automation

Guidelines and principles for effective Slack browser automation.

## Core Principles

1. **Connect to existing sessions**: Use `agent-browser connect 9222` if Slack is already open. This is faster than opening a new browser.

2. **Take snapshots before clicking**: Always `snapshot -i` to identify refs before clicking buttons.

3. **Re-snapshot after navigation**: After navigating to a new channel or section, take a fresh snapshot to find new refs.

4. **Use JSON snapshots for parsing**: When you need to extract structured data, use `snapshot --json` for machine-readable output.

5. **Pace interactions**: Add `sleep 1` or `wait 1000` between rapid interactions to let the UI update.

6. **Check accessibility tree**: The accessibility tree shows what screen readers (and your automation) can see. If an element isn't in the snapshot, it may be hidden or require scrolling.

7. **Scroll in sidebar**: Use `agent-browser scroll down 300 --selector ".p-sidebar"` to scroll within the Slack sidebar if channel list is long.

## Limitations

- **Cannot access Slack API**: This uses browser automation, not the Slack API. No OAuth, webhooks, or bot tokens needed.
- **Session-specific**: Screenshots and snapshots are tied to the current browser session.
- **Rate limiting**: Slack may rate-limit rapid interactions. Add delays between commands if needed.
- **Workspace-specific**: You interact with your own workspace -- no cross-workspace automation.

## Evidence Capture Best Practices

Always capture evidence of findings:

```bash
# Annotated screenshot (shows element numbers)
agent-browser screenshot --annotate finding.png

# Full page screenshot
agent-browser screenshot --full full-page.png

# Get URL and title for reference
agent-browser get url
agent-browser get title
```

## Keyboard Shortcuts

Type `?` in Slack for a list of keyboard shortcuts. Useful ones for automation:
- `j` - Jump to unread
- `k` / `j` - Navigate messages (when in focus mode)
- `/` - Command palette

## Example: Full Unread Check Script

```bash
#!/bin/bash

# Connect to Slack
agent-browser connect 9222

# Take initial snapshot
echo "=== Checking Slack unreads ==="
agent-browser snapshot -i > snapshot.txt

# Check Activity tab for unreads
agent-browser click @e14  # Activity tab
agent-browser wait 1000
agent-browser screenshot activity.png
ACTIVITY_RESULT=$(agent-browser get text @e_main_area)
echo "Activity: $ACTIVITY_RESULT"

# Check DMs
agent-browser click @e13  # DMs tab
agent-browser wait 1000
agent-browser screenshot dms.png

# Check unread channels in sidebar
agent-browser click @e21  # More unreads button
agent-browser wait 500
agent-browser snapshot -i > unreads-expanded.txt
agent-browser screenshot unreads.png

# Summary
echo "=== Summary ==="
echo "See activity.png, dms.png, and unreads.png for full details"
```

## External References

- **Slack docs**: https://slack.com/help
- **Web experience**: https://app.slack.com
- **Keyboard shortcuts**: Type `?` in Slack for shortcut list