# Tailwind 4

## Key Changes from v3

| v3 | v4 |
|----|----|
| `@tailwind base/components/utilities` | `@import "tailwindcss"` |
| `tailwind.config.js` | Theme in CSS |
| `bg-blue-500` | Same |
| JIT always on | JIT always on |

## Setup with Vite

```bash
# Install
npm install tailwindcss @tailwindcss/vite

# vite.config.ts
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
    plugins: [react(), tailwindcss()],
});
```

## CSS Import

```css
/* resources/css/app.css */
@import "tailwindcss";
```

## Theme Configuration

```css
/* In your main CSS file */
@theme {
    --color-primary: #3b82f6;
    --color-primary-dark: #1d4ed8;
    --font-sans: 'Inter', system-ui, sans-serif;
    
    --spacing-container: 1280px;
    --radius-lg: 0.5rem;
}
```

## Usage

```tsx
// Classes work the same as v3
<div className="bg-primary text-white p-4 rounded-lg">
    Content
</div>

// Using theme values
<div className="w-container">
    Content
</div>
```

## Breaking Changes

1. No more `@tailwind` directives - use `@import "tailwindcss"`
2. Theme configuration moved to CSS (`@theme` block)
3. Some utility prefixes changed (check docs)
4. CSS variables for colors (e.g., `--color-primary-500`)

## Common Issues

| Issue | Solution |
|-------|----------|
| Styles not applying | Check `@import "tailwindcss"` is in main CSS |
| Custom theme not working | Ensure `@theme` block is in imported CSS |
| Build errors | Update `@tailwindcss/vite` to latest |