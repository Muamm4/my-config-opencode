# Trigger Events

How to trigger/broadcast events from controllers, jobs, or other parts of your application.

## Basic Usage

```php
// In controller
event(new OrderStatusUpdated($order));
```

## From a Job

```php
// In a queued job
public function handle(): void
{
    event(new OrderStatusUpdated($this->order));
}
```

## From a Listener

```php
// In an event listener
public function handle(OrderUpdated $event): void
{
    event(new OrderStatusUpdated($event->order));
}
```

## Conditional Broadcasting

```php
// Only broadcast if status changed
if ($this->order->isDirty('status')) {
    event(new OrderStatusUpdated($this->order));
}
```

## Broadcasting to Specific Users

```php
use Illuminate\Support\Facades\Broadcast;

Broadcast::toOthers()->toUser($user)->event(new OrderStatusUpdated($order));
```

## Global Event Helper

Laravel provides a global `event()` helper:

```php
event(new MyBroadcastEvent($data));
```

This dispatches the event through Laravel's event system, which handles broadcasting automatically if the event implements `ShouldBroadcast`.