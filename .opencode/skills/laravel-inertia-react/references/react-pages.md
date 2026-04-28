# React Pages

## Page Structure (resources/js/Pages)

```
resources/js/
├── Pages/
│   ├── Dashboard/
│   │   ├── Index.tsx      # Page component
│   │   └── Edit.tsx
│   └── Auth/
│       ├── Login.tsx
│       └── Register.tsx
├── components/              # Shared UI
├── layouts/               # Layouts
└── app.tsx               # Entry point
```

## Page Component Example

```tsx
import { PageProps } from '@/types'
import AuthenticatedLayout from '@/layouts/AuthenticatedLayout'

interface Props extends PageProps {
    users: User[]
}

export default function Index({ users }: Props) {
    return (
        <AuthenticatedLayout>
            <h1>Users</h1>
            <ul>
                {users.map(user => (
                    <li key={user.id}>{user.name}</li>
                ))}
            </ul>
        </AuthenticatedLayout>
    )
}
```

## Lazy Loading Pages

For better performance, use lazy loading:

```tsx
const Dashboard = lazy(() => import('./Pages/Dashboard/Index'))
```