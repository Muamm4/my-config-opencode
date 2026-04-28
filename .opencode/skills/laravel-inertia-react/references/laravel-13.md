# Laravel 13 Specific

## PHP Attributes

### Model Attributes

```php
<?php
use Illuminate\Database\Eloquent\Attributes\Fillable;
use Illuminate\Database\Eloquent\Attributes\Hidden;
use Illuminate\Database\Eloquent\Attributes\Table;

#[Table('products')]
#[Fillable('name', 'price', 'sku')]
#[Hidden('internal_notes')]
class Product extends Model
{
    // No fillable/hidden properties needed
}
```

### Controller with Attributes

```php
<?php
use Illuminate\Routing\Attributes\Middleware;
use Illuminate\Routing\Attributes\Authorize;
use Illuminate\Http\Request;

#[Middleware('auth')]
#[Authorize('admin')]
class AdminController extends Controller
{
    public function index() { /* ... */ }
    
    #[Authorize('admin.users')]
    public function store(Request $request) { /* ... */ }
}
```

### Queue Job Attributes

```php
<?php
use Illuminate\Queue\Attributes\Tries;
use Illuminate\Queue\Attributes\Backoff;

#[Tries(3)]
#[Backoff([60, 120, 300])]
class ProcessOrderJob implements ShouldQueue
{
    public function handle() { /* ... */ }
}
```

## Stack Overview

- **Backend**: Laravel 13 (PHP 8.3+)
- **Bridge**: Inertia.js v3
- **Frontend**: React 19 + TypeScript + Vite
- **UI**: Tailwind CSS + shadcn/ui
- **Auth**: Laravel Breeze (session-based)