# Performance Measurement and Metrics

Measuring performance is essential before and after optimizations. This reference covers instrumentation, benchmarking, and metrics analysis.

## Response Time Measurement

### Middleware-Based
```php
// app/Http/Middleware/MeasureResponseTime.php
public function handle($request, Closure $next)
{
    $start = microtime(true);
    
    $response = $next($request);
    
    $duration = (microtime(true) - $start) * 1000;
    
    $response->headers->set('X-Response-Time', $duration.'ms');
    
    // Log slow requests
    if ($duration > 500) {
        Log::warning("Slow request", [
            'path' => $request->path(),
            'duration' => $duration,
        ]);
    }
    
    return $response;
}
```

### Route-Based (Closure)
```php
Route::get('/users/{user}', function (User $user) {
    $start = microtime(true);
    
    $response = $user->load('posts');
    
    logger('User load: ' . ((microtime(true) - $start) * 1000) . 'ms');
    
    return $response;
});
```

## Database Query Profiling

### Query Logging
```php
// config/database.php
'log_queries' => env('DB_LOG_QUERIES', false),
```

### With Query Count
```php
// In service provider
DB::listen(function ($query) {
    Log::info($query->sql, [
        'bindings' => $query->bindings,
        'time' => $query->time,
    ]);
});
```

### Count Queries Per Request
```php
trait QueryCounter
{
    public static function countQueries($callback)
    {
        $count = 0;
        
        DB::listen(function ($query) use (&$count) {
            $count++;
        });
        
        $callback();
        
        return $count;
    }
}

// Usage
$queryCount = QueryCounter::countQueries(function () {
    $user = User::with('posts')->first();
});
```

## Profiling with Clockwork

```bash
composer require--dev clockwork/clockwork
```

Access: `/clockwork` in development
Shows: Timeline, Queries, Memory, Custom events

## Benchmarking Patterns

### 1. Simple Benchmark
```php
function benchmark(callable $callback, int $iterations = 1000)
{
    // Warmup
    for ($i = 0; $i < 10; $i++) {
        $callback();
    }
    
    // Measure
    $start = microtime(true);
    
    for ($i = 0; $i < $iterations; $i++) {
        $callback();
    }
    
    $duration = (microtime(true) - $start) * 1000;
    
    return [
        'total' => $duration,
        'avg' => $duration / $iterations,
        'per_sec' => $iterations / ($duration / 1000),
    ];
}

// Usage
$result = benchmark(fn() => User::where('active', true)->count());
logger($result['avg']); // ~0.5ms
```

### 2. Comparison Benchmark
```php
function compare(string $name, callable $old, callable $new, int $iterations = 100)
{
    $oldResult = benchmark($old, $iterations);
    $newResult = benchmark($new, $iterations);
    
    $improvement = (($oldResult['total'] - $newResult['total']) / $oldResult['total']) * 100;
    
    return [
        'name' => $name,
        'old' => $oldResult,
        'new' => $newResult,
        'improvement' => $improvement.'%',
    ];
}
```

### 3. HTTP Benchmark
```bash
# Apache Bench
ab -n 100 -c 10 http://localhost/api/endpoint

# Using wrk (better)
wrk -t10 -c100 -d30s http://localhost/api/endpoint

# Single request
time curl -w "@curl-format.txt" -o /dev/null -s http://localhost/api/endpoint
```

### curl-format.txt
```
\n
     DNS: %{time_namelookup}s\n
  Connect: %{time_connect}s\n
   SSL: %{time_appconnect}s\n
      Start: %{time_starttransfer}s\n
       Total: %{time_total}s\n
      Speed: %{speed_download}B/s\n
```

## Key Metrics

### 1. Response Time

| Endpoint | Before | After | Target |
|----------|--------|-------|--------|
| API /users | 450ms | 45ms | <100ms |
| Web /dashboard | 1200ms | 200ms | <500ms |
| API /search | 800ms | 80ms | <200ms |

### 2. Queries Per Request

| Endpoint | Before | After | Target |
|----------|--------|-------|--------|
| User list | 45 | 3 | <5 |
| Dashboard | 150 | 12 | <20 |
| API /export | 500 | 50 | <100 |

### 3. Memory Usage

| Component | Before | After | Target |
|-----------|--------|-------|--------|
| Octane worker | 256MB | 128MB | <200MB |
| Single request | 50MB | 15MB | <30MB |
| Export job | 512MB | 100MB | <200MB |

### 4. Throughput

| Scenario | Before | After | Target |
|----------|--------|-------|--------|
| Concurrent users | 50 | 500 | >1000 |
| Requests/sec | 100 | 800 | >500 |
| Time to first byte | 200ms | 20ms | <50ms |

## Performance Baselines

### Creating a Baseline
```php
// routes/api.php
Route::get('/benchmark', function () {
    return [
        'php_version' => PHP_VERSION,
        'laravel_version' => app()->version(),
        'memory_limit' => ini_get('memory_limit'),
        'max_execution_time' => ini_get('max_execution_time'),
        'database' => DB::connection()->getDriverName(),
        'cache' => config('cache.default'),
        'octane' => extension_loaded('swoole') ? 'swoole' : 'fpm',
    ];
});
```

### Automated Reporting
```php
// Schedule in routes/console.php
Schedule::call(function () {
    $metrics = $this->collectMetrics();
    
    // Send to monitoring
    Notification::send($metrics);
})->daily();
```

## Monitoring Tools

### Laravel Pulse
```bash
composer require laravel/pulse
php artisan pulse:install
```

Shows: Requests, Slow requests, Memory, Database queries

### New Relic / Scout APM
```bash
composer require newrelic/php-agent
```

### Custom Dashboard
```php
// Store metrics
Metric::record('response_time', $duration);
Metric::record('query_count', $queryCount);
Metric::record('memory', memory_get_usage(true));
```

## Before/After Reporting

Always provide expected improvement:
```php
// Example response format
return [
    'optimization' => 'N+1 resolution',
    'before' => [
        'queries' => 45,
        'time' => '450ms',
    ],
    'after' => [
        'queries' => 3,
        'time' => '45ms',
    ],
    'improvement' => '90% faster, 93% fewer queries',
];
```

## Testing Performance

### Pest Test
```php
it('loads users efficiently', function () {
    $queryCount = 0;
    
    DB::listen(fn($q) => $queryCount++);
    
    $users = User::with('posts')->get();
    
    expect($queryCount)->toBeLessThanOrEqual(3);
});
```

### Load Testing
```php
// Using k6
// load-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
    vus: 100,
    duration: '30s',
};

export default function () {
    const res = http.get('http://localhost/api/users');
    check(res, { 'status is 200': r => r.status === 200 });
    sleep(1);
}
```