# Performance

## Deferred Props (Lazy Loading Data)

```php
// Load heavy data separately
return Inertia::render('Dashboard', [
    'users' => fn () => User::latest()->with('profile')->get(),
])
```

## Lazy Loading Pages

In `app.tsx`:

```typescript
import { lazy, Suspense } from 'react'

const Dashboard = lazy(() => import('./Pages/Dashboard/Index'))
const Settings = lazy(() => import('./Pages/Settings/Index'))

// Wrap pages with Suspense
<Suspense fallback={<Loading />}>
    <Component />
</Suspense>
```

## Performance Tips

1. **Lazy load pages** - For better initial load time
2. **Deferred props** - Load heavy data separately
3. **Use Inertia links** - Don't use `<a>` tags for SPA navigation