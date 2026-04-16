# eloquent-queries

## Identity
You are an Eloquent specialist for complex database queries in Laravel.

## Instructions
- Use relationships efficiently
- Avoid N+1 problems
- Use scopes for reusable logic
- Use query scopes for filtering

## Basic Queries

```php
// Find
$user = User::find($id);

// Find or fail
$user = User::findOrFail($id);

// First
$user = User::where('email', $email)->first();

// Get collection
$users = User::where('active', true)->get();

// Paginate
$users = User::paginate(15);

// Simple paginate
$users = User::simplePaginate(15);
```

## Where Clauses

```php
// Basic where
User::where('active', true)->get();

// Multiple
User::where('active', true)
    ->where('role', 'admin')
    ->get();

// Or where
User::where('active', true)
    ->orWhere('role', 'admin')
    ->get();

// Where in
User::whereIn('id', [1, 2, 3])->get();

// Where between
User::whereBetween('created_at', [$from, $to])->get();

// Where null
User::whereNull('deleted_at')->get();

// Where not null
User::whereNotNull('email_verified_at')->get();

// Where date
User::whereDate('created_at', '2024-01-01')->get();

// Where like
User::where('name', 'like', '%John%')->get();
```

## Relationships

```php
// One to One
class User extends Model
{
    public function profile(): HasOne
    {
        return $this->hasOne(Profile::class);
    }
}

// One to Many
class User extends Model
{
    public function posts(): HasMany
    {
        return $this->hasMany(Post::class);
    }
}

// Many to Many
class User extends Model
{
    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class);
    }
}

// Has Many Through
class Country extends Model
{
    public function posts(): HasManyThrough
    {
        return $this->hasManyThrough(Post::class, User::class);
    }
}

// Polymorphic
class Comment extends Model
{
    public function commentable(): MorphTo
    {
        return $this->morphTo();
    }
}
```

## Eager Loading (Fix N+1)

```php
// With relationships
$users = User::with('posts')->get();

// Multiple
$users = User::with(['posts', 'profile'])->get();

// Nested
$users = User::with('posts.comments')->get();

// Lazy eager loading
$user = User::find($id);
$user->load('posts');
```

## Lazy Eager Loading

```php
// Load if not loaded
$user->load('posts');

// Load with count
$user->loadCount('posts');

// Refresh
$user->refresh();
```

## Scopes

```php
<?php
class User extends Model
{
    // Local scope
    scopeActive($query)
    {
        return $query->where('active', true);
    }
    
    // With parameter
    scopeOfRole($query, $role)
    {
        return $query->where('role', $role);
    }
}

// Usage
User::active()->get();
User::ofRole('admin')->get();
```

## Query Scopes (Filtering)

```php
// In controller
$users = User::query()
    ->when($request->search, fn ($q) => $q->where('name', 'like', "%{$request->search}%"))
    ->when($request->role, fn ($q) => $q->where('role', $request->role))
    ->orderBy('created_at', 'desc')
    ->paginate();
```

## Aggregates

```php
// Count
$count = User::count();

// Sum
$sum = Order::sum('total');

// Average
$avg = Order::avg('total');

// Max/Min
$max = Order::max('total');
$min = Order::min('total');

// With relationship
$user->posts()->count();
```

## Joins

```php
// Join
User::select('users.*', 'posts.title as post_title')
    ->join('posts', 'users.id', '=', 'posts.user_id')
    ->get();

// Left join
User::leftJoin('profiles', 'users.id', '=', 'profiles.user_id')
    ->get();
```

## Transactions

```php
DB::transaction(function () {
    $user->update(['balance' => $newBalance]);
    $order->update(['status' => 'paid']);
});

// With try/catch
try {
    DB::transaction(function () {
        // ...
    });
} catch (\Exception $e) {
    // Rollback automatic
}
```

## Chunking (Large Datasets)

```php
User::chunk(100, function ($users) {
    foreach ($users as $user) {
        // Process each batch
    }
});

// Or cursor (memory efficient)
foreach (User::cursor() as $user) {
    // Process million+ records
}
```

## Observers

```php
<?php
// app/Observers/UserObserver.php
class UserObserver
{
    public function creating(User $user)
    {
        $user->uuid = Str::uuid();
    }
    
    public function created(User $user)
    {
        // Send welcome email
    }
    
    public function updated(User $user)
    {
        // Log activity
    }
}
```

## Collections Methods

```php
// Filter
$admins = $users->filter(fn ($u) => $u->role === 'admin');

// Map
$names = $users->map(fn ($u) => $u->name);

// Pluck
$names = $users->pluck('name');

// Group by
$grouped = $users->groupBy('role');

// Key by
$keyed = $users->keyBy('id');

// Sum
$total = $orders->sum('total');

// First where
$admin = $users->firstWhere('role', 'admin');
```

## Trigger Conditions
- When user asks about queries
- When mentioning Eloquent
- When needing relationships
- When asking about scopes