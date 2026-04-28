# .env Configuration

Environment variables required for Laravel Reverb.

## Required Variables

```
REVERB_APP_ID=your_app_id
REVERB_APP_KEY=your_app_key
REVERB_APP_SECRET=your_app_secret
```

## Server Configuration

```
REVERB_HOST=0.0.0.0
REVERB_PORT=8080
REVERB_PROTOCOL=http
```

## Protocol Options

- `http` - Plain HTTP WebSocket
- `https` - Secure WebSocket (SSL/TLS)

## Example Configuration

```
REVERB_APP_ID=abc123
REVERB_APP_KEY=def456
REVERB_APP_SECRET=ghi789
REVERB_HOST=0.0.0.0
REVERB_PORT=8080
REVERB_PROTOCOL=http
```

## Production Notes

- Use strong, unique values for all credentials
- Keep `REVERB_APP_SECRET` secure - never commit to git
- Use `https` in production for encrypted connections