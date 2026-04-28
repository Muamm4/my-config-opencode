# Core Pattern: Sandbox Browser Automation

The fundamental pattern for running agent-browser inside Vercel Sandbox microVMs.

## Dependencies

```bash
pnpm add @vercel/sandbox
```

The sandbox VM needs system dependencies for Chromium plus agent-browser itself. Use sandbox snapshots (see `sandbox-snapshots.md`) to pre-install everything for sub-second startup.

## System Libraries Required

Chromium requires these Amazon Linux / dnf packages:

```ts
const CHROMIUM_SYSTEM_DEPS = [
  "nss", "nspr", "libxkbcommon", "atk", "at-spi2-atk", "at-spi2-core",
  "libXcomposite", "libXdamage", "libXrandr", "libXfixes", "libXcursor",
  "libXi", "libXtst", "libXScrnSaver", "libXext", "mesa-libgbm", "libdrm",
  "mesa-libGL", "mesa-libEGL", "cups-libs", "alsa-lib", "pango", "cairo",
  "gtk3", "dbus-libs",
];
```

## The withBrowser Pattern

```ts
import { Sandbox } from "@vercel/sandbox";

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

async function withBrowser<T>(
  fn: (sandbox: InstanceType<typeof Sandbox>) => Promise<T>,
): Promise<T> {
  const snapshotId = process.env.AGENT_BROWSER_SNAPSHOT_ID;
  const credentials = getSandboxCredentials();

  const sandbox = snapshotId
    ? await Sandbox.create({
        ...credentials,
        source: { type: "snapshot", snapshotId },
        timeout: 120_000,
      })
    : await Sandbox.create({ ...credentials, runtime: "node24", timeout: 120_000 });

  if (!snapshotId) {
    await sandbox.runCommand("sh", [
      "-c",
      `sudo dnf clean all 2>&1 && sudo dnf install -y --skip-broken ${CHROMIUM_SYSTEM_DEPS.join(" ")} 2>&1 && sudo ldconfig 2>&1`,
    ]);
    await sandbox.runCommand("npm", ["install", "-g", "agent-browser"]);
    await sandbox.runCommand("npx", ["agent-browser", "install"]);
  }

  try {
    return await fn(sandbox);
  } finally {
    await sandbox.stop();
  }
}
```

## Key Concepts

1. **Credential Resolution**: Automatically uses environment variables for local dev, falls back to OIDC on Vercel
2. **Snapshot Support**: If `AGENT_BROWSER_SNAPSHOT_ID` is set, boots from pre-built image for sub-second startup
3. **Cleanup**: Always stops the sandbox in the `finally` block to prevent resource leaks
4. **Timeout**: 120 second timeout provides ample time for most browser operations

## When to Use This Pattern

- Any browser automation task that needs isolation
- Running headless Chrome without binary size limits
- Needing ephemeral environments that spin up and down
- Persistent browser sessions across multiple commands