# Scheduled Workflows (Cron)

Combine with Vercel Cron Jobs for recurring browser tasks.

## API Route Pattern

```ts
// app/api/cron/route.ts (Next.js App Router)
// Or equivalent in your framework
export async function GET() {
  const result = await withBrowser(async (sandbox) => {
    await sandbox.runCommand("agent-browser", ["open", "https://example.com/pricing"]);
    const snap = await sandbox.runCommand("agent-browser", ["snapshot", "-i", "-c"]);
    await sandbox.runCommand("agent-browser", ["close"]);
    return await snap.stdout();
  });

  // Process results, send alerts, store data...
  return Response.json({ ok: true, snapshot: result });
}
```

## Vercel Cron Configuration

```json
// vercel.json
{
  "crons": [
    {
      "path": "/api/cron",
      "schedule": "0 9 * * *"
    }
  ]
}
```

## Cron Schedule Examples

| Schedule | Description |
|----------|-------------|
| `0 9 * * *` | Daily at 9 AM |
| `0 9 * * 1` | Weekly on Monday at 9 AM |
| `*/15 * * * *` | Every 15 minutes |
| `0 0 1 * *` | First day of every month |

## Use Cases

1. **Price Monitoring**: Check competitor pricing daily
2. **Availability Checks**: Verify services are up
3. **Data Collection**: Scrape data on schedule
4. **Health Checks**: Monitor critical pages

## Considerations

- Each cron job spins up a new sandbox (consider using snapshots for speed)
- Vercel Cron has timeout limits - keep operations lightweight
- Store results externally (database, S3, etc.) for later analysis