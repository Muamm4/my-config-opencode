# API Key Authentication

Custom API key-based authentication middleware.

## Middleware Structure

```php
<?php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class APIKeyAuthentication
{
    public function handle(Request $request, Closure $next)
    {
        $apiKey = $request->header('X-API-KEY');
        
        if (!$apiKey || $apiKey !== config('services.api.key')) {
            return response()->json([
                'error' => 'Invalid API key',
            ], 401);
        }
        
        return $next($request);
    }
}
```

## Kernel Registration

```php
// app/Http/Kernel.php
protected $middlewareAliases = [
    'api.key' => \App\Http\Middleware\APIKeyAuthentication::class,
];
```

## Route Usage

```php
// routes/api.php
Route::middleware('api.key')->group(function () {
    // Protected routes
});
```

## Configuration

```php
// config/services.php
'api' => [
    'key' => env('API_KEY'),
],
```

## Advanced: Multiple Keys

```php
public function handle(Request $request, Closure $next)
{
    $apiKey = $request->header('X-API-KEY');
    $validKeys = config('services.api.keys', []);
    
    if (!$apiKey || !in_array($apiKey, $validKeys)) {
        return response()->json([
            'error' => 'Invalid API key',
        ], 401);
    }
    
    return $next($request);
}
```

## Best Practices

- Use secure storage for API keys (environment variables)
- Rotate keys periodically
- Log API key usage for auditing
- Consider combining with other auth methods