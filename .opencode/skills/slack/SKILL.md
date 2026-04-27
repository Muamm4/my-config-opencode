---
name: slack
description: Interact with Slack workspaces using browser automation. Use when the user needs to check unread channels, navigate Slack, send messages, extract data, find information, search conversations, or automate any Slack task. Triggers include "check my Slack", "what channels have unreads", "send a message to", "search Slack for", "extract from Slack", "find who said", or any task requiring programmatic Slack interaction.
allowed-tools: Bash(agent-browser:*), Bash(npx agent-browser:*)
---

# Slack Automation

Interact with Slack workspaces to check messages, extract data, and automate common tasks.

## Quick Start

Connect to an existing Slack browser session or open Slack:

```bash
# Connect to existing session on port 9222 (typical for already-open Slack)
agent-browser connect 9222

# Or open Slack if not already running
agent-browser open https://app.slack.com
```

Then take a snapshot to see what's available:

```bash
agent-browser snapshot -i
```

## Core Workflow

1. **Connect/Navigate**: Open or connect to Slack
2. **Snapshot**: Get interactive elements with refs (`@e1`, `@e2`, etc.)
3. **Navigate**: Click tabs, expand sections, or navigate to specific channels
4. **Extract/Interact**: Read data or perform actions
5. **Screenshot**: Capture evidence of findings

```bash
# Example: Check unread channels
agent-browser connect 9222
agent-browser snapshot -i
# Look for "More unreads" button
agent-browser click @e21  # Ref for "More unreads" button
agent-browser screenshot slack-unreads.png
```

## Common Tasks

### Checking Unread Messages

```bash
# Connect to Slack
agent-browser connect 9222

# Take snapshot to locate unreads button
agent-browser snapshot -i

# Look for:
# - "More unreads" button (usually near top of sidebar)
# - "Unreads" toggle in Activity tab (shows unread count)
# - Channel names with badges/bold text indicating unreads

# Navigate to Activity tab to see all unreads in one view
agent-browser click @e14  # Activity tab (ref may vary)
agent-browser wait 1000
agent-browser screenshot activity-unreads.png

# Or check DMs tab
agent-browser click @e13  # DMs tab
agent-browser screenshot dms.png

# Or expand "More unreads" in sidebar
agent-browser click @e21  # More unreads button
agent-browser wait 500
agent-browser screenshot expanded-unreads.png
```

### Navigating to a Channel

```bash
# Search for channel in sidebar or by name
agent-browser snapshot -i

# Look for channel name in the list (e.g., "engineering", "product-design")
# Click on the channel treeitem ref
agent-browser click @e94  # Example: engineering channel ref
agent-browser wait --load networkidle
agent-browser screenshot channel.png
```

### Finding Messages/Threads

```bash
# Use Slack search
agent-browser snapshot -i
agent-browser click @e5  # Search button (typical ref)
agent-browser fill @e_search "keyword"
agent-browser press Enter
agent-browser wait --load networkidle
agent-browser screenshot search-results.png
```

### Extracting Channel Information

```bash
# Get list of all visible channels
agent-browser snapshot --json > slack-snapshot.json

# Parse for channel names and metadata
# Look for treeitem elements with level=2 (sub-channels under sections)
```

### Checking Channel Details

```bash
# Open a channel
agent-browser click @e_channel_ref
agent-browser wait 1000

# Get channel info (members, description, etc.)
agent-browser snapshot -i
agent-browser screenshot channel-details.png

# Scroll through messages
agent-browser scroll down 500
agent-browser screenshot channel-messages.png
```

### Taking Notes/Capturing State

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

## Sidebar Structure

Understanding Slack's sidebar helps you navigate efficiently:

```
- Threads
- Huddles
- Drafts & sent
- Directories
- [Section Headers - External connections, Starred, Channels, etc.]
  - [Channels listed as treeitems]
- Direct Messages
  - [DMs listed]
- Apps
  - [App shortcuts]
- [More unreads] button (toggles unread channels list)
```

Key refs to look for:
- `@e12` - Home tab (usually)
- `@e13` - DMs tab
- `@e14` - Activity tab
- `@e5` - Search button
- `@e21` - More unreads button (varies by session)

## Tabs in Slack

After clicking on a channel, you'll see tabs:
- **Messages** - Channel conversation
- **Files** - Shared files
- **Pins** - Pinned messages
- **Add canvas** - Collaborative canvas
- Other tabs depending on workspace setup

Click tab refs to switch views and get different information.

## Extracting Data from Slack

### Get Text Content

```bash
# Get a message or element's text
agent-browser get text @e_message_ref
```

### Parse Accessibility Tree

```bash
# Full snapshot as JSON for programmatic parsing
agent-browser snapshot --json > output.json

# Look for:
# - Channel names (name field in treeitem)
# - Message content (in listitem/document elements)
# - User names (button elements with user info)
# - Timestamps (link elements with time info)
```

### Count Unreads

```bash
# After expanding unreads section:
agent-browser snapshot -i | grep -c "treeitem"
# Each treeitem with a channel name in the unreads section is one unread
```

## Best Practices

- **Connect to existing sessions**: Use `agent-browser connect 9222` if Slack is already open. This is faster than opening a new browser.
- **Take snapshots before clicking**: Always `snapshot -i` to identify refs before clicking buttons.
- **Re-snapshot after navigation**: After navigating to a new channel or section, take a fresh snapshot to find new refs.
- **Use JSON snapshots for parsing**: When you need to extract structured data, use `snapshot --json` for machine-readable output.
- **Pace interactions**: Add `sleep 1` between rapid interactions to let the UI update.
- **Check accessibility tree**: The accessibility tree shows what screen readers (and your automation) can see. If an element isn't in the snapshot, it may be hidden or require scrolling.
- **Scroll in sidebar**: Use `agent-browser scroll down 300 --selector ".p-sidebar"` to scroll within the Slack sidebar if channel list is long.

## Limitations

- **Cannot access Slack API**: This uses browser automation, not the Slack API. No OAuth, webhooks, or bot tokens needed.
- **Session-specific**: Screenshots and snapshots are tied to the current browser session.
- **Rate limiting**: Slack may rate-limit rapid interactions. Add delays between commands if needed.
- **Workspace-specific**: You interact with your own workspace -- no cross-workspace automation.

## Debugging

### Check console for errors

```bash
agent-browser console
agent-browser errors
```

### Get current page state

```bash
agent-browser get url
agent-browser get title
agent-browser screenshot page-state.png
```

## Example: Full Unread Check

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

## References

- **Slack docs**: https://slack.com/help
- **Web experience**: https://app.slack.com
- **Keyboard shortcuts**: Type `?` in Slack for shortcut list

--- references/slack-tasks.md ---

# Common Slack Tasks & Patterns

Reference guide for common automations and data extraction patterns when interacting with Slack.

## Task: Check All Unread Messages

### Goal
Determine which channels and DMs have unread messages.

### Steps

1. **Connect to Slack**
   ```bash
   agent-browser connect 9222
   ```

2. **Check Activity Tab**
   - Take snapshot: `agent-browser snapshot -i`
   - Look for Activity tab ref (usually `@e14`)
   - Click: `agent-browser click @e14`
   - Wait: `agent-browser wait 1000`
   - If you see "You've read all the unreads", you have no unread messages
   - Screenshot: `agent-browser screenshot activity.png`

3. **Check DMs**
   - Click DMs tab ref (usually `@e13`)
   - Look for "Unreads" toggle/badge
   - Count visible conversations with indicators

4. **Check Channels**
   - Look for "More unreads" button (usually in sidebar)
   - Click it to expand list of channels with unreads
   - Screenshot the expanded view
   - Parse channel names from snapshot

5. **Summary**
   - Activity + DMs + Channels = complete unread picture

### Evidence Capture
- Screenshot of Activity tab
- Screenshot of DMs
- Screenshot of expanded unreads sidebar

---

## Task: Find All Channels in Workspace

### Goal
Get a complete list of all channels you have access to.

### Steps

1. **Navigate to Channels section**
   ```bash
   agent-browser connect 9222
   agent-browser snapshot -i
   ```

2. **Look for "Channels" treeitem**
   - This is usually a collapsed section header
   - Click to expand if collapsed
   - Screenshot: `agent-browser screenshot all-channels.png`

3. **Scroll through sidebar**
   ```bash
   # If the list is long, scroll within the sidebar
   agent-browser scroll down 500 --selector ".p-sidebar"
   agent-browser screenshot channels-page-2.png
   ```

4. **Parse snapshot for channel list**
   ```bash
   agent-browser snapshot --json > channels.json
   # Search JSON for treeitem elements with level=2 under "Channels" section
   ```

### Evidence
- JSON snapshot with all channel refs
- Screenshots of channel list
- Count of total channels

---

## Task: Search for Messages Containing Keywords

### Goal
Find all messages/threads mentioning specific terms.

### Steps

1. **Open search**
   ```bash
   agent-browser snapshot -i
   # Find Search button ref (usually @e5)
   agent-browser click @e5
   agent-browser wait 500
   ```

2. **Enter search term**
   ```bash
   # Identify search input ref from snapshot
   agent-browser fill @e_search_input "your keyword"
   agent-browser press Enter
   agent-browser wait --load networkidle
   ```

3. **Capture results**
   ```bash
   agent-browser screenshot search-results.png
   agent-browser snapshot -i > search-snapshot.txt
   ```

4. **Parse results**
   - Look for result items in snapshot
   - Extract message content, sender, channel, timestamp
   - Follow links to view full context

### Filters
Slack search supports filters:
- `in:channel-name` - Search in specific channel
- `from:@user` - Messages from specific user
- `before:2026-02-25` - Messages before date
- `after:2026-02-20` - Messages after date
- `has:file` - Messages with files
- `has:emoji` - Messages with reactions

Example search: `"bug report" in:engineering from:@alice after:2026-02-20`

---

## Task: Monitor a Specific Channel for Activity

### Goal
Watch a channel and capture new messages/engagement.

### Steps

1. **Navigate to channel**
   ```bash
   agent-browser connect 9222
   agent-browser snapshot -i
   # Find channel ref from sidebar
   agent-browser click @e_channel_ref
   agent-browser wait --load networkidle
   ```

2. **Check channel info**
   - Screenshot channel details: `agent-browser screenshot channel-header.png`
   - Look for member count, description, topic

3. **View messages**
   ```bash
   # Jump to recent/unread
   agent-browser press j  # Jump to unread in Slack
   agent-browser wait 500
   agent-browser screenshot recent-messages.png
   ```

4. **Scroll to see more**
   ```bash
   agent-browser scroll down 500
   agent-browser screenshot more-messages.png
   ```

5. **Check threads**
   - Click on messages with thread indicators
   - View replies in thread view
   - Screenshot: `agent-browser screenshot thread.png`

### Evidence
- Channel info screenshot
- Message history screenshots
- Thread examples

---

## Task: Extract User Information from a Conversation

### Goal
Find who said what, when, and in what context.

### Steps

1. **Navigate to relevant channel or DM**
   ```bash
   agent-browser click @e_conversation_ref
   agent-browser wait 1000
   ```

2. **Take snapshot with context**
   ```bash
   agent-browser snapshot --json > conversation.json
   ```

3. **Find message blocks**
   - In JSON, look for document/listitem elements
   - These contain: user name (button), timestamp (link), message text, reactions

4. **Extract structured data**
   - User: Found in button element with username
   - Time: Found in link with timestamp
   - Content: Text content of message
   - Reactions: Buttons showing emoji counts

5. **Screenshot key messages**
   ```bash
   agent-browser screenshot important-message.png
   agent-browser screenshot --annotate annotated-message.png
   ```

---

## Task: Track Reactions to a Message

### Goal
See who reacted to a message and with what emoji.

### Steps

1. **Find message with reactions**
   ```bash
   agent-browser snapshot -i
   # Look for "N reaction(s)" buttons in messages
   ```

2. **Click reaction button to expand**
   ```bash
   agent-browser click @e_reaction_button
   agent-browser wait 500
   ```

3. **Capture reaction details**
   ```bash
   agent-browser screenshot reactions.png
   # You'll see emoji, count, and list of users who reacted
   ```

4. **Extract data**
   - Emoji used
   - Number of people who reacted
   - User names (if visible in popup)

---

## Task: Find and Review Pinned Messages

### Goal
See messages that have been pinned in a channel.

### Steps

1. **Open a channel**
   ```bash
   agent-browser click @e_channel_ref
   agent-browser wait 1000
   agent-browser snapshot -i
   ```

2. **Click Pins tab**
   - In channel view, look for "Pins" tab ref (usually near Messages, Files tabs)
   - Click it: `agent-browser click @e_pins_tab`
   - Wait: `agent-browser wait 500`

3. **View pinned messages**
   ```bash
   agent-browser screenshot pins.png
   agent-browser snapshot -i > pins-snapshot.txt
   ```

4. **Review each pin**
   - Click pin to see context
   - Note who pinned it, when, and why
   - Screenshot: `agent-browser screenshot pin-detail.png`

---

## Pattern: Extract Timestamp from Link

In Slack snapshot, message timestamps appear as links. Example:
```
- link "Feb 25th at 10:26:22 AM" [ref=e151]
  - /url: https://vercel.slack.com/archives/C0A5RTN0856/p1772036782543189
```

The URL contains the timestamp in the fragment (`p1772036782543189`). This is a Slack message ID that uniquely identifies the message.

---

## Pattern: Understanding Channel/Thread Structure

```
- treeitem "channel-name" [ref=e94] [level=2]
  - group: (contains channel metadata or sub-items)
```

- **level=1**: Section headers (External connections, Starred, Channels, etc.)
- **level=2**: Individual channels/items within sections
- **level=3+**: Nested sub-items (rare in sidebar)

---

## Common Ref Patterns (Session-Dependent)

These refs vary per session, but follow patterns:

| Element | Typical Ref Range | How to Find |
|---------|------------------|------------|
| Home tab | e10-e20 | `snapshot -i \| grep "Home"` |
| DMs tab | e10-e20 | `snapshot -i \| grep "DMs"` |
| Activity tab | e10-e20 | `snapshot -i \| grep "Activity"` |
| Search | e5-e10 | `snapshot -i \| grep "Search"` |
| More unreads | e20-e30 | `snapshot -i \| grep "More unreads"` |
| Channel refs | e30+ | `snapshot -i \| grep "treeitem"` |

**Always take a fresh snapshot** to find current refs for the current session.

---

## Debugging: Element Not Found

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

--- templates/slack-report-template.md ---

# Slack Analysis Report

**Date**: [DATE]
**Workspace**: [WORKSPACE_NAME]
**Analyst**: [YOUR_NAME]
**Scope**: [WHAT_YOU_ANALYZED]

## Summary

### Unread Counts
- **Activity**: [NUMBER] unreads
- **Direct Messages**: [NUMBER] unreads
- **Channels**: [NUMBER] channels with unreads

### Key Findings
- [FINDING 1]
- [FINDING 2]
- [FINDING 3]

---

## Unread Channels

List of channels with unread messages:

| Channel | Unread Count | Last Activity | Notes |
|---------|-------------|---------------|-------|
| #engineering | 12 | Today 2:45 PM | Active discussion thread |
| #announcements | 3 | Yesterday 5:30 PM | Team updates |
| #random | 5 | Today 11:20 AM | Various topics |

---

## Unread Direct Messages

| User/Group | Message Count | Last Message | Preview |
|------------|--------------|--------------|---------|
| @alice | 2 | Today 3:15 PM | "Are you free to..." |
| @product-team | 5 | Today 2:00 PM | Sync scheduled for... |

---

## Channel Snapshot

### Total Channels Accessible
- **Public Channels**: [NUMBER]
- **Private Channels**: [NUMBER]
- **Group DMs**: [NUMBER]

### Channel Categories
- **External Connections**: [COUNT] channels
- **Starred**: [COUNT] channels
- **Main Channels**: [COUNT] channels

---

## Most Active Channels (by recent activity)

| Rank | Channel | Activity | Participants |
|------|---------|----------|--------------|
| 1 | #engineering | High | 15+ active |
| 2 | #general | High | 10+ active |
| 3 | #product-design | Medium | 8+ active |

---

## Key Conversations

### [TOPIC 1]: Channel #engineering
- **Status**: Ongoing discussion
- **Participants**: @alice, @bob, @charlie
- **Latest Update**: [TIME]
- **Thread Count**: 5 threads
- **Files Shared**: 2 documents
- **Screenshots**: See `engineering-thread.png`

**Notes**: [Additional context about the conversation]

### [TOPIC 2]: DM with @alice
- **Unread Messages**: 2
- **Last Message**: [TIME]
- **Summary**: [Brief summary of conversation]
- **Action Items**: [Any TODOs mentioned]

---

## Search Results

### Query: "[SEARCH_TERM]"
- **Results**: [NUMBER] messages
- **Date Range**: [FROM] to [TO]
- **Top Channels**: [LIST]
- **Key Themes**: [PATTERNS OBSERVED]

#### Sample Results
1. **[Date/Time]** in #[channel]: [Message snippet]
2. **[Date/Time]** in #[channel]: [Message snippet]
3. **[Date/Time]** in #[channel]: [Message snippet]

---

## Reactions & Engagement

### Most Reacted-To Messages
| Message | Emoji | Count | Channel |
|---------|-------|-------|---------|
| "Shipped to production" | 🎉 | 8 | #engineering |
| "FYI the site is down" | 🚨 | 12 | #incidents |

---

## Team Insights

### Most Active Users (by message volume)
1. @alice - [COUNT] messages
2. @bob - [COUNT] messages
3. @charlie - [COUNT] messages

### Most Active Times
- Peak hour: [TIME]
- Peak day: [DAY]
- Average messages per hour: [NUMBER]

---

## Issues / Observations

### [ISSUE 1]: [Title]
**Severity**: [Critical/High/Medium/Low]
**Description**: [What was observed]
**Evidence**: See `issue-1-screenshot.png`
**Recommendation**: [Suggested action]

---

## Screenshots

| File | Description |
|------|-------------|
| `activity-tab.png` | Activity tab showing unreads |
| `dms-overview.png` | DM list with unread indicators |
| `channels-full-list.png` | Complete channel list |
| `engineering-thread.png` | Active engineering thread |

---

## Appendix: Raw Data

### Snapshot Output
```
[Paste snapshot -i output here]
```

### JSON Snapshot (for parsing)
```json
[Paste snapshot --json output here]
```

---

**Report Generated**: [DATE/TIME]
**Analysis Duration**: [TIME]
**Next Steps**: [TODO]
