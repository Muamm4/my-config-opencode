# API Routes

Defining RESTful API routes in Laravel using the routes/api.php file.

## Basic API Routes

```php
// routes/api.php
use Illuminate\Support\Facades\Route;

Route::prefix('v1')->group(function () {
    Route::apiResource('users', UserController::class);
    Route::apiResource('posts', PostController::class);
});
```

## API Resource Methods

The `apiResource` method automatically registers these routes:

| Method | URI | Controller Action |
|-------|-----|----------------|
| GET | /users | index |
| GET | /users/{user} | show |
| POST | /users | store |
| PUT/PATCH | /users/{user} | update |
| DELETE | /users/{user} | destroy |

## API Resources vs Resources

- `apiResource`: Full RESTful CRUD (7 routes)
- `resource`: Customizable routes for specific use cases

```php
Route::resource('users', UserController::class)->only(['index', 'show']);
Route::resource('users', UserController::class)->except(['destroy']);
```

## Versioning

Use route prefixes for API versioning:

```php
Route::prefix('v1')->group(function () {
    // V1 routes
});

Route::prefix('v2')->group(function () {
    // V2 routes
});
```

## Best Practices

- Always use route groups with prefixes for versioning
- Use `apiResource` for standard CRUD endpoints
- Group related resources together