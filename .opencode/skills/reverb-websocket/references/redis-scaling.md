# Redis Scaling

How to scale Laravel Reverb with Redis for multiple server instances.

## Configuration

In `config/reverb.php`:

```php
return [
    'pusher' => [
        'driver' => 'pusher',
        'key' => env('REVERB_APP_KEY'),
        'secret' => env('REVERB_APP_SECRET'),
        'app_id' => env('REVERB_APP_ID'),
        'cluster' => env('PUSHER_APP_CLUSTER'),
        'useTLS' => true,
    ],

    'database' => [
        'driver' => 'database',
        'connection' => 'redis',
        'channel' => 'reverb',
        'queue' => 'default',
    ],
];
```

## Redis Driver Benefits

- Multiple Reverb instances can share state
- Horizontal scaling becomes possible
- Messages are persisted in Redis queue
- Automatic reconnection handling

## Supervisor Configuration

For production, use Supervisor to manage the Reverb process:

```ini
# /etc/supervisor/conf.d/reverb.conf
[program:reverb]
process_name=%(program_name)s
command=php /path/to/artisan reverb:start
autostart=true
autorestart=true
user=www-data
redirect_stderr=true
stdout_logfile=/var/log/reverb.log
stopwaitsecs=3600
```

## Start Multiple Instances

```bash
# Each on different port
php artisan reverb:start --host=0.0.0.0 --port=8080
php artisan reverb:start --host=0.0.0.0 --port=8081
```

All instances connect to the same Redis backend.

## Queue Worker

The database driver uses Laravel queues:

```bash
php artisan queue:work redis --queue=reverb --sleep=3
```

## Best Practices

1. Use Redis Sentinel for high availability
2. Configure appropriate queue connection
3. Monitor Redis memory usage
4. Set up log rotation
5. Use connection pooling for high traffic