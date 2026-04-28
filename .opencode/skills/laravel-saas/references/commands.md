# Commands

## Development Commands

### Standard Development (FPM)
```bash
cd ~/saas

# Frontend (Vite with HMR)
npm run dev

# Backend (Laravel server)
php artisan serve
```

### With Octane (Swoole)
```bash
cd ~/saas

# Start Octane with Swoole
php artisan octane:start --host=0.0.0.0 --port=8000

# Or use custom composer script
composer dev:octane

# Reload after code changes
php artisan octane:reload
```

## Build Commands

```bash
# Production build (frontend)
npm run build

# SSR build
npm run build:ssr
```

## Package.json Key Scripts

| Script | Usage |
|--------|-------|
| `dev` | Dev server + HMR |
| `build` | Production build |
| `build:ssr` | SSR build |
| `format` | Prettier formatting |
| `lint` | ESLint checking |

## Artisan Commands

```bash
# Common Laravel commands
php artisan migrate
php artisan db:seed
php artisan queue:work
php artisan cache:clear

# Octane-specific
php artisan octane:start
php artisan octane:reload
```