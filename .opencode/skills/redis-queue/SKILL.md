# redis-queue

## Identity
You are a Redis specialist for Laravel caching and queues with Octane.

## Instructions
- Use Redis for cache driver
- Use Redis for queue (with Octane)
- Configure for real-time features

## Laravel Redis Setup

```env
REDIS_HOST=127.0.0.1
REDIS_PORT=6379
REDIS_PASSWORD=null

QUEUE_CONNECTION=redis
CACHE_STORE=redis
```

## Redis Commands

```bash
# CLI
redis-cli

# Check connection
redis-cli ping
# PONG

# Set/Get
redis-cli SET mykey value
redis-cli GET mykey

# Keys pattern
redis-cli KEYS "laravel:*"

# Delete
redis-cli DEL cache:key

# Flush all
redis-cli FLUSHDB
```

## Queue Jobs

```php
<?php
class ProcessPodcast implements ShouldQueue
{
    public int $tries = 3;
    public int $backoff = 60;
    public array $chunks = [];
    
    public function handle(): void
    {
        // Process
    }
    
    public function failed(\Throwable $exception): void
    {
        // Notify admin
    }
}
```

## Dispatching

```php
// Queue immediately
ProcessPodcast::dispatch($podcast);

// Delay
ProcessPodcast::dispatch($podcast)->delay(now()->addMinutes(10));

// On queue
ProcessPodcast::dispatch($podcast)->onQueue('podcasts');

// With timeout
ProcessPodcast::dispatch($podcast)->timeout(300);
```

## Queue Commands

```bash
# Start worker
php artisan queue:work redis

# Start supervisor
php artisan queue:supervisor

# Listen
php artisan queue:listen

# Retry failed
php artisan queue:retry

# Failed jobs table
php artisan queue:failed-table
php artisan migrate
```

## Octane + Redis

```bash
# Start with Octane
php artisan octane:start --host=0.0.0.0 --port=8000

# Reload workers
php artisan octane:reload
```

## Cache Usage

```php
// Cache
Cache::put('key', $value, now()->addMinutes(10));
$value = Cache::get('key');

// Remember pattern
$data = Cache::remember('users', now()->addHour(), function () {
    return User::all();
});

// Tags (Redis only)
Cache::tags(['posts', 'users'])->put('key', $value);
```

## Trigger Conditions
- When user asks about Redis
- When mentioning queues
- When asking about caching