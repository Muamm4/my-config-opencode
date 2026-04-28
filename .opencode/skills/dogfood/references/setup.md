# Setup and Parameters

Configuration for dogfood sessions. Only the **Target URL** is required.

## Parameters

| Parameter | Default | Example override |
|-----------|---------|-----------------|
| **Target URL** | _(required)_ | `vercel.com`, `http://localhost:3000` |
| **Session name** | Slugified domain (e.g., `vercel.com` -> `vercel-com`) | `--session my-session` |
| **Output directory** | `./dogfood-output/` | `Output directory: /tmp/qa` |
| **Scope** | Full app | `Focus on the billing page` |
| **Authentication** | None | `Sign in to user@example.com` |

## Quick Start

If the user says something like "dogfood vercel.com", start immediately with defaults. Do not ask clarifying questions unless authentication is mentioned but credentials are missing.

## Tool Selection

Always use `agent-browser` directly -- never `npx agent-browser`. The direct binary uses the fast Rust client. `npx` routes through Node.js and is significantly slower.