# Octane / Swoole

## Overview

Laravel Octane boots Laravel once and keeps it in memory, providing ~10x performance improvement over traditional FPM.

## Key Concepts

1. **Single Boot**: Laravel initializes once, not per request
2. **Memory Persistence**: Application state persists across requests
3. **Swoole Worker**: Handles multiple concurrent requests
4. **Reload Required**: Code changes need `octane:reload`

## Starting Octane

```bash
# Basic start
php artisan octane:start --host=0.0.0.0 --port=8000

# With custom worker count
php artisan octane:start --workers=4

# Using composer script
composer dev:octane
```

## Reloading

After making code changes:
```bash
php artisan octane:reload
```

## Important Notes

- **State Management**: Be careful with static variables and singleton bindings that hold request-specific data
- **Database Connections**: Connections persist - use `DB::purge()` if needed
- **Queue Workers**: Use `queue:restart` after code changes
- **Swoole Tables**: Use for cross-request state sharing

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Changes not reflected | Run `php artisan octane:reload` |
| Memory leaks | Check for unbounded caching |
| Connection issues | Use `DB::connection()->disconnect()` in bootstrap |