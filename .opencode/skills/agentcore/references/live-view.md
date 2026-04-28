# AgentCore Live View

Monitor active cloud browser sessions in real-time through the AWS Console.

## Live View URL

When a session starts, AgentCore prints a Live View URL to stderr:

```
Session: abc123-def456
Live View: https://us-east-1.console.aws.amazon.com/bedrock-agentcore/browser/aws.browser.v1/session/abc123-def456#
```

## How to Use

1. Run an agent-browser command with AgentCore
2. Look for the Live View URL in stderr output
3. Open the URL in any browser to watch the session in real-time

## What You Can Do

- **Watch**: See the browser render in real-time
- **Debug**: Visually verify automation steps
- **Interact**: Manually take over if needed (through AWS Console)

## Note

The Live View URL is only available during active sessions. Once the session closes, the URL becomes inaccessible.