# Routes & Controllers

## Laravel Routes (routes/web.php)

```php
Route::get('/dashboard', [DashboardController::class, 'index'])
    ->middleware(['auth', 'verified'])
    ->name('dashboard');
```

## Controller Returns Inertia Response

```php
<?php
namespace App\Http\Controllers;

use Inertia\Inertia;

class DashboardController extends Controller
{
    public function index()
    {
        $users = User::latest()->get();
        
        return Inertia::render('Dashboard/Index', [
            'users' => $users
        ]);
    }
}
```

## Key Principles

1. **No API needed** - Inertia connects Laravel directly to React
2. **Server-side routing** - Laravel routes, no React Router
3. **Session auth** - Use Laravel's auth, not tokens
4. **Props, not API calls** - Pass data from controllers