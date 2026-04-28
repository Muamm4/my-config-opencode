# N+1 Query Detection and Resolution

N+1 queries are the most common performance killer in Laravel/PostgreSQL applications. This reference covers detection, prevention, and resolution patterns.

## Detection Tools

### Laravel Debugbar
- Install: `composer require --dev barryvdh/laravel-debugbar`
- Shows all queries with execution time
- Look for repeated similar queries in the timeline

### Laravel Telescope
- `php artisan telescope:install`
- Visit `/telescope` in development
- Filter by "Queries" panel
- Look for duplicate queries with different parameters

### Query Logging (Production)
```php
// config/database.php
'profiling' => [
    'enabled' => env('DB_PROFILING', false),
    'log_slow_queries' => env('DB_LOG_SLOW', true), // >100ms
],
```

## Detection Patterns

### 1. Eloquent Relationship Queries
```php
// Anti-pattern: N+1
$users = User::all();
foreach ($users as $user) {
    echo $user->posts->count(); // Each iteration = 1 query
}

// Fixed: Eager loading
$users = User::with('posts')->get();
foreach ($users as $user) {
    echo $user->posts->count(); // 2 queries total (users + posts)
}

// Fixed: Nested eager loading
$users = User::with('posts.comments')->get();
```

### 2. Dynamic Relationships
```php
// Anti-pattern
$posts = Post::all();
foreach ($posts as $post) {
    $author = $post->author->name; // N+1
}

// Fixed: with() accepts nested arrays
$posts = Post::with(['author', 'comments', 'comments.user'])->get();
```

### 3. Accessors
```php
// app/Models/User.php
// Always eager load relationships used in accessors
protected $with = ['profile', 'settings'];
```

### 4. Raw SQL Queries
```php
// Anti-pattern: N+1 in loop
$ids = [1, 2, 3];
$users = [];
foreach ($ids as $id) {
    $users[] = DB::table('users')->where('id', $id)->first();
}

// Fixed: WHERE IN clause
$users = DB::table('users')->whereIn('id', $ids)->get();
```

## Resolution Strategies

### 1. Eager Loading (with)
```php
// Single relationship
Model::with('relationship')->get();

// Multiple relationships
Model::with(['posts', 'comments'])->get();

// Nested relationships
Model::with(['posts.comments', 'posts.author'])->get();

// Conditional eager loading
Model::with(['posts' => function ($query) {
    $query->where('active', true);
}])->get();
```

### 2. Lazy Eager Loading (load)
```php
// When you need to load after the fact
$user = User::find($id);
if ($someCondition) {
    $user->load('posts', 'comments');
}
```

### 3. Chunked Eager Loading (chunkById)
```php
// For large datasets to avoid memory issues
User::chunkById(100, function ($users) {
    foreach ($users as $user) {
        // Process with relationships already loaded
    }
});
```

### 4. Cursor (Memory Efficient)
```php
// For very large datasets
foreach (User::cursor() as $user) {
    // Streams from database, one at a time
}
```

## Common Pitfalls

### 1. Nested Relationships Without Prefix
```php
// Problem: Ambiguous
$users = User::with('posts.comments')->get();
// Comments loaded for ALL posts of ALL users

// Solution: Use array syntax
$users = User::with(['posts', 'posts.comments', 'profile'])->get();
```

### 2. Morph Relationships
```php
// Eager load morph relationships
Model::with(['commentable', 'tags'])->get();

// Or with specific morph types
Model::with(['imageable', 'documentable' => function ($q) {
    $q->where('active', true);
}])->get();
```

### 3. Count Without Load
```php
// Anti-pattern
$post->comments->count(); // Loads ALL comments then counts

// Fixed
$post->comments_count; // Requires: Model::withCount('comments')

// Or: withCount method
Post::withCount('comments')->get(); // Adds comments_count attribute
```

## Performance Metrics

| Scenario | Before | After | Improvement |
|----------|--------|-------|-------------|
| 100 users with posts | 101 queries | 2 queries | 98% reduction |
| 1000 users with posts | 1001 queries | 2 queries | 99.8% reduction |
| Pagination (n=15) | N+1 queries | 2 queries | ~85% reduction |

## Testing

```php
// Test for N+1 in PHPUnit
public function test_no_n_plus_one()
{
    $queryCount = 0;
    
    DB::listen(function ($query) use (&$queryCount) {
        $queryCount++;
    });
    
    $users = User::with('posts')->get();
    
    // Assert only 2 queries: users + posts
    $this->assertLessThanOrEqual(2, $queryCount);
}
```