# Server Commands

How to start and manage the Laravel Reverb server.

## Basic Start

```bash
php artisan reverb:start
```

Starts Reverb on default host `0.0.0.0` and port `8080`.

## Custom Host and Port

```bash
php artisan reverb:start --host=0.0.0.0 --port=9090
```

## Secure Mode (HTTPS/WSS)

```bash
php artisan reverb:start --host=0.0.0.0 --port=8080 --protocols=ssl
```

This starts WebSocket Secure (WSS) server.

## Process Management

### Background Execution

```bash
# Start in background
php artisan reverb:start > reverb.log 2>&1 &

# Or use supervisor (recommended for production)
# See redis-scaling.md for details
```

### Stopping the Server

```bash
# Find the process
ps aux | grep reverb

# Kill by PID
kill <PID>
```

## Common Options

| Option | Description | Default |
|--------|-------------|---------|
| `--host` | Server host | `0.0.0.0` |
| `--port` | Server port | `8080` |
| `--protocols` | `http` or `ssl` | `http` |

## Production Best Practices

- Use a process supervisor (SupervisorD, systemd) to keep the server running
- Configure auto-restart on failure
- Set up log rotation for output logs