# Data Extraction from Slack

Techniques for extracting structured data from Slack using browser automation.

## Get Text Content

```bash
# Get a message or element's text
agent-browser get text @e_message_ref
```

## Parse Accessibility Tree

```bash
# Full snapshot as JSON for programmatic parsing
agent-browser snapshot --json > output.json

# Look for:
# - Channel names (name field in treeitem)
# - Message content (in listitem/document elements)
# - User names (button elements with user info)
# - Timestamps (link elements with time info)
```

## Extract Channel Information

```bash
# Get list of all visible channels
agent-browser snapshot --json > slack-snapshot.json

# Parse for channel names and metadata
# Look for treeitem elements with level=2 (sub-channels under sections)
```

## Count Unreads

```bash
# After expanding unreads section:
agent-browser snapshot -i | grep -c "treeitem"
# Each treeitem with a channel name in the unreads section is one unread
```

## Extract User Information from a Conversation

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

## Track Reactions to a Message

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

## Extract Timestamp from Link

In Slack snapshot, message timestamps appear as links. Example:
```
- link "Feb 25th at 10:26:22 AM" [ref=e151]
  - /url: https://vercel.slack.com/archives/C0A5RTN0856/p1772036782543189
```

The URL contains the timestamp in the fragment (`p1772036782543189`). This is a Slack message ID that uniquely identifies the message.

## Find Pinned Messages

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