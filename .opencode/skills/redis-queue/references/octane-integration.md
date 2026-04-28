# Octane + Redis Integration

## Starting Octane Server

```bash
# Basic start
php artisan octane:start --host=0.0.0.0 --port=8000

# With TTY (for interactive commands)
php artisan octane:start --host=0.0.0.0 --port=8000 --watch

# HTTPS
php artisan octane:start --host=0.0.0.0 --port=8000 --https

# Daemon mode
php artisan octane:start --daemonize
```

## Worker Management

```bash
# Reload workers (zero-downtime)
php artisan octane:reload

# Stop server
php artisan octane:stop

# Status
php artisan octane:status
```

## Swoole Configuration

```php
// config/octane.php

return [
    'driver' => env('OCTANE_DRIVER', 'swoole'),

    'swoole' => [
        'worker_num' => env('SWOOLE_WORKER_NUM', 'auto'),
        'daemonize' => false,
        'max_request' => 10000,
        'task_worker_num' => env('SWOOLE_TASK_WORKERS', 'auto'),
    ],

    'reloading' => [
        'enabled' => true,
        'paths' => [
            base_path('app/Http/Controllers'),
            base_path('app/Models'),
            resource_path('views'),
        ],
    ],

    'serving' => [
        'expose' => env('OCTANE_EXPOSE', false),
        'https' => env('OCTANE_EXPOSE', false),
    ],
];
```

## Redis with Octane

### Queue Configuration

```php
// Queue uses Redis via Octane workers
// No external queue worker needed!

// Jobs are processed by Octane worker
class ProcessPodcast implements ShouldQueue
{
    public function handle(): void
    {
        // Runs in Octane context
    }
}
```

### Cache Configuration

```php
// config/cache.php
'redis' => [
    'driver' => 'redis',
    'connection' => 'cache',
    'lock_connection' => 'default',
],
```

## Performance Benefits

| Feature | Benefit |
|---------|----------|
| Persistent workers | No bootstrap overhead |
| In-memory caching | Faster cache access |
| Task workers | Async job processing |
| Coroutines | Concurrent operations |

## Queue Driver with Octane

```bash
# With Octane, you have two options:

# Option 1: Octane handles queues
# (No separate queue:work needed!)
php artisan octane:start

# Option 2: Separate worker (recommended for heavy load)
php artisan octane:start
php artisan queue:work redis --queue=high,default
```

## Best Practices

1. **Use --watch**: Auto-reload on file changes during dev
2. **Queue**: Use Octane's built-in queue for light jobs
3. **Separate worker**: Use `queue:work` for heavy jobs
4. **Redis**: Configure separate Redis connection for cache vs queue
5. **Swoole**: Tune `worker_num` based on CPU cores

## Common Issues

```bash
# Worker crashed
php artisan octane:reload

# Memory leak
php artisan octane:stop && php artisan octane:start

# Jobs not executing
# Check: QUEUE_CONNECTION=redis in .env
```