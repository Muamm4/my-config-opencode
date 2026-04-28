# Forms & Data

## useForm (Inertia Forms)

```tsx
import { useForm } from '@inertiajs/react'

export default function Create() {
    const form = useForm({
        name: '',
        email: '',
    })

    function submit(e) {
        e.preventDefault()
        form.post('/users')
    }

    return (
        <form onSubmit={submit}>
            <input 
                value={form.data.name}
                onChange={e => form.setData('name', e.target.value)}
            />
            {form.errors.name && <p>{form.errors.name}</p>}
            <button disabled={form.processing}>Submit</button>
        </form>
    )
}
```

## Form with Validation (Laravel side)

```php
<?php
namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Validation\Rules\Password;

class UserController extends Controller
{
    public function store(Request $request)
    {
        $request->validate([
            'name' => ['required', 'string', 'max:255'],
            'email' => ['required', 'email', 'max:255', 'unique:users'],
            'password' => ['required', 'string', 'Password::min(8')->confirmed()],
        ]);

        $user = User::create($request->validated());

        return redirect('/users')->with('success', 'User created');
    }
}
```

## Best Practices

1. **Validate server-side** - Don't duplicate validation in React
2. **Use flash messages** - For success/error feedback
3. **Keep React thin** - Logic in Laravel controllers