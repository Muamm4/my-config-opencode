# Environment Variables Reference

Complete reference for environment variables used with Vercel Sandbox browser automation.

## Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `AGENT_BROWSER_SNAPSHOT_ID` | No | None | Pre-built sandbox snapshot ID for sub-second startup |
| `VERCEL_TOKEN` | No | None | Vercel personal access token (for local dev) |
| `VERCEL_TEAM_ID` | No | None | Vercel team ID (for local dev) |
| `VERCEL_PROJECT_ID` | No | None | Vercel project ID (for local dev) |
| `VERCEL_OIDC_TOKEN` | No | Auto | OIDC token (automatic on Vercel) |

## Usage Matrix

| Environment | Required Variables |
|-------------|-------------------|
| Local Development | `VERCEL_TOKEN`, `VERCEL_TEAM_ID`, `VERCEL_PROJECT_ID` |
| Vercel Deployment | None (OIDC automatic) |
| With Snapshot | `AGENT_BROWSER_SNAPSHOT_ID` (+ above based on env) |

## Setting Variables

### Local Development (.env)

```bash
VERCEL_TOKEN=your_token_here
VERCEL_TEAM_ID=your_team_id
VERCEL_PROJECT_ID=your_project_id
AGENT_BROWSER_SNAPSHOT_ID=snap_xxxxxxxxxxxx
```

### Vercel Project Settings

Add in Vercel dashboard: Project Settings → Environment Variables

### Vercel Cron

Add to `vercel.json`:

```json
{
  "crons": [
    {
      "path": "/api/cron",
      "schedule": "0 9 * * *",
      "env": ["AGENT_BROWSER_SNAPSHOT_ID"]
    }
  ]
}
```