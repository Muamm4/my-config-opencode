# Transactions

Pattern reference for database transactions in Laravel.

## Basic Transaction

```php
// Automatic rollback on exception
DB::transaction(function () {
    $user->update(['balance' => $newBalance]);
    $order->update(['status' => 'paid']);
    $payment->update(['processed' => true]);
});

// Transaction with return value
$order = DB::transaction(function () {
    $order = Order::create([...]);
    $this->processPayment($order);
    return $order;
});
```

## Manual Transaction Control

```php
// Begin transaction
DB::beginTransaction();

try {
    $user->update(['balance' => $newBalance]);
    $order->update(['status' => 'paid']);
    
    DB::commit();
} catch (\Exception $e) {
    DB::rollBack();
    throw $e;
}

// Explicit rollback
DB::rollBack();

// Check transaction state
DB::transactionLevel(); // Returns nesting level
```

## Transaction Isolation Levels

```php
// Set isolation level (MySQL example)
DB::statement('SET TRANSACTION ISOLATION LEVEL SERIALIZABLE');

DB::transaction(function () {
    // Critical section
});
```

## Nested Transactions

```php
// Laravel handles nesting automatically
DB::transaction(function () {
    // Outer transaction
    
    DB::transaction(function () {
        // Inner - actually same transaction
        // No actual savepoint in MySQL
    });
});

// Use savepoints for true nesting (PostgreSQL)
DB::transaction(function () {
    DB::statement('SAVEPOINT savepoint1');
    try {
        // Operation
    } catch (\Exception $e) {
        DB::statement('ROLLBACK TO SAVEPOINT savepoint1');
    }
});
```

## Locking

```php
// Lock for update (row-level lock)
$user = User::lockForUpdate()->find($id);

// Shared lock (read lock)
$user = User::sharedLock()->find($id);

// Lock with specific rows
$users = User::where('id', '>', 100)
    ->lockForUpdate()
    ->get();

// Transaction with locking
DB::transaction(function () {
    $user = User::lockForUpdate()->find($id);
    $user->balance -= $amount;
    $user->save();
});
```

## Deadlock Prevention

```php
// Retry on deadlock (Laravel 10+)
DB::transaction(function () {
    // Operation
}, 5); // 5 retries

// With wait timeout
DB::transaction(function () {
    // Operation
}, 3, 1000); // 3 retries, 1000ms timeout
```

## Best Practices

```php
// ✅ Do: Keep transactions short
DB::transaction(function () {
    $order->update(['status' => 'processing']);
    $this->processPayment($order); // Should be fast
    $order->update(['status' => 'completed']);
});

// ❌ Don't: Call external APIs in transactions
DB::transaction(function () {
    $order->save();
    // NEVER do this - external API calls can hang
    // $this->sendEmail($order); 
});
```