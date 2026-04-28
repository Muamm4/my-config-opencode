# Chunking

Pattern reference for processing large datasets efficiently in Eloquent.

## Chunk Method

```php
// Process in batches of 100
User::chunk(100, function ($users) {
    foreach ($users as $user) {
        // Process each user
    }
});

// Chunk by ID (more efficient for sorted data)
User::chunkById(100, function ($users) {
    foreach ($users as $user) {
        // Process
    }
}, 'id');
```

## Cursor Method

```php
// Memory-efficient iteration (generator-based)
foreach (User::cursor() as $user) {
    // Process million+ records
    // Only one query executed, memory stays constant
}

// With filtering
foreach (User::where('active', true)->cursor() as $user) {
    // Process active users only
}
```

## When to Use Each

| Method | Use Case | Memory |
|--------|----------|--------|
| `chunk()` | General batch processing | Moderate |
| `chunkById()` | Large tables with ID | Moderate |
| `cursor()` | Very large datasets | Low |
| `lazy()` | Custom chunk sizes | Low |

## Chunk with Relationships

```php
// Eager load in chunks
User::with('posts')->chunk(100, function ($users) {
    foreach ($users as $user) {
        // $user->posts already loaded
    }
});

// Chunk with constraints
Post::where('published', true)
    ->with(['author', 'comments'])
    ->chunk(50, function ($posts) {
        foreach ($posts as $post) {
            // Process
        }
    });
```

## Chunk Callback Return

```php
// Return false to stop processing
User::chunk(100, function ($users) {
    foreach ($users as $user) {
        if ($user->shouldStopProcessing()) {
            return false; // Stops iteration
        }
        // Process
    }
});
```

## Lazy Collection

```php
// Lazy collection (like cursor but more flexible)
$users = User::where('active', true)->lazy();

// With chunk size
$users = User::lazy(1000);

// Iterate
foreach ($users as $user) {
    // Process
}
```

## Parallel Processing (Laravel 9+)

```php
// Process chunks in parallel
User::chunk(1000, function ($users) {
    dispatch(new ProcessUsersJob($users));
});

// Using batch
$users = User::where('active', true)->get();
$users->chunk(100)->each(function ($chunk) {
    dispatch(new ProcessChunkJob($chunk));
});
```

## Progress Tracking

```php
// Track progress with chunk
$total = User::count();
$processed = 0;

User::chunk(100, function ($users) use (&$processed) {
    foreach ($users as $user) {
        // Process
    }
    $processed += $users->count();
    $progress = ($processed / $total) * 100;
    // Update progress bar or log
});
```

## Common Pitfalls

```php
// ❌ Don't: Update while chunking without care
User::chunk(100, function ($users) {
    foreach ($users as $user) {
        // This can cause issues if query changes
        $user->update(['processed' => true]);
    }
});

// ✅ Do: Use update with chunk
User::where('active', true)
    ->chunk(100, function ($users) {
        $ids = $users->pluck('id');
        User::whereIn('id', $ids)->update(['processed' => true]);
    });
```