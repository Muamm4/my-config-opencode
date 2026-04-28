# Where Clauses

Comprehensive reference for filtering queries with Eloquent.

## Basic Where

```php
// Simple equality
User::where('active', true)->get();

// Custom operator
User::where('age', '>=', 18)->get();
User::where('status', '!=', 'banned')->get();

// Array syntax (AND)
User::where([
    'active' => true,
    'role' => 'admin'
])->get();
```

## Multiple Conditions

```php
// Chained WHERE (AND)
User::where('active', true)
    ->where('role', 'admin')
    ->get();

// OR where
User::where('active', true)
    ->orWhere('role', 'admin')
    ->get();

// Grouping OR conditions
User::where(function ($query) {
    $query->where('role', 'admin')
          ->orWhere('role', 'moderator');
})->get();
```

## WhereIn / WhereNotIn

```php
// Where in array
User::whereIn('id', [1, 2, 3])->get();

// Where not in
User::whereNotIn('id', [4, 5, 6])->get();

// Subquery support
User::whereIn('id', function ($query) {
    $query->select('user_id')->from('posts')->where('published', true);
})->get();
```

## WhereBetween / WhereNotBetween

```php
// Between range (inclusive)
User::whereBetween('created_at', [$from, $to])->get();

// Not between
User::whereNotBetween('age', [18, 65])->get();
```

## Null Checks

```php
// Where null
User::whereNull('deleted_at')->get();

// Where not null
User::whereNotNull('email_verified_at')->get();
```

## Date/Time Clauses

```php
// Specific date
User::whereDate('created_at', '2024-01-01')->get();

// Year, month, day
User::whereYear('created_at', 2024)->get();
User::whereMonth('created_at', 12)->get();
User::whereDay('created_at', 15)->get();

// Time portion
User::whereTime('created_at', '>=', '09:00:00')->get();
```

## Like/Pattern Matching

```php
// Like (case-sensitive)
User::where('name', 'like', '%John%')->get();

// Case-insensitive like
User::whereRaw('LOWER(name) LIKE ?', [strtolower('%john%')])->get();

// ILIKE (PostgreSQL)
User::where('name', 'ilike', '%john%')->get();

// Starts with
User::where('name', 'like', 'John%')->get();

// Ends with
User::where('name', 'like', '%Doe')->get();
```

## WhereHas (Relationship Filtering)

```php
// Users with at least one published post
User::whereHas('posts', function ($query) {
    $query->where('published', true);
})->get();

// With count condition
User::has('posts', '>=', 5)->get();

// With orDoesntHave
User::doesntHave('posts')->get();
```

## Dynamic Where

```php
// Dynamic method (whereColumn)
$user = User::whereEmail($email)->first();

// Multiple parameters
User::whereStatusAndRole('active', 'admin')->get();