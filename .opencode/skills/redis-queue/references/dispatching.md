# Job Dispatching Patterns

## Basic Dispatch

```php
use App\Jobs\ProcessPodcast;

// Fire immediately
ProcessPodcast::dispatch($podcast);

// With method chaining
ProcessPodcast::dispatch($podcast)
    ->onQueue('podcasts')
    ->onConnection('redis');
```

## Delay Dispatch

```php
use App\Jobs\ProcessPodcast;
use Carbon\Carbon;

// Relative delay
ProcessPodcast::dispatch($podcast)
    ->delay(now()->addMinutes(10));

// Absolute time
ProcessPodcast::dispatch($podcast)
    ->delay(Carbon::tomorrow());
```

## Queue Selection

```php
// Specific queue
ProcessPodcast::dispatch($podcast)->onQueue('podcasts');

// High priority queue
ProcessPodcast::dispatch($podcast)->onQueue('high');

// Default queue
ProcessPodcast::dispatch($podcast)->onQueue('default');
```

## Timeout & Retry

```php
// Custom timeout (seconds)
ProcessPodcast::dispatch($podcast)
    ->timeout(300);

// Custom tries
ProcessPodcast::dispatch($podcast)
    ->tries(5);

// Chain settings
ProcessPodcast::dispatch($podcast)
    ->timeout(300)
    ->tries(3)
    ->delay(now()->addMinutes(5))
    ->onQueue('podcasts');
```

## Conditional Dispatch

```php
// Only queue if not in test environment
ProcessPodcast::dispatchIf(
    !app()->environment('testing'),
    $podcast
);

// Queue unless feature is disabled
ProcessPodcast::dispatchUnless(
    !feature_enabled('podcast_processing'),
    $podcast
);
```

## Chained Jobs

```php
use Illuminate\Support\Facades\Bus;

Bus::chain([
    new ProcessPodcast($podcast),
    new NotifyUserOfCompletedPodcast($podcast),
    new UpdatePodcastStats($podcast),
])->dispatch();
```

## Batch Dispatch

```php
use Illuminate\Support\Facades\Bus;

Bus::batch([
    new ProcessPodcast($podcast1),
    new ProcessPodcast($podcast2),
    new ProcessPodcast($podcast3),
])
    ->name("process-podcast-{$podcast->id}")
    ->dispatch();
```

## Sync Dispatch (Testing)

```php
use App\Jobs\ProcessPodcast;

// Execute immediately (no queue)
ProcessPodcast::dispatchSync($podcast);

// With delay even in sync
ProcessPodcast::dispatch($podcast)->delay(now()->addMinutes(5));
```

## Job Middleware

```php
class ProcessPodcast implements ShouldQueue
{
    public function middleware(): array
    {
        return [
            new ThrottlesHits(10),  // Rate limiting
            new RedisLock,        // Prevent duplicates
        ];
    }
}
```

## Dispatch from Controllers

```php
class PodcastController extends Controller
{
    public function store(Request $request): RedirectResponse
    {
        $podcast = Podcast::create($request->validated());

        ProcessPodcast::dispatch($podcast)
            ->onQueue('podcasts')
            ->delay(now()->addSeconds(30));

        return redirect()->route('podcasts.show', $podcast);
    }
}
```

## Best Practices

1. **Queue selection**: Use queues for job priority (high, default, low)
2. **Timeout**: Set reasonable timeouts to prevent zombie jobs
3. **Delay**: Use for rate limiting external APIs
4. **Batching**: Group related jobs for better monitoring