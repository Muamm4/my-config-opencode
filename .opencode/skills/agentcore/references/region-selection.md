# AgentCore Region Selection

Configure which AWS region the cloud browser runs in.

## Default Region

If not specified, AgentCore uses `us-east-1`.

## Setting Region

```bash
# Default: us-east-1
agent-browser -p agentcore open https://example.com

# Explicit region via environment variable
AGENTCORE_REGION=eu-west-1 agent-browser -p agentcore open https://example.com
AGENTCORE_REGION=us-west-2 agent-browser -p agentcore open https://example.com
```

## Available Regions

AgentCore is available in most AWS regions. Common options:
- `us-east-1` (Virginia) - Default
- `us-west-2` (Oregon)
- `eu-west-1` (Ireland)
- `eu-central-1` (Frankfurt)
- `ap-southeast-1` (Singapore)
- `ap-northeast-1` (Tokyo)

## Considerations

- **Latency**: Choose a region closest to your target website for best performance
- **Availability**: Not all regions may support AgentCore; check AWS documentation
- **Cost**: Different regions may have different pricing