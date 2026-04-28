# Queue Artisan Commands

## Worker Commands

```bash
# Start worker (supervisord)
php artisan queue:work redis

# Specific queue
php artisan queue:work redis --queue=podcasts

# Multiple queues (in priority order)
php artisan queue:work redis --queue=high,default,low

# Don't daemonize
php artisan queue:work redis --once

# Stop on first error
php artisan queue:work redis --stop-on-error

# Memory limit (MB)
php artisan queue:work redis --memory=256
```

## Supervisor (Recommended)

```bash
# Start supervisor
php artisan queue:supervisor

# Or with config file
php artisan queue:supervisor --config=supervisor.yml
```

### Supervisor Config (supervisor.yml)

```yaml
# config/supervisor.yml
supervisor:
  queue:high:
   :
      worker: redis
      queue: high
      balance: simple
    minproz: 2
    maxproz: 2

  queue:default:
    command: 'php artisan queue:work redis'
    user: www-data
    queue: default
    minproz: 2
    maxproz: 4
    stopwaitsecs: 30
```

## Queue Listen

```bash
# Listen to queue
php artisan queue:listen

# Specific connection
php artisan queue:listen redis

# Queue
php artisan queue:listen redis --queue=podcasts
```

## Failed Jobs

```bash
# List failed jobs
php artisan queue:retry

# Retry specific ID
php artisan queue:retry --id=uuid

# Retry all
php artisan queue:retry --all

# Flush failed jobs
php artisan queue:flush
```

## Failed Jobs Table

```bash
# Create table
php artisan queue:failed-table

# Migrate
php artisan migrate
```

## Additional Commands

```bash
# Restart worker (graceful reload)
php artisan queue:restart

# Purge queue
php artisan queue:clear redis
php artisan queue:clear redis --queue=podcasts

# Monitor (real-time)
php artisan queue:monitor redis --max=100
```

## Common Options

| Option | Description |
|--------|-------------|
| `--queue` | Queue name(s) to process |
| `--once` | Process one job then exit |
| `--stop-on-error` | Exit on first error |
| `--memory` | Memory limit in MB |
| `--timeout` | Job timeout in seconds |
| `--sleep` | Seconds to sleep when no job |
| `--max-time` | Max runtime in seconds |
| `--max-jobs` | Max jobs to process |

## Daemon vs Once

```bash
# Daemon (persistent)
php artisan queue:work redis
# Runs until killed or memory exceeded

# Once (one job per call)
php artisan queue:work redis --once
# Good for cron jobs
```

## Best Practices

1. **Use supervisor**: Auto-restarts workers on failure
2. **Queue priority**: Use multiple queues with priorities
3. **Memory limit**: Keep at 256MB to prevent memory leaks
4. **Graceful restart**: Use `queue:restart` to reload workers
5. **Monitor failed**: Set up alerts for failed jobs