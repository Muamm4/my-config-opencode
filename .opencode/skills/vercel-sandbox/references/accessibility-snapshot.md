# Accessibility Snapshot Pattern

How to capture the accessibility tree from the sandbox browser for element discovery and automation.

## Pattern

```ts
export async function snapshotUrl(url: string) {
  return withBrowser(async (sandbox) => {
    // Open the URL
    await sandbox.runCommand("agent-browser", ["open", url]);

    // Get page title
    const titleResult = await sandbox.runCommand("agent-browser", [
      "get", "title", "--json",
    ]);
    const title = JSON.parse(await titleResult.stdout())?.data?.title || url;

    // Capture accessibility snapshot
    const snapResult = await sandbox.runCommand("agent-browser", [
      "snapshot", "-i", "-c",
    ]);
    const snapshot = await snapResult.stdout();

    // Clean up
    await sandbox.runCommand("agent-browser", ["close"]);

    return { title, snapshot };
  });
}
```

## Flags Explained

| Flag | Description |
|------|-------------|
| `-i` | Interactive mode - returns detailed element information |
| `-c` | Compact output - more readable JSON |

## Return Format

```ts
{
  title: string;      // Page title or URL fallback
  snapshot: string;   // JSON string of accessibility tree
}
```

## Use Cases

1. **Element Discovery**: Find element references (e.g., `@e5`) for automation
2. **Form Automation**: Identify input fields, buttons, and their refs
3. **Testing**: Verify accessibility tree structure
4. **Debugging**: Understand page structure without visual inspection

## Important Note

This is different from **sandbox snapshots** (pre-built VM images). A sandbox snapshot is a Vercel infrastructure concept for fast VM startup. The accessibility snapshot (`agent-browser snapshot`) dumps the page's accessibility tree.