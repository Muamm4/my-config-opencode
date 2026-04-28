# Authentication

## Auth Data via Middleware

```php
<?php
// app/Http/Middleware/HandleInertiaRequests.php

class HandleInertiaRequests extends Middleware
{
    public function share(Request $request): array
    {
        return array_merge(parent::share($request), [
            'auth' => [
                'user' => $request->user() ? $request->user()->only('id', 'name', 'email') : null,
            ],
            'flash' => [
                'success' => fn () => $request->session()->get('success'),
            ],
        ];
    }
}
```

## Accessing Auth in React

```tsx
import { usePage } from '@inertiajs/react'

export default function Component() {
    const { auth } = usePage().props
    
    if (auth.user) {
        return <p>Welcome {auth.user.name}</p>
    }
    
    return <p>Please login</p>
}
```

## Auth Data via Props

```tsx
interface Props extends PageProps {
    auth: {
        user: { id: number; name: string; email: string } | null
    }
    flash?: {
        success?: string
    }
}
```