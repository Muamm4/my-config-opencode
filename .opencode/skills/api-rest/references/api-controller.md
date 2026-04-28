# API Controller

RESTful controller implementation for Laravel APIs.

## Complete UserController

```php
<?php
namespace App\Http\Controllers;

use App\Http\Resources\UserResource;
use App\Models\User;

class UserController extends Controller
{
    public function index()
    {
        return UserResource::collection(
            User::query()->paginate(15)
        );
    }

    public function store(Request $request)
    {
        $validated = $request->validate([
            'name' => ['required', 'string', 'max:255'],
            'email' => ['required', 'email', 'unique:users'],
        ]);

        $user = User::create($validated);
        
        return (new UserResource($user))
            ->response()
            ->setStatusCode(201);
    }

    public function show(User $user)
    {
        return new UserResource($user);
    }

    public function update(Request $request, User $user)
    {
        $user->update($request->validated());
        
        return new UserResource($user);
    }

    public function destroy(User $user)
    {
        $user->delete();
        
        return response()->noContent();
    }
}
```

## Key Patterns

### Index - Paginated Collection
```php
return UserResource::collection(User::query()->paginate(15));
```

### Store - Create with 201 Status
```php
return (new UserResource($user))->response()->setStatusCode(201);
```

### Destroy - No Content Response
```php
return response()->noContent();
```

## Validation Rules

Always validate in the controller or form request:

```php
$validated = $request->validate([
    'name' => ['required', 'string', 'max:255'],
    'email' => ['required', 'email', 'unique:users'],
]);
```

## Error Handling

- Use `validate()` for automatic 422 responses on validation failure
- Return proper status codes (201 for created, 204 for deleted)
- Use API Resources for consistent response formatting