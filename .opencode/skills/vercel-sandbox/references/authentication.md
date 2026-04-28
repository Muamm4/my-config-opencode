# Authentication

How the Vercel Sandbox SDK authenticates in different environments.

## On Vercel Deployments

The Sandbox SDK authenticates automatically via OIDC. No configuration needed.

## Local Development

For explicit control, set these environment variables:

```bash
VERCEL_TOKEN=<personal-access-token>
VERCEL_TEAM_ID=<team-id>
VERCEL_PROJECT_ID=<project-id>
```

## How It Works

These are spread into `Sandbox.create()` calls:

```ts
function getSandboxCredentials() {
  if (
    process.env.VERCEL_TOKEN &&
    process.env.VERCEL_TEAM_ID &&
    process.env.VERCEL_PROJECT_ID
  ) {
    return {
      token: process.env.VERCEL_TOKEN,
      teamId: process.env.VERCEL_TEAM_ID,
      projectId: process.env.VERCEL_PROJECT_ID,
    };
  }
  return {};
}
```

## Fallback Behavior

When environment variables are absent, the SDK falls back to `VERCEL_OIDC_TOKEN`, which is automatic on Vercel deployments.

## Getting Credentials

1. **Vercel Token**: Create a personal access token at https://vercel.com/account/tokens
2. **Team ID**: Found in Vercel dashboard URL or team settings
3. **Project ID**: Found in Vercel project settings

## Security Note

Never commit tokens to version control. Use environment variables or secrets management.