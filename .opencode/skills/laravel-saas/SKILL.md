# laravel-saas

## Identity
You are a Laravel SaaS specialist. Expert in Laravel 13 + React + Inertia + Tailwind + ShadCN + Octane/Swoole stack.

## Stack Overview

Your project at `~/saas` uses:
- **Laravel 13** (PHP 8.3)
- **Laravel Octane** with Swoole
- **React 19** + TypeScript
- **Inertia v2** (@inertiajs/react ^2.0.0)
- **Tailwind 4** (@tailwindcss/vite)
- **ShadCN UI** (@radix-ui/*)
- **Reverb** (WebSocket)
- **Spatie** (Permission + Activitylog)

## Project Structure

```
~/saas/
├── app/                    # Laravel app
├── resources/js/           # React frontend
│   ├── Pages/
│   ├── components/
│   └── layouts/
├── routes/
├── database/
├── composer.json          # Dependencies
└── package.json         # npm deps
```

## Commands

### Development
```bash
cd ~/saas
npm run dev           # Vite dev server
php artisan serve   # Laravel server
```

### With Octane (Swoole)
```bash
# Start octane with swoole
php artisan octane:start --host=0.0.0.0 --port=8000

# Or use dev script
composer dev:octane
```

### Build
```bash
npm run build         # Production build
npm run build:ssr    # With SSR
```

## Octane/Swoole Notes

- Octane boots Laravel once, keeps in memory
- Use `octane:reload` after code changes
- Swoole provides ~10x performance vs FPM
- Reverb for WebSocket (real-time)

## Package.json Key Scripts

| Script | Usage |
|--------|-------|
| `dev` | Dev server + HMR |
| `build` | Production |
| `build:ssr` | SSR build |
| `format` | Prettier |
| `lint` | ESLint |

## Dependencies - Backend

```json
{
  "laravel/framework": "^13.0",
  "laravel/octane": "^2.17",
  "laravel/reverb": "^1.0",
  "spatie/laravel-permission": "*",
  "spatie/laravel-activitylog": "^4.0"
}
```

## Dependencies - Frontend

```json
{
  "@inertiajs/react": "^2.0.0",
  "@radix-ui/react-*": "^1.1.x",
  "tailwindcss": "^4.1.x"
}
```

## Inertia v2 (Breaking Changes)

- `Inertia::render()` → `Inertia::render()` (same)
- Pages in `resources/js/Pages/`
- useForm from `@inertiajs/react`
- Lazy loading supported

## Permission & Roles (Spatie)

```php
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;

// Assign role
$user->assignRole('admin');

// Check permission
$user->can('edit users');
```

## Tailwind 4 Changes

- Uses `@tailwindcss/vite` plugin
- CSS imports: `@import "tailwindcss"`
- No more `@tailwind base/components/utilities`
- Theme in CSS: `@theme { --color-primary: ... }`

## ShadCN Components

```bash
# Add component
npx shadcn@latest add button

# Component path
~/saas/resources/js/components/ui/
```

## Docker Services

The project also uses:
- `evolution-go` - WhatsApp API (port 8080)
- PostgreSQL databases

```bash
# Start Docker
docker-compose up -d

# Check status
docker-compose ps
```

## Reverb (WebSocket)

```bash
# Start reverb
php artisan reverb:start --host=0.0.0.0 --port=9090
```

## .env Configuration

```
# Octane
OCTANE_SERVER=swoole
OCTANE_PORT=8000

# Reverb
REVERB_APP_ID=
REVERB_APP_KEY=
REVERB_APP_SECRET=
REVERB_HOST=

# Database
DB_CONNECTION=pgsql
DB_HOST=localhost
DB_PORT=5432

# Redis (for queue/cache)
REDIS_HOST=127.0.0.1
REDIS_PORT=6379
```

## Trigger Conditions
- When user asks about ~/saas project
- When mentioning Laravel + React + Octane
- When asking about permission/roles
- When needing Tailwind 4 or ShadCN