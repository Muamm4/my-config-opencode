# Laravel Queue Jobs

## Basic Job Structure

```php
<?php

namespace App\Jobs;

use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class ProcessPodcast implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, SerializesModels;

    public int $tries = 3;
    public int $backoff = 60;
    public array $chunks = [];
    public int $timeout = 300;

    public function __construct(
        public Podcast $podcast
    ) {}

    public function handle(): void
    {
        // Process the podcast
        $this->podcast->process();
    }

    public function failed(\Throwable $exception): void
    {
        // Notify admin when job fails permanently
        \Log::error('Podcast processing failed', [
            'podcast_id' => $this->podcast->id,
            'exception' => $exception->getMessage(),
        ]);
    }
}
```

## Job Properties

| Property | Type | Description |
|----------|------|-------------|
| `$tries` | int | Number of attempts before marked failed |
| `$backoff` | int/array | Seconds to wait before retry (array for exponential) |
| `$timeout` | int | Max seconds the job can run |
| `$chunks` | array | Number of items to process per chunk |
| `$chunkSize` | int | Items per chunk (default: 100) |
| `$deleteWhenMissingModels` | bool | Delete job if model is missing |

## Retry Strategies

```php
class ProcessPodcast implements ShouldQueue
{
    // Simple backoff: wait 60 seconds between retries
    public int $backoff = 60;

    // Exponential backoff: wait 60, 300, 600 seconds
    public array $backoff = [60, 300, 600];

    // Custom retry logic
    public int $tries = 5;

    public function retryUntil(): \DateTime
    {
        return now()->addDays(3);
    }
}
```

## Chunking Large Datasets

```php
class ProcessPodcast implements ShouldQueue
{
    public int $tries = 3;
    public int $backoff = 60;

    public function handle(): void
    {
        $podcast->episodes()
            ->chunkById(100, function ($episodes) {
                foreach ($episodes as $episode) {
                    // Process each episode
                }
            });
    }
}
```

## Batched Jobs (Laravel 9+)

```php
use Illuminate\Bus\Batch;
use Illuminate\Support\Facades\Bus;

$batch = Bus::batch([
    new ProcessPodcast($podcast1),
    new ProcessPodcast($podcast2),
])->then(function (Batch $batch) {
    // All jobs completed
})->catch(function (Batch $batch, \Throwable $e) {
    // Any job failed
})->dispatch();
```

## Unique Jobs

```php
class ProcessPodcast implements ShouldQueue, ShouldBeUnique
{
    public int $uniqueFor = 3600;
    public string $uniqueVia = 'redis';

    public function uniqueId(): string
    {
        return (string) $this->podcast->id;
    }
}
```

## Common Pitfalls

1. **Missing `ShouldQueue`**: Job won't queue without it
2. **Serialization issues**: Always use `SerializesModels` for Eloquent models
3. **Timeout too low**: Jobs may be killed before completion
4. **Memory leaks**: Process large datasets in chunks