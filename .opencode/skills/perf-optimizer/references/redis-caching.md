# Redis Caching Patterns in Laravel

Redis is Laravel's primary caching driver. This reference covers implementation patterns, invalidation strategies, and common pitfalls.

## Configuration

### Install
```bash
composer require predis/predis
# Or use phpredis PHP extension (faster)
```

### config/cache.php
```php
'redis' => [
    'client' => env('REDIS_CLIENT', 'phpredis'),
    'default' => [
        'host' => env('REDIS_HOST', '127.0.0.1'),
        'port' => env('REDIS_PORT', '6379'),
        'database' => env('REDIS_CACHE_DB', '0'),
        'password' => env('REDIS_PASSWORD'),
    ],
    'cache' => [
        'host' => env('REDIS_HOST', '127.0.0.1'),
        'port' => env('REDIS_PORT', '6379'),
        'database' => env('REDIS_CACHE_DB', '1'), // Separate DB for cache
    ],
],
```

## Basic Cache Operations

### Store Data
```php
// Basic
Cache::put('key', $value, $seconds);
// or
Cache::put('key', $value, now()->addMinutes(10));

// Only if not exists
Cache::add('key', $value, $seconds); // Returns bool
```

### Retrieve Data
```php
// Simple
$value = Cache::get('key');

// With default
$value = Cache::get('key', 'default');
$value = Cache::get('key', function () {
    return expensiveOperation();
});
```

### Delete Data
```php
Cache::forget('key');
Cache::flush(); // Warning: clears ALL cache
```

## Caching Patterns

### 1. Remember Pattern (Cache-Aside)
```php
// Cache-Aside: Check, else compute and store
$users = Cache::remember('users:active', $seconds, function () {
    return User::where('active', true)->get();
});
```

### 2. Remember Forever
```php
// Use carefully - requires manual invalidation
Cache::rememberForever('settings', function () {
    return Settings::all()->keyBy('key');
});
```

### 3. Tags (Namespace)
```php
// Tag-based invalidation
Cache::tags(['posts', 'user:1'])->put('key', $data, $seconds);
Cache::tags(['posts'])->flush(); // Invalidate all posts

// Common tags
Cache::tags(['posts', 'user:'.$userId])->get('some_key');
```

### 4. Lock (Mutual Exclusion)
```php
// Prevent stampede
Cache::lock('processing:'.$id, 10)->block(function () {
    // Only one process enters
    expensiveOperation();
});
```

## Invalidation Strategies

### 1. Time-Based (TTL)
```php
// Simple expiration
Cache::put('dashboard', $data, now()->addHour());
// Automatically expires
```

### 2. Event-Based
```php
// In Event
Event::listen(PostUpdated::class, function ($event) {
    Cache::forget('post:'.$event->post->id);
    Cache::forget('posts:latest');
});

// Or in Observer
public function updated(Post $post): void
{
    Cache::forget('post:'.$post->id);
    Cache::forget('posts:latest');
}
```

### 3. Tag-Based
```php
// Invalidate related cache
Post::saved(function (Post $post) {
    Cache::tags(['posts'])->flush();
    Cache::tags(['user:'.$post->user_id])->flush();
});
```

### 4. Version-Based
```php
// Increment version on changes
Cache::put('cache_version', $version, now()->addYear());
$current = Cache::get('cache_version');

Cache::remember('data:v'.$current, function () {
    // Only recomputes when version changes
});
```

## Entity Caching

### 1. Single Entity
```php
// Cache single record
Cache::remember("user:{$user->id}", $seconds, function () use ($user) {
    return $user->fresh();
});

// In model
public static function cachedFind($id)
{
    return Cache::remember("user:{$id}", 3600, function () use ($id) {
        return static::findOrFail($id);
    });
}
```

### 2. Collections
```php
// Cache collections
Cache::remember('posts:visible', 1800, function () {
    return Post::where('visible', true)
        ->with('user')
        ->orderBy('created_at', 'desc')
        ->limit(50)
        ->get();
});
```

### 3. Query Results
```php
// Cache complex queries
Cache::remember('dashboard:stats', 300, function () {
    return [
        'users_count' => User::count(),
        'posts_count' => Post::count(),
        'revenue' => Order::where('status', 'completed')->sum('total'),
    ];
});
```

## Serializer (Optimization)

### config/cache.php
```php
'redis' => [
    'client' => 'phpredis',
    'default' => [
        'host' => env('REDIS_HOST', '127.0.0.1'),
        'port' => env('REDIS_PORT', '6379'),
        'database' => env('REDIS_CACHE_DB', '0'),
        'cache' => [
            'driver' => 'redis',
            'connection' => 'cache',
            'lock_connection' => 'default',
        ],
        'serializer' => 'igbinary', // More efficient than default
        'compression' => 'zstd',   // Zstandard > LZ4 > gzip
    ],
],
```

## Performance Metrics

| Operation | Cache Hit | Database | Improvement |
|-----------|----------|----------|--------------|
| Single user lookup | 0.5ms | 15ms | 30x |
| Dashboard stats | 2ms | 300ms | 150x |
| Posts list (paginated) | 5ms | 250ms | 50x |

## Common Pitfalls

### 1. Serializing Objects
```php
// Anti-pattern: Serializes full objects
Cache::put('user', $userModel, 3600);

// Fixed: Store arrays/IDs
Cache::put('user:'.$id, ['id' => $id, 'name' => $name], 3600);
```

### 2. Cache Stampede
```php
// Anti-pattern: Multiple processes compute same thing
$users = Cache::remember('users', 60, function () {
    // Several requests hit this simultaneously
    return User::all();
});

// Fixed: Use lock
Cache::lock('users', 10)->block(function () {
    return User::all();
});
```

### 3. Invalidation Race
```php
// Anti-pattern: Stale data
// Fixed: Use versioning or events
```

### 4. Key Collisions
```php
// Fixed: Use namespaced keys
Cache::put("posts:{$post->id}:comments", ...);
Cache::put("posts:latest", ...);
```

## Laravel Octane Considerations

### Swoole Tables (Faster Than Redis)
```php
// config/octane.php
'tables' => [
    'ephemeral' => [
        // Fast in-memory table
        'posts:1000',
    ],
],
```

### Redis in Octane
```php
// Redis persists between requests
Cache::put('shared_data', $data, 0); // Survives worker restarts
```

## Best Practices

1. **Use separate Redis DBs** for cache vs. sessions vs. queue
2. **Set appropriate TTLs** - longer for rare data, shorter for frequent updates
3. **Always provide fallbacks** - database when cache misses
4. **Monitor hit rate** - Redis INFO stats
5. **Use meaningful keys** - `entity:id:relation` pattern