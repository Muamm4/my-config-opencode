# Echo Setup

How to configure Laravel Echo on the frontend to connect to Reverb.

## Install Dependencies

```bash
npm install laravel-echo @laravel/echo-react
```

## Bootstrap Configuration

In `resources/js/bootstrap.js` (or `resources/js/echo.js`):

```javascript
import Echo from 'laravel-echo';
import Pusher from 'pusher-js';

window.Echo = new Echo({
    broadcaster: 'reverb',
    key: import.meta.env.VITE_REVERB_APP_KEY,
    wsHost: import.meta.env.VITE_REVERB_HOST,
    wsPort: import.meta.env.VITE_REVERB_PORT,
    wssPort: import.meta.env.VITE_REVERB_PORT, // Same port for secure
    forceTLS: import.meta.env.VITE_REVERB_PROTOCOL === 'https',
});
```

## Environment Variables

In `.env` or `.env.local`:

```
VITE_REVERB_APP_KEY=your_app_key
VITE_REVERB_HOST=localhost
VITE_REVERB_PORT=8080
VITE_REVERB_PROTOCOL=http
```

## TypeScript Support

Create a type declaration in `resources/js/types/echo.d.ts`:

```typescript
import Echo from 'laravel-echo';

declare global {
    interface Window {
        Echo: Echo;
    }
}
```

## React-Specific Setup

For React, you might want to create a custom hook:

```typescript
// hooks/useEcho.ts
import { useEffect } from 'react';

export function useEcho() {
    return window.Echo;
}
```

## Common Issues

- **Connection refused**: Check host/port matches server config
- **TLS errors**: Ensure `forceTLS` matches protocol (http vs https)
- **CORS errors**: Configure CORS in Reverb or proxy