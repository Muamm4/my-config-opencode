# Laravel Redis Configuration

## Environment Variables

```env
# Connection
REDIS_HOST=127.0.0.1
REDIS_PORT=6379
REDIS_PASSWORD=null

# Cache
CACHE_STORE=redis

# Queue
QUEUE_CONNECTION=redis
```

## Configuration Options

### Connection Options

- `REDIS_CLIENT`: phpredis (default) or predis
- `REDIS_PREFIX`: Key prefix (default: `laravel_database_`)

### Cache TTL Defaults

```php
// config/cache.php
'redis' => [
    'driver' => 'redis',
    'connection' => 'cache',
    'lock_connection' => 'default',
],
```

### Queue Configuration

```php
// config/queue.php
'redis' => [
    'driver' => 'redis',
    'connection' => 'default',
    'queue' => 'default',
    'retry_after' => 90,
    'block_for' => null,
],
```

## Best Practices

1. Use `REDIS_CLIENT=phpredis` for better performance (compiled extension)
2. Set a meaningful `REDIS_PREFIX` to avoid key collisions
3. Configure `retry_after` based on your longest job execution time
4. Use separate Redis connections for cache vs queue to prevent blocking