# Dependencies

## Backend (composer.json)

```json
{
  "laravel/framework": "^13.0",
  "laravel/octane": "^2.17",
  "laravel/reverb": "^1.0",
  "spatie/laravel-permission": "*",
  "spatie/laravel-activitylog": "^4.0"
}
```

## Frontend (package.json)

```json
{
  "@inertiajs/react": "^2.0.0",
  "@radix-ui/react-dialog": "^1.1.x",
  "@radix-ui/react-dropdown-menu": "^1.1.x",
  "@radix-ui/react-slot": "^1.1.x",
  "tailwindcss": "^4.1.x",
  "@tailwindcss/vite": "^4.1.x",
  "react": "^19.0.0",
  "react-dom": "^19.0.0",
  "typescript": "^5.0.0"
}
```

## Installing Dependencies

```bash
# Backend
cd ~/saas
composer install
composer require laravel/octane laravel/reverb

# Frontend
cd ~/saas
npm install

# Add ShadCN component
npx shadcn@latest add button
```

## Key Packages

| Package | Purpose |
|---------|---------|
| `laravel/framework` | Core Laravel |
| `laravel/octane` | High-performance server |
| `laravel/reverb` | WebSocket server |
| `spatie/laravel-permission` | RBAC |
| `@inertiajs/react` | Inertia adapter |
| `@radix-ui/react-*` | ShadCN primitives |