# Aggregates

Pattern reference for aggregate functions in Eloquent queries.

## Basic Aggregates

```php
// Count all records
$count = User::count();

// Count with conditions
$count = User::where('active', true)->count();

// Sum a column
$total = Order::sum('total');

// Average
$average = Order::avg('total');

// Maximum
$max = Order::max('total');

// Minimum
$min = Order::min('total');
```

## Aggregate with Relationships

```php
// Count related models
$user = User::find(1);
$postCount = $user->posts()->count();

// Sum on relationship
$totalSpent = $user->orders()->sum('total');

// Average on relationship
$avgOrderValue = $user->orders()->avg('total');
```

## Using Select with Aggregates

```php
// Select with aggregate
$users = User::select('role')
    ->selectRaw('COUNT(*) as count')
    ->groupBy('role')
    ->get();

// Sum with grouping
$orders = Order::select('user_id')
    ->selectRaw('SUM(total) as total_spent')
    ->groupBy('user_id')
    ->get();
```

## Having Clauses

```php
// Filter after aggregation
$roles = User::select('role')
    ->selectRaw('COUNT(*) as count')
    ->groupBy('role')
    ->having('count', '>', 5)
    ->get();

// Having with raw
$users = User::select('role')
    ->selectRaw('COUNT(*) as count')
    ->groupBy('role')
    ->havingRaw('count > ?', [5])
    ->get();
```

## Advanced Aggregates

```php
// Distinct count
$count = User::distinct()->count('role');

// Count with where
$activeAdmins = User::where('role', 'admin')
    ->where('active', true)
    ->count();

// Aggregate on date ranges
$salesByDay = Order::selectRaw('DATE(created_at) as date, SUM(total) as daily_total')
    ->groupByRaw('DATE(created_at)')
    ->get();

// Conditional aggregates
$total = Order::where('status', 'completed')->sum('total');
```

## Using Aggregate Methods in Collections

```php
// Collection aggregate methods (after fetching)
$users = User::all();
$count = $users->count();
$names = $users->pluck('name')->implode(', ');

// Sum on collection
$total = $orders->sum('total');

// Average on collection
$avg = $orders->avg('total');
```

## Existence Checks

```php
// Exists (returns boolean, efficient)
$hasUsers = User::where('active', true)->exists();

// Doesnt exist
$noUsers = User::where('active', false)->doesntExist();

// First (or null)
$user = User::where('email', $email)->first();
```