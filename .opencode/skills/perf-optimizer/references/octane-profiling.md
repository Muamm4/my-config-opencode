# Laravel Octane/Swoole Profiling

Laravel Octane runs on Swoole or Revol PHP workers for high-performance serving. This reference covers profiling, optimization, and common issues.

## Installation

```bash
composer require laravel/octane
php artisan octane:install
```

## Configuration

### config/octane.php
```php
return [
    'workers' => env('OCTANE_WORKER', 'swoole'),
    
    'worker_options' => [
        'worker_num' => env('OCTANE_WORKERS', cpu_count() * 2),
        'max_request' => 20000, // Recycle worker after N requests
    ],
    
    'tables' => [
        'ephemeral' => [
            // Fast in-memory storage
            'sessions:1000',
        ],
    ],
    
    'warm' => env('OCTANE_WARM', true),
];
```

## Profiling Tools

### 1. Laravel Telescope Octane
```bash
composer require laravel/telescope --dev
php artisan telescope:install
```

Add to `bootstrap/app.php`:
```php
->withDebugging()
->withTelescope();
```

### 2. Custom Metrics
```php
// In middleware or service provider
Octane::tick('metric_name', function ($table) {
    $table->increment('requests');
});
```

### 3. Swoole Status
```bash
php artisan octane:status
```

## Memory Profiling

### Memory Leaks
```php
// Add to baseline or early in request
$initialMemory = memory_get_usage(true);

// Log near end
$peakMemory = memory_get_peak_usage(true);
Log::info('Memory', [
    'initial' => $initialMemory,
    'peak' => $peakMemory,
    'used' => $peakMemory - $initialMemory,
]);
```

### Table-Based Memory
```php
// Use Swoole tables for in-memory storage
use Laravel\Octane\Summaries\Memory;

$table = Memory::table('sessions');

// In your code
$table->set($sessionId, ['data' => serialize($sessionData)]);
$sessionData = $table->get($sessionId);
```

## Common Performance Issues

### 1. Memory Leaks
**Symptoms**: Worker memory grows indefinitely
**Cause**: Variables not released, closures, event listeners

```php
// Anti-pattern: Closure capture
app()->bind('client', function () use ($someHugeArray) {
    return new Client($someHugeArray);
});

// Fixed: Clear references
app()->singleton('client', function () {
    return new Client(config('client.options'));
});
```

### 2. State Mutating Singletons
```php
// Anti-pattern: Singleton mutates
app()->singleton(DataStore::class, function () {
    $store = new DataStore;
    $store->load(data); // Loads once, but may mutate
    return $store;
});

// Fixed: Use request-scoped or re-load
app()->singleton(DataStore::class, DataStore::class);
$store = app(DataStore::class);
$store->fresh(); // Reload per request
```

### 3. Connection Pool Exhaustion
```php
// Anti-pattern: New connection per request
DB::connection()->.. 
// Creates connection each time

// Fixed: Reuse connections
DB::purge('mysql'); // Clear stale connections
// Already handled by Octane
```

### 4. Event Listeners Accumulating
```php
// Anti-pattern: Re-registering listeners
Event::listen('User.created', function () { ... });
// Called from service provider, repeats on worker request

// Fixed: Once in boot
// app/Providers/EventServiceProvider.php
protected $listen = [
    'User.created' => [SendWelcomeEmail::class],
];
```

## Optimization Patterns

### 1. Warm Boot
```php
// config/octane.php
'warmed_by' => [
    // Pre-load expensive operations
    App\Http\Controllers\HomeController::class,
    // ...
],
```

### 2. Prepared Statements
```php
// Cache the query, not the result
// $statement = DB::select('SELECT * FROM users WHERE id = ?');
// Prepared statements are cached by the database driver

// For complex queries, use Query Builder caching
$query = DB::table('users')->where('active', true);
$query->useWritePdo(); // Force write connection
```

### 3. Coroutines (Swoole Only)
```php
use Swoole\Coroutine;

// Concurrent database operations
Coroutine::join([
    function () {
        $posts = Post::all();
    },
    function () {
        $users = User::all();
    },
]);
```

## Worker Tuning

### Worker Count (cpu_count * 2)
```bash
# .env
OCTANE_WORKERS=8
```

### Max Requests (Worker Recycle)
```php
// config/octane.php
'max_request' => 10000, // Lower if memory leaks
```

### Task Workers (Background Jobs)
```php
// config/octane.php
'task_workers' => env('OCTANE_TASK_WORKERS', 2),
```

## Startup Profiling

### Octane Boot Time
```bash
php artisan octane:boot --profile
```

### Warmup Issues
```php
// If warmup takes too long,
// disable unnecessary warmers in
// app/Octane/Warmers/
```

## Metrics to Monitor

| Metric | Target | Warning |
|--------|--------|---------|
| Memory per worker | <256MB | >512MB |
| Response time | <100ms | >500ms |
| Worker count | cpu * 2 | At capacity |
| Request queue | 0 | >100 |

## Octane in Production

### Supervisor Config
```ini
# /etc/supervisor/conf.d/octane.conf
[program:octane]
command=php /var/www/artisan octane:serve
process_name=%(program_name)s_%(process_num)02d
numprocs=4
directory=/var/www
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
stopwaitsecs=10
user=www-data
stdout_logfile=/var/log/octane.log
redirect_stderr=true
```

## Troubleshooting

```bash
# Check worker status
php artisan octane:status

# Reload workers (zero-downtime)
php artisan octane:reload

# Stop workers
php artisan octane:stop
```

### Enable Coredump
```php
// In development only
\Cohensive\Octane\Swoole\Settings::configure([
    'max_coroutine_dump_level' => 1,
]);
```