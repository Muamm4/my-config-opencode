# AgentCore Common Issues

Troubleshooting guide for frequent problems.

## Issue: "Failed to run aws CLI"

**Cause**: AWS CLI is not installed or not in PATH.

**Solutions**:
1. Install AWS CLI: `pip install awscli` or download from AWS
2. OR set `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` directly

## Issue: "AWS CLI failed: ... Run 'aws sso login'"

**Cause**: SSO credentials have expired.

**Solutions**:
```bash
# Refresh SSO credentials
aws sso login --profile <your-profile>

# Then retry your agent-browser command
AWS_PROFILE=<your-profile> agent-browser -p agentcore open https://example.com
```

## Issue: Session Timeout

**Cause**: Default timeout is 3600 seconds (1 hour).

**Solutions**:
```bash
# Increase timeout for longer tasks (e.g., 2 hours)
AGENTCORE_SESSION_TIMEOUT=7200 agent-browser -p agentcore open https://example.com
```

## Issue: Permission Denied

**Cause**: Missing IAM permissions for Bedrock AgentCore.

**Solutions**:
1. Ensure your IAM role/user has `bedrock:CreateAgent` or equivalent
2. Check AWS Bedrock AgentCore is enabled in the region
3. Verify your account has access to AgentCore (may require AWS approval)