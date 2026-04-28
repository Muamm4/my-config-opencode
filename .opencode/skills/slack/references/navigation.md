# Slack Navigation Guide

How to navigate the Slack interface using browser automation.

## Sidebar Structure

The Slack sidebar contains:

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

## Key Sidebar Refs (Session-Dependent)

These refs vary per session but follow patterns:

| Element | Typical Ref Range | How to Find |
|---------|------------------|-------------|
| Home tab | e10-e20 | `snapshot -i \| grep "Home"` |
| DMs tab | e10-e20 | `snapshot -i \| grep "DMs"` |
| Activity tab | e10-e20 | `snapshot -i \| grep "Activity"` |
| Search | e5-e10 | `snapshot -i \| grep "Search"` |
| More unreads | e20-e30 | `snapshot -i \| grep "More unreads"` |
| Channel refs | e30+ | `snapshot -i \| grep "treeitem"` |

**Always take a fresh snapshot** to find current refs for the current session.

## Navigating to a Channel

```bash
# Search for channel in sidebar
agent-browser snapshot -i

# Look for channel name in the list (e.g., "engineering", "product-design")
# Click on the channel treeitem ref
agent-browser click @e94  # Example: engineering channel ref
agent-browser wait --load networkidle
agent-browser screenshot channel.png
```

## Tabs in Channel View

After clicking on a channel, you'll see tabs:
- **Messages** - Channel conversation
- **Files** - Shared files
- **Pins** - Pinned messages
- **Add canvas** - Collaborative canvas
- Other tabs depending on workspace setup

Click tab refs to switch views and get different information.

## Expanding Sidebar Sections

### Expand "More Unreads"

```bash
agent-browser click @e21  # More unreads button
agent-browser wait 500
agent-browser screenshot expanded-unreads.png
```

### Scroll Sidebar

If the channel list is long:

```bash
agent-browser scroll down 300 --selector ".p-sidebar"
agent-browser screenshot channels-page-2.png
```

## Activity Tab

Navigate to Activity tab to see all unreads in one view:

```bash
agent-browser click @e14  # Activity tab (ref may vary)
agent-browser wait 1000
agent-browser screenshot activity-unreads.png
```

## Direct Messages Tab

```bash
agent-browser click @e13  # DMs tab
agent-browser screenshot dms.png
```

## Using Search

```bash
agent-browser snapshot -i
agent-browser click @e5  # Search button (typical ref)
agent-browser fill @e_search "keyword"
agent-browser press Enter
agent-browser wait --load networkidle
agent-browser screenshot search-results.png
```

## Channel Level Structure

- **level=1**: Section headers (External connections, Starred, Channels, etc.)
- **level=2**: Individual channels/items within sections
- **level=3+**: Nested sub-items (rare in sidebar)