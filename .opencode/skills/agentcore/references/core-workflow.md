# AgentCore Core Workflow

All standard agent-browser commands work identically on AgentCore; the only difference is where the browser runs.

## Basic Command Syntax

```bash
# Open a page on an AgentCore cloud browser
agent-browser -p agentcore open https://example.com

# Everything else is the same as local Chrome
agent-browser snapshot -i
agent-browser click @e1
agent-browser screenshot page.png
agent-browser close
```

## Using with AGENT_BROWSER_PROVIDER

Set the provider via environment variable to avoid passing `-p agentcore` on every command:

```bash
export AGENT_BROWSER_PROVIDER=agentcore
export AGENTCORE_REGION=us-east-2

agent-browser open https://example.com
agent-browser snapshot -i
agent-browser click @e1
agent-browser close
```

## Supported Commands

All agent-browser commands are supported:
- `open` - Navigate to URL
- `snapshot -i` - Get interactive elements
- `click`, `fill`, `type` - Interact with elements
- `screenshot` - Capture page
- `close` - Terminate session

The cloud browser behaves identically to local Chrome for command execution.