# Scopes

Pattern reference for reusable query logic in Eloquent models.

## Local Scopes

```php
class User extends Model
{
    // Basic scope (no parameters)
    public function scopeActive($query)
    {
        return $query->where('active', true);
    }

    // Scope with parameter
    public function scopeOfRole($query, string $role)
    {
        return $query->where('role', $role);
    }

    // Scope with optional parameter
    public function scopeInStatus($query, array $status = ['active', 'pending'])
    {
        return $query->whereIn('status', $status);
    }

    // Scope with multiple parameters
    public function scopeCreatedBetween($query, Carbon $from, Carbon $to)
    {
        return $query->whereBetween('created_at', [$from, $to]);
    }
}

// Usage
$activeUsers = User::active()->get();
$admins = User::ofRole('admin')->get();
$recentActive = User::active()->createdBetween($from, $to)->get();
```

## Global Scopes

```php
// Simple global scope
class User extends Model
{
    protected static function booted()
    {
        static::addGlobalScope('active', function ($query) {
            $query->where('active', true);
        });
    }
}

// Remove global scope
User::withoutGlobalScope('active')->get();
User::withoutGlobalScopes()->get();

// With global scope class
class ActiveScope implements Scope
{
    public function apply(Builder $query, Model $model)
    {
        $query->where('active', true);
    }
}

class User extends Model
{
    protected static function booted()
    {
        static::addGlobalScope(new ActiveScope);
    }
}
```

## Query Scopes (Filtering in Controllers)

```php
// Controller example with query scopes
public function index(Request $request)
{
    $users = User::query()
        ->when($request->search, fn ($q) => $q
            ->where('name', 'like', "%{$request->search}%")
            ->orWhere('email', 'like', "%{$request->search}%"))
        ->when($request->role, fn ($q) => $q
            ->where('role', $request->role))
        ->when($request->status === 'active', fn ($q) => $q
            ->active())
        ->when($request->sort, fn ($q) => $q
            ->orderBy($request->sort, $request->dir ?? 'asc'))
        ->paginate();
}

// Conditional relationship filtering
$posts = Post::query()
    ->when($request->category, fn ($q) => $q
        ->whereHas('category', fn ($q) => $q
            ->where('slug', $request->category)))
    ->get();
```

## Combining Scopes

```php
// Chain multiple local scopes
$users = User::active()
    ->ofRole('admin')
    ->createdBetween($from, $to)
    ->latest()
    ->get();

// Scope with or conditions
public function scopeActiveOrAdmin($query)
{
    return $query->where(function ($q) {
        $q->where('active', true)
          ->orWhere('role', 'admin');
    });
}
```

## Scopes with Relationships

```php
// Scope that filters by relationship
class Post extends Model
{
    public function scopePublished($query)
    {
        return $query->where('published', true)
            ->whereNotNull('published_at');
    }

    // Scope with relationship join
    public function scopeWithActiveAuthor($query)
    {
        return $query->whereHas('author', fn ($q) => $q->where('active', true));
    }
}

// Usage
$posts = Post::published()
    ->withActiveAuthor()
    ->with('comments')
    ->get();
```

## Dynamic Scopes

```php
// Using the query builder's orWhere method
class User extends Model
{
    public function scopeSearch($query, string $term)
    {
        return $query->where(function ($q) use ($term) {
            $q->where('name', 'like', "%{$term}%")
              ->orWhere('email', 'like', "%{$term}%");
        });
    }
}