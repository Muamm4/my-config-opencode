# Presence Channels

Presence channels track users who are "online" in a channel - perfect for chat rooms, live collaboration, etc.

## Backend Setup

Create a presence channel in a service provider or route:

```php
<?php

use Illuminate\Support\Facades\Broadcast;
use Illuminate\Support\Facades\Auth;

Broadcast::presence('chat-room.{roomId}')->here(function (User $user) {
    // Return user data for presence
    return ['id' => $user->id, 'name' => $user->name];
})->joining(function (User $user, $roomId) {
    // User joined - broadcast notification
    broadcast(new UserJoined($user, $roomId));
})->leaving(function (User $user, $roomId) {
    // User left - broadcast notification
    broadcast(new UserLeft($user, $roomId));
});
```

## Callback Methods

| Callback | When Called |
|----------|-------------|
| `here()` | When a user joins - returns user data for others |
| `joining()` | When a new user joins the channel |
| `leaving()` | When a user leaves the channel |

## React Frontend

```tsx
import Echo from 'laravel-echo';

const roomId = '123';

const channel = Echo.join(`chat-room.${roomId}`);

// Access presence data
channel.presence('chat-room', (joined) => {
    console.log('Users joined:', joined);
});

// Listen for joining/leaving
channel.here((users) => {
    console.log('Currently in room:', users);
});

channel.joining((user) => {
    console.log(`${user.name} joined`);
});

channel.leaving((user) => {
    console.log(`${user.name} left`);
});
```

## Authentication Required

Presence channels require authentication. Ensure:

1. User is authenticated
2. Private channel auth is configured in `BroadcastServiceProvider`