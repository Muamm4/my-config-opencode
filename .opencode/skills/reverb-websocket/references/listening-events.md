# Listening Events

How to listen for broadcast events on the frontend.

## Basic Event Listening

```typescript
import Echo from 'laravel-echo';

// Listen on a public channel
Echo.channel(`orders.${orderId}`)
    .listen('OrderStatusUpdated', (e: { status: string; updated_at: string }) => {
        console.log('Status:', e.status);
        console.log('Updated:', e.updated_at);
    });
```

## Listening with UseEffect (React)

```tsx
import { useEffect } from 'react';

interface OrderEvent {
    status: string;
    updated_at: string;
}

function OrderTracker({ orderId }: { orderId: string }) {
    useEffect(() => {
        const channel = Echo.channel(`orders.${orderId}`);

        channel.listen('OrderStatusUpdated', (e: OrderEvent) => {
            console.log('Order status:', e.status);
            // Update UI state here
        });

        // Cleanup on unmount
        return () => {
            Echo.leaveChannel(`orders.${orderId}`);
        };
    }, [orderId]);

    return <div>Tracking order {orderId}...</div>;
}
```

## Multiple Events

```typescript
Echo.channel(`orders.${orderId}`)
    .listen('OrderStatusUpdated', (e) => { /* ... */ })
    .listen('OrderCancelled', (e) => { /* ... */ });
```

## Listening for Specific Events

```typescript
// Wildcard listener
Echo.channel(`orders.${orderId}`)
    .listen('.OrderEvent', (e) => {
        console.log('Event name:', e.event);
        console.log('Data:', e.data);
    });
```

## Stop Listening

```typescript
// Leave specific channel
Echo.leaveChannel(`orders.${orderId}`);

// Leave all channels
Echo.leaveAllChannels();
```

## Event Names

The event name broadcast from Laravel should match exactly:

- Exact match: `'OrderStatusUpdated'`
- With namespace: `'App\\Events\\OrderStatusUpdated'` (Echo handles stripping namespace)