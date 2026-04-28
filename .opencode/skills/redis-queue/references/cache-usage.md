# Laravel Cache with Redis

## Basic Cache Operations

```php
use Illuminate\Support\Facades\Cache;

Cache::put('key', $value, $ttl);
Cache::put('key', $value, now()->addMinutes(10));

$value = Cache::get('key');
$value = Cache::get('key', 'default');

Cache::forget('key');
$exists = Cache::has('key');
```

## Remember Pattern (Cache-aside)

```php
$users = Cache::remember('users', now()->addHour(), function () {
    return User::all();
});

// Or with TTL helper
$users = Cache::remember('users', 3600, function () {
    return User::all();
});
```

## Lock (Mutex)

```php
use Illuminate\Support\Facades\Cache;

// Atomic lock
if (Cache::lock('processing', 30)->get()) {
    try {
        // Process once
    } finally {
        Cache::lock('processing')->release();
    }
}

// Blocking lock (wait for availability)
Cache::lock('processing', 10)->block(30, function () {
    // Wait and process
});
```

## Cache Tags (Redis Only)

```php
use Illuminate\Support\Facades\Cache;

// Tag for grouping
Cache::tags(['posts'])->put('key', $value);
Cache::tags(['posts'])->remember('key1', $ttl, fn() => ...);

// Invalidate tag
Cache::tags(['posts'])->flush();

// Common use: Related content
Cache::tags(['posts', "post:{$post->id}"])->put('key', $value);
Cache::tags(["post:{$post->id}"])->flush();
```

## Increments/Decrements

```php
use Illuminate\Support\Facades\Cache;

// Atomic counter
Cache::increment('visits');
Cache::decrement('visits', 5);

// Initialize if not exists
Cache::put('visits', 0, now()->addDay());
```

## Multiple Keys

```php
// Many keys at once
Cache::putMany([
    'key1' => $value1,
    'key2' => $value2,
], $ttl);

// Many gets
Cache::many(['key1', 'key2']);
```

## Cache Events

```php
// Event listener
// App/Providers/AppServiceProvider.php
public function boot(): void
{
    Cache::macro('rememberWithLock', function ($key, $ttl, $callback) {
        return Cache::remember($key, $ttl, function () use ($key, $callback) {
            return Cache::lock("lock:{$key}", 10)->block(30, $callback);
        });
    });
}
```

## TTL Helpers

```php
// Seconds
now()->addSeconds(60)

// Minutes
now()->addMinutes(10)
now()->addMinutes(10)->diffInSeconds()

// Hours
now()->addHours(2)

// Days
now()->addDays(7)

// For Laravel >= 10
Cache::put('key', $value, \DateInterval)
```

## Best Practices

1. **Use remember**: Avoid cache misses by caching expensive queries
2. **Cache tags**: Use for related content invalidation
3. **Lock**: Prevent race conditions on expensive operations
4. **TTL**: Set appropriate expiry (not too short, not too long)
5. **Prefix**: Configure REDIS_PREFIX to avoid collisions

## Example: User Profile Cache

```php
class UserService
{
    public function getProfile(int $userId): array
    {
        return Cache::remember(
            "user:profile:{$userId}",
            now()->addHours(2),
            fn() => User::findOrFail($userId)->toArray()
        );
    }

    public function updateProfile(int $userId, array $data): void
    {
        User::findOrFail($userId)->update($data);
        Cache::forget("user:profile:{$userId}");
    }
}
```

## Example: Stats Cache

```php
class StatsService
{
    public function getPostStats(int $postId): array
    {
        return Cache::tags(['posts', "post:{$postId}"])
            ->remember("post:{$postId}:stats", 3600, function () {
                return [
                    'views' => $this->getViews($postId),
                    'shares' => $this->getShares($postId),
                ];
            });
    }

    public function incrementViews(int $postId): void
    {
        Cache::increment("post:{$postId}:views");
    }
}
```