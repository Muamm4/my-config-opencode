# Sandbox Snapshots (Fast Startup)

A sandbox snapshot is a saved VM image with system dependencies + agent-browser + Chromium already installed. Instead of installing from scratch each time, the sandbox boots from the pre-built image.

## Without Snapshot

Each run installs system deps + agent-browser + Chromium (~30 seconds).

## With Snapshot

Startup is sub-second.

## Important Distinction

This is unrelated to agent-browser's *accessibility snapshot* feature (`agent-browser snapshot`), which dumps a page's accessibility tree. A sandbox snapshot is Vercel infrastructure for fast VM startup.

## Creating a Snapshot

```ts
import { Sandbox } from "@vercel/sandbox";

const CHROMIUM_SYSTEM_DEPS = [
  "nss", "nspr", "libxkbcommon", "atk", "at-spi2-atk", "at-spi2-core",
  "libXcomposite", "libXdamage", "libXrandr", "libXfixes", "libXcursor",
  "libXi", "libXtst", "libXScrnSaver", "libXext", "mesa-libgbm", "libdrm",
  "mesa-libGL", "mesa-libEGL", "cups-libs", "alsa-lib", "pango", "cairo",
  "gtk3", "dbus-libs",
];

async function createSnapshot(): Promise<string> {
  const sandbox = await Sandbox.create({
    runtime: "node24",
    timeout: 300_000,
  });

  // Install system dependencies
  await sandbox.runCommand("sh", [
    "-c",
    `sudo dnf clean all 2>&1 && sudo dnf install -y --skip-broken ${CHROMIUM_SYSTEM_DEPS.join(" ")} 2>&1 && sudo ldconfig 2>&1`,
  ]);

  // Install agent-browser
  await sandbox.runCommand("npm", ["install", "-g", "agent-browser"]);
  await sandbox.runCommand("npx", ["agent-browser", "install"]);

  // Create snapshot
  const snapshot = await sandbox.snapshot();
  return snapshot.snapshotId;
}
```

## Using the Snapshot

Run once, then set the environment variable:

```bash
AGENT_BROWSER_SNAPSHOT_ID=snap_xxxxxxxxxxxx
```

The `withBrowser` pattern (see `core-pattern.md`) automatically uses this ID when present.

## Alternative: Helper Script

A helper script is available in the demo app:

```bash
npx tsx examples/environments/scripts/create-snapshot.ts
```

## Recommendation

Use snapshots for any production deployment using the Sandbox pattern. The time investment pays off in:
- Faster cold starts
- More predictable performance
- Reduced failure points during startup