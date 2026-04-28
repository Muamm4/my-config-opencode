# Basic Queries

Pattern-oriented reference for fundamental Eloquent query operations.

## Find Operations

```php
// Find by primary key (returns null if not found)
$user = User::find($id);

// Find or throw ModelNotFoundException
$user = User::findOrFail($id);

// Find multiple by IDs
$users = User::find([1, 2, 3]);
```

## First/FirstOr

```php
// First matching record
$user = User::where('email', $email)->first();

// First or fail (throws exception)
$user = User::where('email', $email)->firstOrFail();

// First or create (find or instantiate new)
$user = User::firstOrCreate(['email' => $email]);

// First or new (find or instantiate without saving)
$user = User::firstOrNew(['email' => $email]);
```

## Get Collections

```php
// All records
$users = User::all();

// Get with constraints
$users = User::where('active', true)->get();

// Select specific columns
$users = User::select('id', 'name', 'email')->get();
```

## Pagination

```php
// Standard pagination (includes meta)
$users = User::paginate(15);
// Returns: LengthAwarePaginator with total, current_page, last_page

// Simple pagination (no total count)
$users = User::simplePaginate(15);

// Cursor pagination (efficient for large datasets)
$users = User::cursorPaginate(15);

// Custom pagination
$users = User::paginate(15, ['*'], 'page', $request->page);

// Append query strings to pagination links
$users->appends(['search' => $request->search])->links();
```

## Ordering & Limiting

```php
// Order by
$users = User::orderBy('created_at', 'desc')->get();

// Multiple order by
$users = User::orderBy('role')->orderBy('name')->get();

// Limit
$users = User::limit(10)->get();

// Latest/Oldest (by created_at)
$latest = User::latest()->first();
$oldest = User::oldest()->first();

// Random (use carefully in production)
$random = User::inRandomOrder()->first();