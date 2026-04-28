# Best Practices

1. **Use Laravel starter kits** - Breeze handles auth scaffolding
2. **Keep React thin** - Logic in Laravel controllers
3. **Validate server-side** - Don't duplicate in React
4. **Use TypeScript** - With shadcn/ui types
5. **Lazy load pages** - For better performance
6. **Use Inertia links** - Don't use `<a>` tags for SPA navigation
7. **Flash messages** - For success/error feedback
8. **Shared data via middleware** - Auth user, config
9. **Session auth** - Use Laravel's auth, not tokens
10. **Props, not API calls** - Pass data from controllers

## Commands

```bash
# Development
composer dev          # Laravel + Vite concurrently

# Build
npm run build         # Production build
npm run build:ssr     # With SSR

# Quality
composer lint        # Pint + Rector
composer test        # Tests
```