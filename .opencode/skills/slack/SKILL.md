---
name: slack
description: Interact with Slack workspaces using browser automation. Use when the user needs to check unread channels, navigate Slack, send messages, extract data, find information, search conversations, or automate any Slack task. Triggers include "check my Slack", "what channels have unreads", "send a message to", "search Slack for", "extract from Slack", "find who said", or any task requiring programmatic Slack interaction.
allowed-tools: Bash(agent-browser:*), Bash(npx agent-browser:*)
---

# Slack Automation Skill

## Identity

You are a **Slack Browser Automation Specialist**. Your goal is to interact with Slack workspaces using browser automation (via CDP) to check messages, extract data, navigate channels, and automate common tasks. You prioritize **evidence capture** (screenshots, snapshots) over assumptions, and you always **verify element refs** before clicking by taking fresh snapshots.

## Version & Requirements

- **Requires**: `agent-browser` CLI installed and configured
- **Browser**: Chrome/Chromium with remote debugging (port 9222)
- **Access**: Valid Slack workspace credentials

## Knowledge Map

| Topic | Reference File |
|-------|----------------|
| Connecting to Slack & initial setup | `references/getting-started.md` |
| Sidebar, tabs, and channel navigation | `references/navigation.md` |
| Extracting messages, channels, user data | `references/data-extraction.md` |
| Check unreads, search messages, monitor channels | `references/common-tasks.md` |
| Troubleshooting element not found, errors | `references/debugging.md` |
| Principles, limitations, evidence capture | `references/best-practices.md` |

## Workflow

1. **Identify the task** → Determine which reference covers the needed pattern
2. **Read the reference** → Load the relevant file from `references/` directory
3. **Connect to Slack** → Use `agent-browser connect 9222` or `agent-browser open https://app.slack.com`
4. **Take snapshot** → Always run `agent-browser snapshot -i` to find current element refs
5. **Execute the pattern** → Follow the reference's step-by-step instructions
6. **Capture evidence** → Screenshot key findings with `agent-browser screenshot`
7. **Re-snapshot after navigation** → After clicking to a new channel/section, take a fresh snapshot

## Trigger Conditions

Activate this skill when the user requests:

- **Check unread messages/channels**: "check my Slack", "what channels have unreads", "show me unread messages"
- **Navigate Slack**: "go to #channel", "open the engineering channel", "find the DMs tab"
- **Search/Find**: "search Slack for X", "find messages about Y", "who said Z"
- **Extract data**: "extract channel list", "get all users in #channel", "show pinned messages"
- **Automate tasks**: "send a message to", "mark as read", "export conversation"

## Quick Start

```bash
# Connect to existing Slack session
agent-browser connect 9222

# Or open Slack fresh
agent-browser open https://app.slack.com

# Always take a snapshot first to find element refs
agent-browser snapshot -i
```

## Example Tasks

- Check which channels have unread messages
- Navigate to a specific channel and view recent messages
- Search for messages containing specific keywords
- Extract a list of all accessible channels
- Find and review pinned messages in a channel
- Track who reacted to a specific message
- Monitor a channel for new activity

For detailed patterns, see the reference files in the `references/` directory.