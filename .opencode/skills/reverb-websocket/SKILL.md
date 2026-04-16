# reverb-websocket

## Identity
You are a WebSocket specialist using Laravel Reverb for real-time features.

## Instructions
- Use Reverb for WebSocket server
- Use broadcasting for real-time events
- Configure Echo on frontend

## Installation

```bash
# Install Reverb
composer require laravel/reverb

# Publish config
php artisan vendor:publish --provider="Laravel\Reverb\ReverbServiceProvider"
```

## .env Configuration

```
REVERB_APP_ID=your_app_id
REVERB_APP_KEY=your_app_key
REVERB_APP_SECRET=your_app_secret
REVERB_HOST=0.0.0.0
REVERB_PORT=8080
REVERB_PROTOCOL=http
```

## Start Reverb Server

```bash
# Basic
php artisan reverb:start

# With host/port
php artisan reverb:start --host=0.0.0.0 --port=9090

# Secure (HTTPS)
php artisan reverb:start --host=0.0.0.0 --port=8080 --protocols=ssl
```

## Broadcasting Events

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

## Trigger Event

```php
// In controller or job
event(new OrderStatusUpdated($order));
```

## Presence Channels (Authentication)

```php
<?php
use Illuminate\Support\Facades\Channel;

Channel::presence('chat-room.{roomId}')->here(function (User $user) {
    // Return user data for presence
    return ['id' => $user->id, 'name' => $user->name];
})->joining(function (User $user) {
    // User joined
    broadcast(new UserJoined($user));
})->leaving(function (User $user) {
    // User left
    broadcast(new UserLeft($user));
});
```

## Frontend Echo Setup

```bash
# Install Laravel Echo
npm install laravel-echo @laravel/echo-react
```

```javascript
// resources/js/bootstrap.js
import Echo from 'laravel-echo';

window.Echo = new Echo({
    broadcaster: 'reverb',
    key: import.meta.env.VITE_REVERB_APP_KEY,
    wsHost: import.meta.env.VITE_REVERB_HOST,
    wsPort: import.meta.env.VITE_REVERB_PORT,
    wssPort: import.meta.env.VITE_REVERB_PORT,
});
```

## Listening Events

```tsx
import { useEffect } from 'react';

useEffect(() => {
    Echo.channel(`orders.${orderId}`)
        .listen('OrderStatusUpdated', (e) => {
            console.log('Status:', e.status);
        });
        
    return () => Echo.leaveChannel(`orders.${orderId}`);
}, [orderId]);
```

## Presence in React

```tsx
const { users } = Echo.join(`chat-room.${roomId}`);

// Users in room
users.presence('chat-room.${roomId}', (joined: () => void) => {
    console.log('Users joined:', joined);
});
```

## Private Channels

```php
// Private channel - need auth
Channel::private('orders.' . $order->id);
```

```javascript
Echo.private(`orders.${orderId}`)
    .listen('OrderStatusUpdated', (e) => {});
```

## Reverb + Redis (Scaling)

```php
// config/reverb.php
'pusher' => [
    'driver' => 'pusher',
    'key' => env('REVERB_APP_KEY'),
    'secret' => env('REVERB_APP_SECRET'),
    'app_id' => env('REVERB_APP_ID'),
    'cluster' => env('PUSHER_APP_CLUSTER'),
    'useTLS' => true,
],

'database' => [
    'driver' => 'database',
    'connection' => 'redis',
    'channel' => 'reverb',
    'queue' => 'default',
],
```

## Trigger Conditions
- When user asks about WebSocket
- When mentioning real-time
- When asking about Reverb
- When needing live updates