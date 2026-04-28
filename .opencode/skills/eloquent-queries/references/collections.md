# Collections

Pattern reference for Laravel Collection methods on Eloquent results.

## Transform Methods

```php
// map - transform each item
$names = $users->map(fn ($user) => $user->name);

// transform - mutate in place (modifies collection)
$users->transform(fn ($user) => {
    $user->full_name = $user->first_name . ' ' . $user->last_name;
    return $user;
});

// flatMap - map and flatten
$allEmails = $users->flatMap(fn ($user) => $user->emails);

// reduce - accumulate to single value
$total = $orders->reduce(fn ($carry, $order) => $carry + $order->total, 0);
```

## Filter Methods

```php
// filter - keep items matching condition
$admins = $users->filter(fn ($user) => $user->role === 'admin');

// reject - remove items matching condition
$nonAdmins = $users->reject(fn ($user) => $user->role === 'admin');

// where - filter by key/value
$activeUsers = $users->where('active', true);

// whereIn - filter by array
$selectedUsers = $users->whereIn('id', [1, 2, 3]);
```

## Search Methods

```php
// first - get first item
$first = $users->first();

// firstWhere - first matching condition
$admin = $users->firstWhere('role', 'admin');

// last - get last item
$last = $users->last();

// find - by key
$user = $users->find($id);

// contains - check if exists
$hasAdmin = $users->contains('role', 'admin');
```

## Aggregation Methods

```php
// count
$count = $users->count();

// sum
$total = $orders->sum('total');

// avg / average
$avg = $orders->avg('total');

// min / max
$min = $orders->min('total');
$max = $orders->max('total');
```

## Grouping Methods

```php
// groupBy - group by key
$byRole = $users->groupBy('role');

// groupBy with callback
$byStatus = $users->groupBy(fn ($user) => $user->status);

// keyBy - use key for items
$byId = $users->keyBy('id');

// keyBy with callback
$byEmail = $users->keyBy(fn ($user) => strtolower($user->email));
```

## Extraction Methods

```php
// pluck - extract single column
$names = $users->pluck('name');

// pluck with key
$nameById = $users->pluck('name', 'id');

// only - get specific keys
$subset = $users->only([1, 2, 3]);

// except - exclude specific keys
$withoutPassword = $users->except(['password', 'token']);
```

## Sorting Methods

```php
// sort - sort by values
$sorted = $users->sortBy('name');

// sortDesc - descending
$sortedDesc = $users->sortByDesc('created_at');

// sortBy with callback
$sorted = $users->sortBy(fn ($user) => strlen($user->name));

// shuffle - random order
$random = $users->shuffle();
```

## Slice & Chunk Methods

```php
// take - get first N items
$first10 = $users->take(10);

// skip - skip first N items
$rest = $users->skip(10);

// chunk - split into chunks
$chunks = $users->chunk(10);

// slice - get portion
$portion = $users->slice(5, 10);
```

## Utility Methods

```php
// isEmpty / isNotEmpty
if ($users->isEmpty()) { /* no results */ }

// unique - remove duplicates
$unique = $users->unique('email');

// values - reset keys
$reset = $users->values();

// toArray / toJson
$array = $users->toArray();
$json = $users->toJson();

// dd/dump for debugging
$users->dd();
$users->dump('name');
```

## Method Chaining

```php
// Complex chain example
$admins = User::all()
    ->filter(fn ($u) => $u->active)
    ->groupBy('department')
    ->map(fn ($group) => $group->pluck('name'))
    ->flatten()
    ->sort()
    ->values();
```