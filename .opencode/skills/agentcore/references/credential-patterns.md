# AgentCore Credential Patterns

Different scenarios require different AWS credential setups.

## Pattern 1: Explicit Credentials

Use for CI/CD pipelines or scripts with dedicated credentials.

```bash
# Set environment variables directly
export AWS_ACCESS_KEY_ID=AKIA...
export AWS_SECRET_ACCESS_KEY=...
agent-browser -p agentcore open https://example.com
```

**Best for**: Automated scripts, CI/CD environments

## Pattern 2: SSO (Interactive)

Use for interactive development with AWS SSO.

```bash
# First, authenticate with AWS SSO
aws sso login --profile my-profile

# Then run AgentCore with the profile
AWS_PROFILE=my-profile agent-browser -p agentcore open https://example.com
```

**Best for**: Developers using AWS Single Sign-On

## Pattern 3: IAM Role / Default Credential Chain

Let AWS SDK handle credential resolution automatically.

```bash
# Uses default credential chain (IAM role, EC2 instance profile, etc.)
agent-browser -p agentcore open https://example.com
```

**Best for**: EC2 instances, ECS tasks, Lambda functions with IAM roles

## Pattern 4: Named Profiles

Use AWS CLI named profiles for multiple accounts.

```bash
# Configure in ~/.aws/config
[profile dev]
region = us-east-1

[profile prod]
region = us-west-2

# Use specific profile
AWS_PROFILE=prod agent-browser -p agentcore open https://example.com
```

**Best for**: Multi-account AWS setups