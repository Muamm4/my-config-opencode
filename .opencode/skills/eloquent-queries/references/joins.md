# Joins

Pattern reference for SQL joins in Eloquent queries.

## Basic Join

```php
// Inner join
$users = User::select('users.*', 'posts.title as post_title')
    ->join('posts', 'users.id', '=', 'posts.user_id')
    ->get();

// Join with multiple conditions
$users = User::select('users.*', 'orders.total')
    ->join('orders', function ($join) {
        $join->on('users.id', '=', 'orders.user_id')
             ->where('orders.status', 'completed');
    })
    ->get();
```

## Left Join

```php
// Left join (includes all from left table)
$users = User::select('users.*', 'profiles.bio')
    ->leftJoin('profiles', 'users.id', '=', 'profiles.user_id')
    ->get();

// Left join with null check for "no related records"
$usersWithoutProfiles = User::leftJoin('profiles', 'users.id', '=', 'profiles.user_id')
    ->whereNull('profiles.id')
    ->get();
```

## Right Join

```php
// Right join (includes all from right table)
$users = User::select('users.name', 'posts.title')
    ->rightJoin('posts', 'users.id', '=', 'posts.user_id')
    ->get();
```

## Cross Join

```php
// Cartesian product
$matrix = User::crossJoin('roles')->get();
```

## Join with Subquery

```php
// Join with subquery
$users = User::select('users.*')
    ->joinSub(
        Post::select('user_id', DB::raw('COUNT(*) as post_count'))
            ->groupBy('user_id'),
        'post_counts',
        'users.id',
        '=',
        'post_counts.user_id'
    )
    ->get();
```

## Advanced Join Patterns

```php
// Multiple joins
$results = User::select('users.name', 'posts.title', 'comments.body')
    ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
    ->leftJoin('comments', 'posts.id', '=', 'comments.post_id')
    ->where('users.active', true)
    ->get();

// Join with alias
$results = User::select('u.name', 'p.title')
    ->from('users as u')
    ->join('posts as p', 'u.id', '=', 'p.user_id')
    ->get();

// Using where instead of on (different semantics)
$results = User::select('users.*', 'posts.title')
    ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
    ->where('posts.published', true) // Filters result, not join
    ->get();
```

## Join with Aggregation

```php
// Get users with their post counts
$users = User::select('users.*')
    ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
    ->selectRaw('COUNT(posts.id) as post_count')
    ->groupBy('users.id')
    ->get();

// Get orders with customer info
$orders = Order::select('orders.*', 'users.name as customer_name')
    ->join('users', 'orders.user_id', '=', 'users.id')
    ->get();
```

## Union

```php
// Union two queries
$active = User::select('name', 'email')->where('active', true);
$inactive = User::select('name', 'email')->where('active', false);

$all = $active->union($inactive)->get();

// Union with order and limit
$results = User::select('name')
    ->where('role', 'admin')
    ->union(User::select('name')->where('role', 'moderator'))
    ->orderBy('name')
    ->get();
```