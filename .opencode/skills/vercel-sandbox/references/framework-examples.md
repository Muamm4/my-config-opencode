# Framework Examples

The Vercel Sandbox pattern works identically across frameworks. The only difference is where you put the server-side code.

## Framework Comparison

| Framework | Server Code Location | File Example |
|-----------|---------------------|--------------|
| Next.js | Server actions, API routes, route handlers | `app/api/browser/route.ts` |
| SvelteKit | `+page.server.ts`, `+server.ts` | `src/routes/+page.server.ts` |
| Nuxt | `server/api/`, `server/routes/` | `server/api/browser.ts` |
| Remix | `loader`, `action` functions | `app/routes/_index.tsx` |
| Astro | `.astro` frontmatter, API routes | `src/pages/api/browser.ts` |

## Next.js Example (App Router)

```ts
// app/api/screenshot/route.ts
import { withBrowser } from "@/lib/sandbox";

export async function POST(request: Request) {
  const { url } = await request.json();

  const result = await withBrowser(async (sandbox) => {
    await sandbox.runCommand("agent-browser", ["open", url]);
    const ssResult = await sandbox.runCommand("agent-browser", ["screenshot", "--json"]);
    const ssPath = JSON.parse(await ssResult.stdout())?.data?.path;
    const b64Result = await sandbox.runCommand("base64", ["-w", "0", ssPath]);
    await sandbox.runCommand("agent-browser", ["close"]);
    return (await b64Result.stdout()).trim();
  });

  return Response.json({ screenshot: result });
}
```

## SvelteKit Example

```ts
// src/routes/+page.server.ts
import { withBrowser } from "$lib/sandbox";

export const actions = {
  screenshot: async ({ request }) => {
    const data = await request.formData();
    const url = data.get("url");

    const result = await withBrowser(async (sandbox) => {
      await sandbox.runCommand("agent-browser", ["open", url]);
      // ... capture screenshot
      await sandbox.runCommand("agent-browser", ["close"]);
      return { success: true };
    });

    return result;
  },
};
```

## Key Points

1. **Same Pattern**: The `withBrowser` function works identically everywhere
2. **Framework-Agnostic**: The sandbox SDK doesn't care about your framework
3. **TypeScript**: Works best with TypeScript for type safety
4. **Async/Await**: All operations are async - handle appropriately