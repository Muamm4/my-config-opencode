# Private Channels

Private channels require authentication - useful for user-specific or sensitive data.

## Backend Registration

Private channels must be authorized in the backend:

```php
use Illuminate\Support\Facades\Broadcast;

// Simple private channel
Broadcast::private('orders.' . $order->id);
```

Or with authorization callback:

```php
Broadcast::private('orders.' . $order->id)
    ->can(function (User $user, $orderId) {
        // Authorize based on user permissions
        $order = Order::find($orderId);
        return $user->id === $order->user_id || $user->is_admin;
    });
```

## Frontend Usage

```typescript
import Echo from 'laravel-echo';

// Listen on private channel
Echo.private(`orders.${orderId}`)
    .listen('OrderStatusUpdated', (e) => {
        console.log('Status:', e.status);
    });
```

## Authorization Logic

Laravel automatically handles authorization via the `BroadcastServiceProvider` or custom channel routes:

```php
// routes/channels.php
Broadcast::channel('orders.{orderId}', function (User $user, $orderId) {
    return $user->id === Order::find($orderId)->user_id;
});
```

## Common Pattern

```php
// Route for private channel authorization
Broadcast::channel('orders.*', function (User $user, $orderId) {
    // Check if user owns the order
    return Order::where('id', $orderId)
        ->where('user_id', $user->id)
        ->exists();
});
```

## Security Notes

- Always authorize private channel access
- Never expose sensitive data in public channels
- Log unauthorized access attempts for security monitoring