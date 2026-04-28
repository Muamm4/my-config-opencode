# Multi-Step Workflows

The sandbox persists between commands, enabling full automation sequences.

## Pattern

```ts
export async function fillAndSubmitForm(url: string, data: Record<string, string>) {
  return withBrowser(async (sandbox) => {
    // Step 1: Open the URL
    await sandbox.runCommand("agent-browser", ["open", url]);

    // Step 2: Get initial snapshot to find element refs
    const snapResult = await sandbox.runCommand("agent-browser", [
      "snapshot", "-i",
    ]);
    const snapshot = await snapResult.stdout();
    // Parse snapshot to find element refs...

    // Step 3: Fill form fields
    for (const [ref, value] of Object.entries(data)) {
      await sandbox.runCommand("agent-browser", ["fill", ref, value]);
    }

    // Step 4: Click submit button
    await sandbox.runCommand("agent-browser", ["click", "@e5"]);

    // Step 5: Wait for navigation
    await sandbox.runCommand("agent-browser", ["wait", "--load", "networkidle"]);

    // Step 6: Capture final screenshot
    const ssResult = await sandbox.runCommand("agent-browser", [
      "screenshot", "--json",
    ]);
    const ssPath = JSON.parse(await ssResult.stdout())?.data?.path;
    const b64Result = await sandbox.runCommand("base64", ["-w", "0", ssPath]);
    const screenshot = (await b64Result.stdout()).trim();

    // Step 7: Clean up
    await sandbox.runCommand("agent-browser", ["close"]);

    return { screenshot };
  });
}
```

## Key Concepts

1. **Persistence**: The browser session stays alive across commands within `withBrowser`
2. **Element References**: Use accessibility snapshots to discover refs like `@e5`
3. **Sequential Operations**: Each command runs in order, waiting for completion
4. **State Verification**: Can capture snapshots/screenshots at any step

## Common Workflow Patterns

| Workflow | Steps |
|----------|-------|
| Form Fill | open → snapshot → fill × N → click → wait → screenshot → close |
| Login Flow | open → snapshot → fill username → fill password → click → wait → snapshot |
| Scraping | open → wait → snapshot → extract data → close |
| Testing | open → interact → snapshot → verify → screenshot → close |

## Error Handling

Consider wrapping individual commands in try-catch to handle failures gracefully:

```ts
try {
  await sandbox.runCommand("agent-browser", ["click", "@e5"]);
} catch (error) {
  // Handle click failure - element might not exist
}
```