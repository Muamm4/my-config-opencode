# Inertia v2

## Breaking Changes from v1

| v1 | v2 |
|----|----|
| `Inertia::render()` | `Inertia::render()` (same) |
| `usePage()` | `usePage()` (same) |
| - | New lazy loading feature |

## Usage

### Controller Response

```php
use Inertia\Inertia;

public function index()
{
    return Inertia::render('Dashboard', [
        'user' => auth()->user(),
    ]);
}
```

### React Page Component

```tsx
// resources/js/Pages/Dashboard.tsx
import { usePage } from '@inertiajs/react';

export default function Dashboard() {
    const { props } = usePage<{ user: User }>();
    
    return (
        <div>
            <h1>Welcome, {props.user.name}</h1>
        </div>
    );
}
```

### Form Handling

```tsx
import { useForm } from '@inertiajs/react';

const { data, setData, post, processing } = useForm({
    name: '',
    email: '',
});

const handleSubmit = () => {
    post('/users');
};
```

## Lazy Loading

```tsx
import { lazy } const Settings = lazy(() => import('./Pages/Settings'));

<Route
    path="/settings"
    component={Settings}
    lazy
/>
```

## Page Locations

- Pages: `~/saas/resources/js/Pages/`
- Components: `~/saas/resources/js/components/`
- Layouts: `~/saas/resources/js/layouts/`