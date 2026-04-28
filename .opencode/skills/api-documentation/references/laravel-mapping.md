# Laravel Endpoint Mapping

This reference covers how to map every OpenAPI endpoint to its corresponding Controller method in Laravel.

## Mapping Strategy

| OpenAPI Path | HTTP Method | Laravel Controller |
|--------------|------------|-------------------|
| `/users` | GET | `UserController@index` |
| `/users` | POST | `UserController@store` |
| `/users/{user_id}` | GET | `UserController@show` |
| `/users/{user_id}` | PUT/PATCH | `UserController@update` |
| `/users/{user_id}` | DELETE | `UserController@destroy` |

## Laravel Routing Pattern

```php
// routes/api.php
Route::middleware(['auth:api'])->group(function () {
    Route::apiResource('users', UserController::class);
});

// Resulting routes:
// GET /users          -> UserController@index
// POST /users        -> UserController@store
// GET /users/{user}  -> UserController@show
// PUT /users/{user}  -> UserController@update
// DELETE /users/{user} -> UserController@destroy
```

## Finding Controllers from Endpoints

### Using Grep

```bash
# Find controller for /users endpoint
grep -r "Route::" routes/api.php | grep "/users"
# Result: Route::apiResource('users', UserController::class);

# Find the controller method
grep -r "function" app/Http/Controllers/Api/UserController.php | head -10
```

### Controller Structure

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\User;
use Illuminate\Http\JsonResponse;

class UserController extends Controller
{
    /**
     * GET /users
     * List all users
     */
    public function index(): JsonResponse
    {
        $users = User::paginate(15);
        return response()->json(['data' => $users]);
    }

    /**
     * POST /users
     * Create new user
     */
    public function store(Request $request): JsonResponse
    {
        $validated = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users',
            'password' => 'required|string|min:8'
        ]);

        $user = User::create($validated);
        return response()->json(['data' => $user], 201);
    }

    /**
     * GET /users/{user}
     * Show single user
     */
    public function show(User $user): JsonResponse
    {
        return response()->json(['data' => $user]);
    }

    /**
     * PUT /users/{user}
     * Update user
     */
    public function update(Request $request, User $user): JsonResponse
    {
        $validated = $request->validate([
            'name' => 'sometimes|string|max:255',
            'email' => 'sometimes|email|unique:users,email,' . $user->id
        ]);

        $user->update($validated);
        return response()->json(['data' => $user]);
    }

    /**
     * DELETE /users/{user}
     * Delete user
     */
    public function destroy(User $user): JsonResponse
    {
        $user->delete();
        return response()->json(null, 204);
    }
}
```

## Best Practices

1. **Use** `apiResource` for consistent CRUD routes
2. **Follow** RESTful conventions (index, store, show, update, destroy)
3. **Use** route model binding (`User $user`) for automatic resolution
4. **Return** proper status codes (200, 201, 204)
5. **Document** the mapping in your API docs