# Sanctum Authentication

Laravel Sanctum for API token-based authentication.

## Installation

```bash
# Install Sanctum
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

## Protected Routes

```php
// routes/api.php
Route::middleware('auth:sanctum')->group(function () {
    Route::get('/user', fn (Request $request) => $request->user());
    Route::post('/logout', fn (Request $request) => $request->user()->currentAccessToken()->delete());
});
```

## Middleware Options

- `auth:sanctum` - Requires valid Sanctum token
- `auth` - Requires any authenticated user
- `auth:basic` - HTTP Basic authentication

## Token Configuration

### Migrations (already included with Sanctum)
Sanctum creates a `personal_access_tokens` table for storing tokens.

### Model Configuration
```php
<?php
namespace App\Models;

use Laravel\Sanctum\HasApiTokens;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasApiTokens;
    // ...
}
```

## Common Issues

### CORS Configuration
Ensure CORS is configured for SPA authentication:

```php
// config/cors.php
'allowed_origins' => ['http://localhost:3000'],
'ssupports_credentials' => true,
```

### Session vs Token
Sanctum supports both:
- **SPA**: Uses session + CSRF token
- **Mobile/API**: Uses bearer tokens