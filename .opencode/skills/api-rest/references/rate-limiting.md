# Rate Limiting

Implementing rate limiting for Laravel API endpoints.

## Basic Rate Limiting

Apply rate limiting to route groups:

```php
// routes/api.php
Route::middleware('throttle:60,1')->group(function () {
    // 60 requests per minute
});
```

## Custom Rate Limiter

Define custom rate limiters in the AppServiceProvider:

```php
// app/Providers/AppServiceProvider.php
use Illuminate\Support\Facades\RateLimiter;
use Illuminate\Http\Request;
use Laravel\Fortify\Fortify;

RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
});
```

## Rate Limiter Options

### Per User Limit
```php
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
});
```

### Per IP Limit (Unauthenticated)
```php
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(60)->by($request->ip());
});
```

### Custom Response
```php
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(60)
        ->by($request->user()?->id ?: $request->ip())
        ->response(function () {
            return response()->json([
                'error' => 'Rate limit exceeded',
            ], 429);
        });
});
```

## Throttle Middleware Syntax

`throttle:requests,minutes`

- `60,1` = 60 requests per minute
- `100,5` = 100 requests per 5 minutes

## Best Practices

- Use rate limiting to prevent abuse
- Identify users by ID when authenticated, IP when not
- Return 429 status with clear error message
- Adjust limits based on endpoint sensitivity