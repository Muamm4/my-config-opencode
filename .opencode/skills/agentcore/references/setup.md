# AgentCore Setup

This reference covers initial setup, credentials, and environment variables.

## Credentials Resolution

AgentCore resolves AWS credentials automatically in this order:

1. **Environment variables** (highest priority):
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_SESSION_TOKEN` (optional, for temporary credentials)

2. **AWS CLI fallback** (lower priority):
   - Runs `aws configure export-credentials`
   - Supports SSO, IAM roles, and named profiles

No additional setup is needed if you already have working AWS credentials.

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `AGENTCORE_REGION` | AWS region | `us-east-1` |
| `AGENTCORE_BROWSER_ID` | Browser identifier | `aws.browser.v1` |
| `AGENTCORE_PROFILE_ID` | Persistent browser profile (cookies, localStorage) | (none) |
| `AGENTCORE_SESSION_TIMEOUT` | Session timeout in seconds | `3600` |
| `AWS_PROFILE` | AWS CLI profile for credential resolution | `default` |

## Prerequisites

- AWS CLI installed and in PATH (or use environment variables directly)
- Valid AWS credentials with bedrock permissions
- `agent-browser` npm package installed