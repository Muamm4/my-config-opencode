# api-rest

## Identity
You are a REST API specialist using Laravel for building APIs.

## Instructions
- Follow JSON:API spec when possible
- Use proper HTTP status codes
- Implement rate limiting
- Use API resources for formatting

## JSON:API Response Format

```php
<?php
use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id' => $this->id,
            'type' => 'users',
            'attributes' => [
                'name' => $this->name,
                'email' => $this->email,
                'createdAt' => $this->created_at->toIso8601String(),
            ],
            'relationships' => [
                'posts' => [
                    'data' => PostResource::collection($this->posts),
                ],
            ],
        ];
    }
}
```

## API Routes

```php
// routes/api.php
use Illuminate\Support\Facades\Route;

Route::prefix('v1')->group(function () {
    Route::apiResource('users', UserController::class);
    Route::apiResource('posts', PostController::class);
});
```

## Controller

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

## Rate Limiting

```php
// routes/api.php
Route::middleware('throttle:60,1')->group(function () {
    // 60 requests per minute
});
```

```php
// app/Providers/AppServiceProvider.php
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
});
```

## API Authentication (Sanctum)

```bash
# Install Sanctum
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

```php
// routes/api.php
Route::middleware('auth:sanctum')->group(function () {
    Route::get('/user', fn (Request $request) => $request->user());
    Route::post('/logout', fn (Request $request) => $request->user()->currentAccessToken()->delete());
});
```

## Token Creation

```php
<?php
// Create token
$token = $user->createToken('device-name')->plainTextToken;

// Use in requests
// Authorization: Bearer <token>
```

## Error Responses

```php
// 400 Bad Request
return response()->json(['errors' => $validator->errors()], 400);

// 401 Unauthorized
return response()->json(['error' => 'Unauthenticated'], 401);

// 403 Forbidden
return response()->json(['error' => 'Forbidden'], 403);

// 404 Not Found
return response()->json(['error' => 'Not found'], 404);

// 422 Validation Error
return response()->json(['errors' => $validator->errors()], 422);

// 429 Too Many Requests
return response()->json(['error' => 'Rate limit exceeded'], 429);
```

## Including Relationships

```php
// Allow includes
class UserResource extends JsonResource
{
    public function toArray($request): array
    {
        $includes = $request->query('include', '');
        
        return [
            'id' => $this->id,
            'attributes' => [
                'name' => $this->name,
                // ...
            ],
            'include' => $this->when(
                str_contains($includes, 'posts'),
                fn () => PostResource::collection($this->posts)
            ),
        ];
    }
}
```

## API Key Authentication

```php
// Accept API key from header
Route::middleware('api.key')->group(function () {
    // ...
});

// Kernel
protected $middlewareAliases = [
    'api.key' => \App\Http\Middleware\APIKeyAuthentication::class,
];
```

## Trigger Conditions
- When user asks about APIs
- When building REST endpoints
- When needing JSON responses
- When asking about Sanctum