# Eager Loading

Pattern reference for solving N+1 query problems with Eloquent.

## Basic Eager Loading

```php
// Load relationships with parent query
$users = User::with('posts')->get();

// Multiple relationships
$users = User::with(['posts', 'profile'])->get();

// Nested relationships
$users = User::with('posts.comments')->get();

// Deep nesting
$users = User::with('posts.comments.author')->get();
```

## Constraining Eager Loads

```php
// With constraints on relationship
$users = User::with(['posts' => function ($query) {
    $query->where('published', true)
          ->orderBy('created_at', 'desc');
}])->get();

// With count constraint
$users = User::with(['posts' => function ($query) {
    $query->select('id', 'user_id'); // Must include foreign key
}])->get();
```

## Lazy Eager Loading

```php
// Load after parent query
$user = User::find($id);
$user->load('posts');

// Load multiple
$user->load(['posts', 'profile']);

// Conditional load
$user->load(['posts' => function ($query) {
    $query->where('published', true);
}]);

// LoadIf - only if not already loaded
$user->loadIf('posts');
```

## Load Count (Virtual Count)

```php
// Load count (adds posts_count attribute)
$user->loadCount('posts');

// Multiple
$user->loadCount(['posts', 'comments']);

// With constraints
$user->loadCount(['posts' => function ($query) {
    $query->where('published', true);
}]);

// Access
$user->posts_count;
```

## Relationship Exists

```php
// Check if relationship exists without loading
$user->relationLoaded('posts'); // bool

// Check if has any
$user->posts()->exists();
```

## Eager Loading Pitfalls

```php
// ❌ N+1 Problem (AVOID)
$users = User::all();
foreach ($users as $user) {
    echo $user->profile->name; // Each iteration = 1 query
}

// ✅ Solution: Eager Load
$users = User::with('profile')->get();
foreach ($users as $user) {
    echo $user->profile->name; // No additional queries
}

// ✅ Solution: Lazy Eager Load
$users = User::all();
$users->load('profile');
```

## Chunking with Eager Loading

```php
// Chunk large datasets with relationships
User::with('posts')->chunk(100, function ($users) {
    foreach ($users as $user) {
        // Process with loaded relationships
    }
});
```

## Performance Tips

```php
// Select specific columns to reduce data
$users = User::with(['posts:id,title,user_id'])->get();

// Use withCount for counts instead of loading
$users = User::withCount('posts')->get();

// Avoid morphTo eager loading issues - specify types
$comments = Comment::with('commentable')->get();
```