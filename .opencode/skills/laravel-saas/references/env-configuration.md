# .env Configuration

## Complete Configuration

```env
# ===========================================
# Application
# ===========================================
APP_NAME=SaaS
APP_ENV=local
APP_KEY=base64:your-app-key-here
APP_DEBUG=true
APP_URL=http://localhost:8000

# ===========================================
# Database (PostgreSQL)
# ===========================================
DB_CONNECTION=pgsql
DB_HOST=localhost
DB_PORT=5432
DB_DATABASE=saas
DB_USERNAME=user
DB_PASSWORD=password

# ===========================================
# Redis (Queue & Cache)
# ===========================================
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

# Queue connection
QUEUE_CONNECTION=redis

# ===========================================
# Laravel Octane
# ===========================================
OCTANE_SERVER=swoole
OCTANE_PORT=8000

# ===========================================
# Laravel Reverb (WebSocket)
# ===========================================
REVERB_APP_ID=your-app-id
REVERB_APP_KEY=your-app-key
REVERB_APP_SECRET=your-app-secret
REVERB_HOST=localhost
REVERB_PORT=9090
REVERB_SCHEME=http

# ===========================================
# Mail
# ===========================================
MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

# ===========================================
# Session
# ===========================================
SESSION_DRIVER=redis
SESSION_LIFETIME=120

# ===========================================
# Filesystem
# ===========================================
FILESYSTEM_DISK=local
```

## Key Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `OCTANE_SERVER` | Octane server driver | `swoole` |
| `OCTANE_PORT` | Port for Octane | `8000` |
| `REVERB_APP_ID` | Reverb app identifier | `abc123` |
| `DB_CONNECTION` | Database driver | `pgsql` |
| `QUEUE_CONNECTION` | Queue driver | `redis` |
| `SESSION_DRIVER` | Session driver | `redis` |

## Generate App Key

```bash
php artisan key:generate
```

## Generate Reverb Keys

```bash
php artisan reverb:publish
```