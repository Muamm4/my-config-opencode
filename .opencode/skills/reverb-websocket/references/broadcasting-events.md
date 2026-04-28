# Broadcasting Events

How to create and broadcast events with Laravel Reverb.

## Event Class Structure

Create an event class in `App/Events/`:

```php
<?php

namespace App\Events;

use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class OrderStatusUpdated implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public function __construct(
        public Order $order
    ) {}

    public function broadcastOn(): array
    {
        return [
            new Channel('orders.' . $this->order->id),
        ];
    }

    public function broadcastWith(): array
    {
        return [
            'status' => $this->order->status,
            'updated_at' => $this->order->updated_at->toIso8601String(),
        ];
    }
}
```

## Key Methods

| Method | Purpose |
|--------|---------|
| `broadcastOn()` | Returns array of Channel/PrivateChannel/PresenceChannel objects |
| `broadcastWith()` | Returns data sent to the client |
| `broadcastAs()` | Custom event name for the client |

## Channel Types

### Public Channel

```php
new Channel('orders.' . $order->id)
```

### Private Channel

```php
new PrivateChannel('orders.' . $order->id)
```

### Presence Channel

```php
new PresenceChannel('chat-room.' . $roomId)
```

## Best Practices

- Always serialize only the data needed (`broadcastWith`)
- Use ISO8601 dates for timezone-aware timestamps
- Include only public-safe data in broadcast payloads