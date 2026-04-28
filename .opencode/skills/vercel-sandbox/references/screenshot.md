# Screenshot Capture Pattern

How to capture screenshots from the sandbox browser and return them as base64.

## Pattern

The `screenshot --json` command saves to a file and returns the path. Read the file back as base64:

```ts
export async function screenshotUrl(url: string) {
  return withBrowser(async (sandbox) => {
    // Open the URL
    await sandbox.runCommand("agent-browser", ["open", url]);

    // Get page title
    const titleResult = await sandbox.runCommand("agent-browser", [
      "get", "title", "--json",
    ]);
    const title = JSON.parse(await titleResult.stdout())?.data?.title || url;

    // Capture screenshot
    const ssResult = await sandbox.runCommand("agent-browser", [
      "screenshot", "--json",
    ]);
    const ssPath = JSON.parse(await ssResult.stdout())?.data?.path;
    const b64Result = await sandbox.runCommand("base64", ["-w", "0", ssPath]);
    const screenshot = (await b64Result.stdout()).trim();

    // Clean up
    await sandbox.runCommand("agent-browser", ["close"]);

    return { title, screenshot };
  });
}
```

## Key Points

1. **JSON Output**: Use `--json` flag to get structured output with file paths
2. **Base64 Encoding**: Use `base64 -w 0` to get raw base64 without line wrapping
3. **Cleanup**: Always close the browser session when done
4. **Error Handling**: Fall back to URL as title if title extraction fails

## Return Format

```ts
{
  title: string;      // Page title or URL fallback
  screenshot: string; // Base64-encoded PNG image
}
```

## Common Use Cases

- Visual regression testing
- Generating preview images
- Capturing state for debugging
- Creating thumbnails of web pages