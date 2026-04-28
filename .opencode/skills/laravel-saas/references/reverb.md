# Reverb (WebSocket)

## Overview

Laravel Reverb provides real-time WebSocket capabilities for the Laravel ecosystem.

## Installation

```bash
composer require laravel/reverb
php artisan reverb:install
```

## Starting Reverb

```bash
# Start Reverb server
php artisan reverb:start --host=0.0.0.0 --port=9090

# Or with custom options
php artisan reverb:start --host=0.0.0.0 --port=9090 --ssl
```

## Configuration (.env)

```env
# Reverb
REVERB_APP_ID=your-app-id
REVERB_APP_KEY=your-app-key
REVERB_APP_SECRET=your-app-secret
REVERB_HOST=localhost
REVERB_PORT=9090
REVERB_SCHEME=http
```

## Broadcasting Events

```php
// app/Events/OrderUpdated.php
use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class OrderUpdated implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public function __construct(public Order $order)
    {
    }

    public function broadcastOn(): array
    {
        return [
            new Channel('orders'),
        ];
    }
}
```

## Listening on Frontend

```tsx
import { useChannel, useEvent } from '@inertiajs/react';

export default function OrderStatus() {
    const { channel } = useChannel('orders');
    
    useEvent(channel, 'OrderUpdated', (event) => {
        console.log('Order updated:', event.order);
    });
    
    return <div>Listening for order updates...</div>;
}
```

## Frontend Package

```bash
npm install @inertiajs/reverb
```